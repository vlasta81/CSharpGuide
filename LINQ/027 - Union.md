
### **`Union`**  
Metoda `Union` slouží ke **kombinaci dvou kolekcí do jedné s odstraněním duplicit**. Je ekvivalentem množinového sjednocení a vrací všechny **unikátní prvky** z obou kolekcí. Je užitečná pro slučování datových sad bez opakujících se záznamů.

---

#### **Základní charakteristika** 

- **Účel**: Sloučit dvě kolekce a odstranit duplicitní prvky.  
- **Návratový typ**: `IEnumerable<T>` (unikátní prvky z obou kolekcí).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  
- **Porovnávání**:  
  - **Výchozí**: Pro hodnotové typy porovnává hodnoty, pro referenční typy **reference**.  
  - **Vlastní porovnávač**: Lze definovat pomocí `IEqualityComparer<T>`.  

---

### **Syntaxe**  

```csharp  
// S výchozím porovnávačem:
IEnumerable<T> sjednocení = kolekce1.Union(kolekce2);

// S vlastním porovnávačem:
IEnumerable<T> sjednocení = kolekce1.Union(kolekce2, new MujComparer());
```

---

### **Klíčové vlastnosti**  

1. **Odstranění duplicit**: Výsledek obsahuje **všechny prvky z obou kolekcí bez opakování**.  
2. **Vlastní porovnávač**:  
   ```csharp  
   class CaseInsensitiveComparer : IEqualityComparer<string>  
   {  
       public bool Equals(string x, string y) => string.Equals(x, y, StringComparison.OrdinalIgnoreCase);  
       public int GetHashCode(string obj) => obj.ToUpper().GetHashCode();  
   }  

   List<string> slova1 = new List<string> { "Ahoj", "Světe" };  
   List<string> slova2 = new List<string> { "AHOJ", "Test" };  
   var sjednocení = slova1.Union(slova2, new CaseInsensitiveComparer()); // { "Ahoj", "Světe", "Test" }  
   ```  
3. **EF Core podpora**: Při použití s `IQueryable` se převádí na SQL `UNION` (automaticky odstraní duplicity).  

---

### **Příklady použití**  

#### **1. Základní použití s čísly**  

```csharp  
List<int> čísla1 = new List<int> { 1, 2, 3 };  
List<int> čísla2 = new List<int> { 3, 4, 5 };  
var sjednocení = čísla1.Union(čísla2); // { 1, 2, 3, 4, 5 }  
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
var sjednocení = produkty1.Union(produkty2, new ProductIdComparer()); // { Id = 1, 2, 3 }  
```  

#### **3. Práce s řetězci (case-sensitive vs. case-insensitive)**  

```csharp  
List<string> jména1 = new List<string> { "Alice", "Bob" };  
List<string> jména2 = new List<string> { "alice", "Charlie" };  
var sjednoceníCaseSensitive = jména1.Union(jména2); // { "Alice", "Bob", "alice", "Charlie" }  
var sjednoceníCaseInsensitive = jména1.Union(jména2, StringComparer.OrdinalIgnoreCase); // { "Alice", "Bob", "Charlie" }  
```  

---

### **Časté chyby a upozornění**  

1. **Záměna s `Concat`**:  
   - `Concat` pouze spojí kolekce **s duplicitami**, zatímco `Union` odstraní duplicity.  
   ```csharp  
   var concatResult = čísla1.Concat(čísla2); // {1, 2, 3, 3, 4, 5}  
   var unionResult = čísla1.Union(čísla2);   // {1, 2, 3, 4, 5}  
   ```  
2. **Ignorování porovnání u referenčních typů**:  
   - Pro objekty je nutné použít vlastní porovnávač, jinak se porovnávají reference.  
3. **Neošetřené `null` hodnoty**:  
   - Pokud jedna kolekce obsahuje `null`, výsledek bude obsahovat jednu `null`.  

---

### **Srovnání s podobnými metodami** 

| Metoda         | Popis                                      |  
|----------------|--------------------------------------------|  
| **`Union`**    | Všechny prvky z obou kolekcí bez duplicit. |  
| **`Concat`**   | Všechny prvky z obou kolekcí s duplicitami. |  
| **`Intersect`**| Pouze prvky společné oběma kolekcím.       |  
| **`Except`**   | Prvky z první kolekce, které nejsou ve druhé. |  

---

### **Tipy pro efektivní použití**  

- **Pro velké kolekce**: Použijte `HashSet<T>` pro rychlejší operace (např. `kolekce2.ToHashSet()`).  
- **Optimalizace v EF Core**:  
  ```csharp  
  var sjednocení = dbContext.Products.Union(lokálníProdukty).ToList(); // Generuje SQL UNION  
  ```  
- **Kombinace s `Select` pro projekci**:  
  ```csharp  
  var sjednoceníIds = produkty1.Select(p => p.Id).Union(produkty2.Select(p => p.Id));  
  ```  

---

### **Shrnutí** 

- `Union` slouží ke **sloučení dvou kolekcí s odstraněním duplicit**.  
- Pro referenční typy je nutné použít **vlastní porovnávač** k porovnání hodnot.  
- Efektivně spolupracuje s **EF Core** (převod na SQL `UNION`).  

📖 **Zdroj**: [Microsoft Docs – Union](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.union)
