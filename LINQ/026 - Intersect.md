
### **`Intersect`**  

Metoda `Intersect` slouží k **nalezení průniku dvou kolekcí** – vrátí prvky, které se **vyskytují v obou kolekcích zároveň**. Je ekvivalentem množinového průniku v matematice a používá se k identifikaci společných datových záznamů.

---

#### **Základní charakteristika**  

- **Účel**: Najít prvky společné pro obě kolekce.  
- **Návratový typ**: `IEnumerable<T>` (unikátní prvky).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  
- **Porovnávání**:  
  - **Výchozí**: Pro hodnotové typy porovnává hodnoty, pro referenční typy **reference**.  
  - **Vlastní porovnávač**: Lze definovat pomocí `IEqualityComparer<T>`.  

---

### **Syntaxe**  

```csharp  
// S výchozím porovnávačem:
IEnumerable<T> průnik = kolekce1.Intersect(kolekce2);

// S vlastním porovnávačem:
IEnumerable<T> průnik = kolekce1.Intersect(kolekce2, new MujComparer());
```

---

### **Klíčové vlastnosti**  

1. **Odstranění duplicit**: Výsledek obsahuje **unikátní prvky** (každý společný prvek je zahrnut jednou).  
2. **Vlastní porovnávač**:  
   ```csharp  
   class CaseInsensitiveComparer : IEqualityComparer<string>  
   {  
       public bool Equals(string x, string y) => string.Equals(x, y, StringComparison.OrdinalIgnoreCase);  
       public int GetHashCode(string obj) => obj.ToUpper().GetHashCode();  
   }  

   List<string> slova1 = new List<string> { "Ahoj", "Světe" };  
   List<string> slova2 = new List<string> { "AHOJ", "test" };  
   var průnik = slova1.Intersect(slova2, new CaseInsensitiveComparer()); // { "Ahoj" }  
   ```  
3. **EF Core podpora**: Při použití s `IQueryable` se převádí na SQL `INTERSECT`.  

---

### **Příklady použití**  

#### **1. Základní použití s čísly**  

```csharp  
List<int> čísla1 = new List<int> { 1, 2, 3, 4 };  
List<int> čísla2 = new List<int> { 3, 4, 5 };  
var průnik = čísla1.Intersect(čísla2); // { 3, 4 }  
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
List<Product> produkty2 = new List<Product> { new Product { Id = 2 }, new Product { Id = 3 } };  
var průnik = produkty1.Intersect(produkty2, new ProductIdComparer()); // { Id = 2 }  
```  

#### **3. Práce s řetězci (case-sensitive vs. case-insensitive)**  

```csharp  
List<string> jména1 = new List<string> { "Alice", "Bob" };  
List<string> jména2 = new List<string> { "alice", "Charlie" };  
var průnikCaseSensitive = jména1.Intersect(jména2); // prázdná kolekce  
var průnikCaseInsensitive = jména1.Intersect(jména2, StringComparer.OrdinalIgnoreCase); // { "Alice" }  
```  

---

### **Časté chyby a upozornění**  

1. **Ignorování porovnání referenčních typů**:  
   ```csharp  
   // Porovnává reference, ne hodnoty!
   var p1 = new Product { Id = 1 };  
   var p2 = new Product { Id = 1 };  
   var průnik = new List<Product> { p1 }.Intersect(new List<Product> { p2 }); // Prázdná kolekce!  
   ```  
2. **Záměna s `Where` a `Contains`**:  
   ```csharp  
   // Méně efektivní než Intersect:
   var neefektivníPrůnik = kolekce1.Where(x => kolekce2.Contains(x));  
   ```  
3. **Neimplementovaný `GetHashCode` v porovnávači**:  
   - Může způsobit nesprávné porovnání.  

---

### **Srovnání s podobnými metodami**  

| Metoda         | Popis                                      |  
|----------------|--------------------------------------------|  
| **`Intersect`** | Prvky společné oběma kolekcím.           |  
| **`Except`**   | Prvky v první kolekci, které nejsou ve druhé. |  
| **`Union`**    | Všechny prvky z obou kolekcí (bez duplicit). |  

---

### **Tipy pro efektivní použití**  

- **Pro velké kolekce**: Použijte `HashSet<T>` pro rychlejší vyhledávání.  
- **Optimalizace v EF Core**:  
  ```csharp  
  var průnik = dbContext.Products.Intersect(lokálníProdukty).ToList(); // Generuje SQL INTERSECT  
  ```  
- **Kombinace s `Select` pro projekci**:  
  ```csharp  
  var společnáIds = produkty1.Select(p => p.Id).Intersect(produkty2.Select(p => p.Id));  
  ```  

---

### **Shrnutí**  

- `Intersect` vrací **unikátní prvky společné pro obě kolekce**.  
- Pro referenční typy je nutné použít **vlastní porovnávač** k porovnání hodnot.  
- Efektivně spolupracuje s **EF Core** (převod na SQL `INTERSECT`).  

📖 **Zdroj**: [Microsoft Docs – Intersect](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.intersect)
