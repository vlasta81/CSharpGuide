
### **`Except`**  

Metoda `Except` slouží k **nalezení rozdílu dvou kolekcí** – vrátí prvky z první kolekce, které se **nevyskytují ve druhé kolekci**. Je ekvivalentem množinového rozdílu v matematice a je užitečná pro porovnávání datových sad.

---

#### **Základní charakteristika**  

- **Účel**: Najít prvky, které jsou v první kolekci, ale ne ve druhé.  
- **Návratový typ**: `IEnumerable<T>` (unikátní prvky).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  
- **Porovnávání**:  
  - **Výchozí**: Pro hodnotové typy porovnává hodnoty, pro referenční typy **reference**.  
  - **Vlastní porovnávač**: Lze definovat pomocí `IEqualityComparer<T>`.  

---

### **Syntaxe**  

```csharp  
// S výchozím porovnávačem:
IEnumerable<T> rozdíl = kolekce1.Except(kolekce2);

// S vlastním porovnávačem:
IEnumerable<T> rozdíl = kolekce1.Except(kolekce2, new MujComparer());
```

---

### **Klíčové vlastnosti**  

1. **Odstranění duplicit**: Výsledek obsahuje **unikátní prvky** (duplicity z první kolekce jsou odstraněny).  
2. **Vlastní porovnávač**:  
   ```csharp  
   class CaseInsensitiveComparer : IEqualityComparer<string>  
   {  
       public bool Equals(string x, string y) => string.Equals(x, y, StringComparison.OrdinalIgnoreCase);  
       public int GetHashCode(string obj) => obj.ToUpper().GetHashCode();  
   }  

   List<string> slova1 = new List<string> { "Ahoj", "světe" };  
   List<string> slova2 = new List<string> { "AHOJ" };  
   var rozdíl = slova1.Except(slova2, new CaseInsensitiveComparer()); // { "světe" }  
   ```  
3. **EF Core podpora**: Při použití s `IQueryable` se převádí na SQL `EXCEPT`.  

---

### **Příklady použití**  

#### **1. Základní použití s čísly**  

```csharp  
List<int> čísla1 = new List<int> { 1, 2, 3, 4 };  
List<int> čísla2 = new List<int> { 3, 4, 5 };  
var rozdíl = čísla1.Except(čísla2); // { 1, 2 }  
```  

#### **2. Porovnání objektů s vlastním porovnávačem**  

```csharp  
class Product { public int Id { get; set; } }  

class ProductIdComparer : IEqualityComparer<Product>  
{  
    public bool Equals(Product x, Product y) => x.Id == y.Id;  
    public int GetHashCode(Product p) => p.Id.GetHashCode();  
}  

List<Product> produkty1 = new List<Product> { new Product { Id = 1 }, new Product { Id = 2 } };  
List<Product> produkty2 = new List<Product> { new Product { Id = 2 } };  
var rozdíl = produkty1.Except(produkty2, new ProductIdComparer()); // { Id = 1 }  
```  

#### **3. Práce s řetězci (case-sensitive vs. case-insensitive)**  

```csharp  
List<string> jména1 = new List<string> { "Alice", "Bob" };  
List<string> jména2 = new List<string> { "alice" };  
var rozdíl = jména1.Except(jména2); // { "Alice", "Bob" } (case-sensitive)  
var rozdílIgnoreCase = jména1.Except(jména2, StringComparer.OrdinalIgnoreCase); // { "Bob" }  
```  

---

### **Časté chyby a upozornění**  

1. **Ignorování porovnání referenčních typů**:  
   ```csharp  
   // Porovnává reference, ne hodnoty!
   var p1 = new Product { Id = 1 };  
   var p2 = new Product { Id = 1 };  
   var rozdíl = new List<Product> { p1 }.Except(new List<Product> { p2 }); // Vrátí p1!  
   ```  
2. **Záměna s `Where`**:  
   - `Except` odstraňuje duplicity a porovnává celé prvky, zatímco `Where` filtruje podle podmínky.  
3. **Neimplementovaný `GetHashCode` v porovnávači**:  
   - Může způsobit nesprávné porovnání.  

---

### **Srovnání s podobnými metodami**  

| Metoda       | Popis                                      |  
|--------------|--------------------------------------------|  
| **`Except`** | Prvky v první kolekci, které nejsve druhé. |  
| **`Intersect`** | Prvky společné oběma kolekcím.          |  
| **`Union`**  | Všechny prvky z obou kolekcí (bez duplicit). |  

---

### **Tipy pro efektivní použití**  

- **Pro velké kolekce**: Použijte `HashSet<T>` pro rychlejší vyhledávání (např. `kolekce2.ToHashSet()`).  
- **Optimalizace v EF Core**:  
  ```csharp  
  var rozdíl = dbContext.Products.Except(lokálníProdukty).ToList(); // Generuje SQL EXCEPT  
  ```  
- **Kombinace s `Select` pro projekci**:  
  ```csharp  
  var rozdílIds = produkty1.Select(p => p.Id).Except(produkty2.Select(p => p.Id));  
  ```  

---

### **Shrnutí**  

- `Except` vrací **unikátní prvky z první kolekce, které nejsou ve druhé**.  
- Pro referenční typy je nutné použít **vlastní porovnávač** k porovnání hodnot.  
- Efektivně spolupracuje s **EF Core** a velkými datovými sadami.  

📖 **Zdroj**: [Microsoft Docs – Except](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.except)
