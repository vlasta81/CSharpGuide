
### **`Select`**  

`Select` je jeden z **nejpoužívanějších operátorů** v LINQ, který slouží k **transformaci (projekci) dat**. Umožňuje převést každý prvek v kolekci do nové podoby podle definovaného výrazu. Je ekvivalentní `SELECT` v SQL, ale s mnohem širšími možnostmi díky integraci do jazyka C#.

---

#### **Základní koncept**  

- **Účel**: Transformace prvků kolekce (např. výběr konkrétní vlastnosti, vytvoření nového objektu).  
- **Návratový typ**: `IEnumerable<TResult>` (nebo `IQueryable<TResult>` pro externí zdroje dat).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  

---

#### **Syntaxe**  

**1. Method Syntax**:  
```csharp
IEnumerable<TResult> result = kolekce.Select(prvek => výraz);
```

**2. Query Syntax**:  
```csharp
IEnumerable<TResult> result = from prvek in kolekce
                              select výraz;
```

---

#### **Příklady použití**  

**1. Výběr jedné vlastnosti**  
```csharp
List<Person> people = GetPeople();
var names = people.Select(p => p.Name); // Vrátí IEnumerable<string>
```

**2. Vytvoření anonymního typu**  
```csharp
var personsInfo = people.Select(p => new { 
    p.Name, 
    BirthYear = DateTime.Now.Year - p.Age 
});
```

**3. Transformace dat**  
```csharp
List<int> temperaturesCelsius = new List<int> { 0, 25, 100 };
var temperaturesFahrenheit = temperaturesCelsius
    .Select(c => c * 9 / 5 + 32); // { 32, 77, 212 }
```

**4. Použití indexu prvku**  
```csharp
List<string> fruits = new List<string> { "Apple", "Banana", "Cherry" };
var indexedFruits = fruits.Select((fruit, index) => $"{index + 1}. {fruit}");
// Výsledek: "1. Apple", "2. Banana", "3. Cherry"
```

---

#### **Pokročilé techniky**  

**1. Složená projekce**  
Kombinace dat z více zdrojů:  
```csharp
var peopleWithIds = people.Select((p, index) => new {
    Id = "P" + index.ToString("D3"),
    p.Name
});
```

**2. Vnořené projekce**  
Transformace vnořených objektů:  
```csharp
var departments = company.Select(d => new {
    d.Name,
    Employees = d.Employees.Select(e => e.Name)
});
```

**3. Použití s `IQueryable` (Entity Framework)**  
Optimalizace databázového dotazu:  
```csharp
var query = dbContext.Products
    .Where(p => p.Price > 100)
    .Select(p => new { p.Name, p.Price })
    .ToList(); // Generuje SQL pouze pro sloupce Name a Price
```

---

#### **Rozdíl mezi `Select` a `SelectMany`**  

- **`Select`**: Vrací kolekci transformovaných prvků (1:1).  
- **`SelectMany`**: Slouží k „zploštění“ vnořených kolekcí (1:N).  
  ```csharp
  List<List<int>> numbers = new List<List<int>> { new List<int> {1, 2}, new List<int> {3} };
  var flattened = numbers.SelectMany(innerList => innerList); // {1, 2, 3}
  ```

---

#### **Časté chyby a upozornění**  

1. **Nezachytávání `null` hodnot**:  
   ```csharp
   // Pokud je např. person.Name null, výsledek bude obsahovat null!
   var names = people.Select(p => p.Name); 
   ```
   - Řešení: Použít `Where(p => p.Name != null)` před `Select`.

2. **Nadbytečné volání `Select`**:  
   ```csharp
   // Zbytečné – Where a OrderBy pracují přímo s Person
   people.Select(p => p).Where(p => p.Age > 18).OrderBy(p => p.Name);
   ```

---

#### **Výhody použití `Select`**  

- **Data shaping**: Výběr pouze potřebných vlastností (vhodné pro DTO nebo API odpovědi).  
- **Optimalizace dotazů**: U externích zdrojů (SQL) se přenáší pouze požadovaná data.  
- **Čitelnost kódu**: Explicitní deklarace transformace.  

---

### **Shrnutí**  

- `Select` slouží k **transformaci prvků** v kolekci.  
- Podporuje **anonymní typy**, **indexy** a **komplexní projekce**.  
- Efektivně pracuje s `IQueryable` pro optimalizaci databázových dotazů.  
- Rozlišujte mezi `Select` (1:1) a `SelectMany` (1:N).  

📖 **Zdroj**: [Microsoft Docs – Select](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.select)
