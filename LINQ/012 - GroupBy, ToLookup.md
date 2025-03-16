
### **`GroupBy` a `ToLookup`**  

Tyto metody slouží k **seskupování prvků kolekce podle klíče**. Umožňují organizovat data do logických skupin, podobně jako `GROUP BY` v SQL, ale s větší flexibilitou díky integraci do jazyka C#.

---

#### **Základní charakteristika**  

| Vlastnost               | **`GroupBy`**                          | **`ToLookup`**                        |
|-------------------------|----------------------------------------|----------------------------------------|
| **Účel**                | Seskupí prvky podle klíče (deferred execution). | Vytvoří **lookup tabulku** (immediate execution). |
| **Návratový typ**       | `IEnumerable<IGrouping<TKey, TElement>>` | `ILookup<TKey, TElement>`             |
| **Vyhodnocení**         | **Odložené** (až při iteraci).         | **Okamžité** (dotaz se vykoná ihned).  |
| **Optimalizace**        | Vhodné pro práci s DB (EF Core).       | Pouze pro **kolekce v paměti**.        |
| **Prázdné skupiny**     | Nevytváří prázdné skupiny.             | Nevytváří prázdné skupiny.             |

---

### **Syntaxe**  

**1. `GroupBy` (Method Syntax)**:  
```csharp
var groups = kolekce.GroupBy(
    keySelector: x => x.Klíč,          // Výběr klíče
    elementSelector: x => x.Hodnota,    // Výběr prvků ve skupině (volitelné)
    resultSelector: (key, elements) => new { Key = key, Items = elements } // Transformace výsledku (volitelné)
);
```

**2. `GroupBy` (Query Syntax)**:  
```csharp
var groups = from prvek in kolekce
             group prvek by prvek.Klíč into skupina
             select new { Key = skupina.Key, Items = skupina };
```

**3. `ToLookup`**:  
```csharp
ILookup<TKey, TElement> lookup = kolekce.ToLookup(
    keySelector: x => x.Klíč,          // Výběr klíče
    elementSelector: x => x.Hodnota    // Výběr prvků (volitelné)
);
```

---

### **Klíčové vlastnosti**  

1. **Vlastní komparátor**:  
   - Obě metody podporují vlastní `IEqualityComparer<T>` pro porovnávání klíčů.  
   ```csharp
   var caseInsensitiveGroups = words.GroupBy(w => w, StringComparer.OrdinalIgnoreCase);
   ```

2. **Práce s objekty**:  
   ```csharp
   class Person { public string City { get; set; } public int Age { get; set; } }
   var peopleByCity = people.GroupBy(p => p.City);
   ```

3. **Transformace výsledků**:  
   ```csharp
   var stats = products.GroupBy(
       p => p.Category,
       (key, items) => new { Category = key, Total = items.Sum(p => p.Price) }
   );
   ```

---

### **Příklady použití**  

#### **1. `GroupBy` – seskupení osob podle města**  

```csharp
var groups = people.GroupBy(p => p.City);
foreach (var group in groups)
{
    Console.WriteLine($"Město: {group.Key}, Počet: {group.Count()}");
    foreach (var person in group)
    {
        Console.WriteLine($"- {person.Name}");
    }
}
```

#### **2. `ToLookup` – rychlý přístup ke skupinám**  

```csharp
var lookup = products.ToLookup(p => p.Category);
var electronics = lookup["Electronics"]; // Všechny produkty v kategorii "Electronics"
```

#### **3. Složený klíč (anonymní typ)**  

```csharp
var ordersByCustomerAndYear = orders.GroupBy(o => new { o.CustomerId, o.OrderDate.Year });
```

---

### **Rozdíly mezi `GroupBy` a `ToLookup`**  

| Kritérium               | **`GroupBy`**                          | **`ToLookup`**                        |
|-------------------------|----------------------------------------|----------------------------------------|
| **Vyhodnocení**         | Lazy – data se načítají při iteraci.   | Eager – data se načítají okamžitě.     |
| **Vhodné pro**          | Velké datové sady (DB, streamování).   | Malé až střední kolekce v paměti.      |
| **Výkon při opakovaném přístupu** | Méně efektivní (opakované vyhodnocení). | Efektivní (data jsou cachovaná).       |
| **Podpora v EF Core**   | Ano (převádí se na SQL `GROUP BY`).    | Ne.                                    |

---

### **Časté chyby**  

1. **Použití `ToLookup` s EF Core**:  
   ```csharp
   // Chyba: ToLookup nelze použít na IQueryable (pouze na IEnumerable)!
   var invalid = dbContext.Products.ToLookup(p => p.Category);
   ```

2. **Modifikace zdrojové kolekce**:  
   ```csharp
   var lookup = list.ToLookup(x => x);
   list.Add(100); // Lookup se neaktualizuje – obsahuje stará data!
   ```

3. **Ignorování `null` klíčů**:  
   ```csharp
   var groups = kolekce.GroupBy(x => x.Klíč); // Pokud je Klíč null, skupina bude mít klíč null.
   ```

---

### **Tipy pro efektivní použití**  

- **`GroupBy` v dotazech na DB**:  
  ```csharp
  var ordersByCustomer = dbContext.Orders
      .GroupBy(o => o.CustomerId)
      .Select(g => new { CustomerId = g.Key, Total = g.Sum(o => o.Amount) })
      .ToList();
  ```
- **Optimalizace `ToLookup`**:  
  - Ideální pro **rychlý přístup ke skupinám** (např. cache).  
- **Kombinace s `Where`**:  
  ```csharp
  var activeGroups = people.GroupBy(p => p.City).Where(g => g.Any(p => p.IsActive));
  ```

---

### **Shrnutí**  

- **`GroupBy`** – pro **odložené seskupování** (DB, streamování).  
- **`ToLookup`** – pro **okamžité vytvoření lookup tabulky** (paměťové kolekce).  
- Oboje umožňuje práci s **vlastními klíči**, **komparátory** a **transformacemi výsledků**.  

📖 **Zdroj**: [Microsoft Docs – GroupBy](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.groupby) | [ToLookup](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.tolookup)
