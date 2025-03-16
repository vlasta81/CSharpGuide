
### **`ThenBy` a `ThenByDescending`**  

Metody `ThenBy` a `ThenByDescending` slouží k **sekundárnímu řazení** prvků kolekce po primárním řazení pomocí `OrderBy` nebo `OrderByDescending`. Umožňují definovat **víceúrovňové řazení** (např. nejprve podle města, poté podle příjmení).

---

#### **Základní charakteristika**  

- **Účel**:  
  - `ThenBy`: Seřadí prvky **vzestupně** podle sekundárního klíče.  
  - `ThenByDescending`: Seřadí prvky **sestupně** podle sekundárního klíče.  
- **Návratový typ**: `IOrderedEnumerable<T>` (umožňuje další řazení pomocí dalších `ThenBy`/`ThenByDescending`).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  
- **Stabilní řazení**: Zachovává pořadí prvků se stejnou hodnotou klíče z předchozího řazení.  

---

### **Syntaxe**  

**1. Method Syntax**:  
```csharp
var sorted = kolekce
    .OrderBy(x => x.PrimárníKlíč)
    .ThenBy(x => x.SekundárníKlíč)
    .ThenByDescending(x => x.TřetíKlíč);
```

**2. Query Syntax**:  
```csharp
var sortedQuery = from prvek in kolekce
                  orderby prvek.PrimárníKlíč, prvek.SekundárníKlíč descending
                  select prvek;
```

---

### **Klíčové vlastnosti**  

1. **Víceúrovňové řazení**:  
   - Lze kombinovat libovolný počet `ThenBy`/`ThenByDescending`.  
   ```csharp
   var employees = dbContext.Employees
       .OrderBy(e => e.Department)
       .ThenBy(e => e.LastName)
       .ThenByDescending(e => e.Salary);
   ```

2. **Vlastní komparátor**:  
   - Stejně jako u `OrderBy` lze použít `IComparer<T>`.  
   ```csharp
   var sorted = people
       .OrderBy(p => p.City)
       .ThenBy(p => p.Name, new CaseInsensitiveComparer());
   ```

3. **Podpora pro `IQueryable` (EF Core)**:  
   - Převádí se na SQL `ORDER BY` s více sloupci.  
   ```csharp
   // Generuje SQL: ORDER BY Department, LastName DESC
   var query = dbContext.Employees
       .OrderBy(e => e.Department)
       .ThenByDescending(e => e.LastName);
   ```

---

### **Příklady použití**  

#### **1. Víceúrovňové řazení objektů**  

```csharp
class Student { 
    public string Name { get; set; }
    public int Grade { get; set; }
    public DateTime BirthDate { get; set; }
}

List<Student> students = new List<Student> { /* ... */ };

var sortedStudents = students
    .OrderBy(s => s.Grade)          // Primární řazení podle známky
    .ThenBy(s => s.BirthDate)       // Sekundární řazení podle data narození
    .ThenByDescending(s => s.Name); // Terciární řazení podle jména (sestupně)
```

#### **2. Řazení s vlastním komparátorem**  

```csharp
// Vlastní komparátor pro řazení podle délky řetězce
class LengthComparer : IComparer<string>
{
    public int Compare(string x, string y) => x.Length.CompareTo(y.Length);
}

var words = new List<string> { "apple", "kiwi", "banana", "grape" };
var sortedWords = words
    .OrderBy(w => w.Length)
    .ThenBy(w => w, new LengthComparer()); // Seřadí podle délky a poté lexikograficky
```

#### **3. Kombinace s `OrderByDescending`**  

```csharp
var products = productList
    .OrderByDescending(p => p.Category) // Primární řazení (sestupně podle kategorie)
    .ThenBy(p => p.Price);             // Sekundární řazení (vzestupně podle ceny)
```

---

### **Časté chyby a upozornění**  

1. **Použití `ThenBy` bez předchozího `OrderBy`**:  
   ```csharp
   // Chyba: ThenBy nelze použít bez OrderBy/OrderByDescending!
   var invalid = kolekce.ThenBy(x => x.Vlastnost);
   ```

2. **Ignorování `null` hodnot**:  
   ```csharp
   // Pokud je vlastnost null, může dojít k chybě:
   var risky = people.OrderBy(p => p.City).ThenBy(p => p.Age);
   ```

3. **Optimalizace u velkých kolekcí**:  
   - Víceúrovňové řazení může být náročné na paměť pro kolekce s miliony prvků.  

---

### **Shrnutí**  

- `ThenBy` a `ThenByDescending` slouží k **sekundárnímu a terciárnímu řazení** po primárním `OrderBy`/`OrderByDescending`.  
- Umožňují **komplexní víceúrovňové řazení** s libovolným počtem klíčů.  
- Zachovávají **stabilní řazení** – prvky se stejnými klíči zůstávají v původním pořadí z předchozí úrovně.  
- Efektivně spolupracují s **databázemi** (EF Core) díky převodu na SQL `ORDER BY`.  

📖 **Zdroj**: [Microsoft Docs – ThenBy](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.thenby) | [ThenByDescending](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.thenbydescending)
