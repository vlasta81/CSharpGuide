
### **`OrderBy`, `OrderByDescending`** 

Metody `OrderBy` a `OrderByDescending` slouží k **řazení prvků kolekce** podle zadaného klíče. Jsou klíčové pro organizaci dat a pracují s různými datovými typy, včetně vlastních objektů.  

---

#### **Základní charakteristika**  

- **Účel**:  
  - `OrderBy`: Seřadí prvky **vzestupně** podle klíče.  
  - `OrderByDescending`: Seřadí prvky **sestupně** podle klíče.  
- **Návratový typ**: `IOrderedEnumerable<T>` nebo `IOrderedQueryable<T>` (pro další řazení pomocí `ThenBy`/`ThenByDescending`).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  
- **Stabilní řazení**: Prvky se stejnou hodnotou klíče zachovávají původní pořadí.  

---

### **Syntaxe**  

**1. Method Syntax**:  
```csharp
// Vzestupné řazení:
var sortedAsc = kolekce.OrderBy(x => x.Vlastnost);

// Sestupné řazení:
var sortedDesc = kolekce.OrderByDescending(x => x.Vlastnost);
```

**2. Query Syntax**:  
```csharp
var sortedQuery = from prvek in kolekce
                  orderby prvek.Vlastnost [ascending/descending]
                  select prvek;
```

---

### **Klíčové vlastnosti**  

1. **Vlastní komparátor**:  
   - Lze definovat vlastní logiku porovnání pomocí `IComparer<T>`.  
   ```csharp
   class CaseInsensitiveComparer : IComparer<string>
   {
       public int Compare(string x, string y) => string.Compare(x, y, StringComparison.OrdinalIgnoreCase);
   }

   var sorted = words.OrderBy(w => w, new CaseInsensitiveComparer());
   ```

2. **Víceúrovňové řazení**:  
   - Kombinace s `ThenBy`/`ThenByDescending` pro sekundární řazení.  
   ```csharp
   var multiSorted = people
       .OrderBy(p => p.Město)
       .ThenByDescending(p => p.Věk);
   ```

3. **Podpora pro `IQueryable` (EF Core)**:  
   - Převádí se na SQL `ORDER BY` (optimalizace na straně databáze).  
   ```csharp
   var products = dbContext.Products
       .OrderBy(p => p.Price)
       .ToList(); // Generuje SQL ORDER BY Price
   ```

---

### **Příklady použití**  

#### **1. Řazení primitivních typů**  

```csharp
List<int> numbers = new List<int> { 5, 1, 9, 3 };
var sortedNumbers = numbers.OrderBy(n => n); // { 1, 3, 5, 9 }
```

#### **2. Řazení objektů podle vlastnosti**  

```csharp
class Person { public string Jméno { get; set; } public int Věk { get; set; } }
List<Person> people = new List<Person> { /* ... */ };

var sortedByAge = people.OrderBy(p => p.Věk);
```

#### **3. Case-insensitive řazení řetězců**  

```csharp
List<string> names = new List<string> { "Anna", "alice", "Bob" };
var sortedNames = names.OrderBy(n => n, StringComparer.OrdinalIgnoreCase); // "alice", "Anna", "Bob"
```

#### **4. Složené řazení**  

```csharp
var sortedPeople = people
    .OrderBy(p => p.Město)
    .ThenBy(p => p.Příjmení)
    .ThenByDescending(p => p.Věk);
```

---

### **Časté chyby a upozornění**  

1. **Ignorování `null` hodnot**:  
   ```csharp
   // Pokud je vlastnost null, může dojít k chybě:
   var risky = people.OrderBy(p => p.Jméno.Length); // NullReferenceException
   ```

2. **Nesprávné použití s anonymními typy**:  
   ```csharp
   // Anonymní typy nelze přímo řadit bez definice klíče:
   var invalid = people.Select(p => new { p.Jméno }).OrderBy(p => p);
   ```

3. **Optimalizace u velkých kolekcí**:  
   - Řazení v paměti může být náročné pro kolekce s miliony prvků.  

---

### **Tipy pro efektivní použití**  

- **Řazení před projekcí**:  
  ```csharp
  // Efektivnější:
  var result = people.OrderBy(p => p.Věk).Select(p => p.Jméno);
  ```
- **Použití `AsEnumerable()` pro lokální řazení**:  
  ```csharp
  // Řazení na straně klienta po načtení dat:
  var data = dbContext.Products.AsEnumerable().OrderBy(p => p.Name);
  ```
- **Výchozí řazení u prázdných kolekcí**:  
  - Metody nevyvolávají výjimku pro prázdné kolekce – vracejí prázdnou sekvenci.  

---

### **Shrnutí**  

- `OrderBy` a `OrderByDescending` slouží k **primárnímu řazení** podle klíče.  
- Pro **víceúrovňové řazení** použijte `ThenBy`/`ThenByDescending`.  
- Podporují **vlastní komparátory** a **integraci s databázemi** (EF Core).  
- Stabilní řazení zajišťuje zachování pořadí prvků se stejným klíčem.  

📖 **Zdroj**: [Microsoft Docs – OrderBy](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.orderby) | [OrderByDescending](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.orderbydescending)
