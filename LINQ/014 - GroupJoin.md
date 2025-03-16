
### **`GroupJoin`**  

Metoda `GroupJoin` slouží k **hierarchickému propojení dvou kolekcí** na základě společného klíče, podobně jako `LEFT OUTER JOIN` v SQL. Umožňuje seskupit prvky z druhé kolekce (např. objednávky) pod odpovídající prvek z první kolekce (např. zákazníky).

---

#### **Základní charakteristika**  

- **Účel**: Vytvořit hierarchická data – každý prvek z první kolekce má kolekci odpovídajících prvků z druhé kolekce.  
- **Typ spojení**: **Left outer join** (výsledek obsahuje všechny prvky z první kolekce, i bez odpovídajících záznamů).  
- **Návratový typ**: `IEnumerable<TResult>` (nebo `IQueryable<TResult>` pro externí zdroje dat).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  

---

### **Syntaxe**  

**1. Method Syntax**:  
```csharp
var result = outerCollection.GroupJoin(
    innerCollection,                     // Druhá kolekce
    outerKeySelector: outer => outer.Key, // Klíč z první kolekce
    innerKeySelector: inner => inner.Key, // Klíč z druhé kolekce
    resultSelector: (outer, innerGroup) => new { outer.Prop, Items = innerGroup } // Výsledný objekt
);
```

**2. Query Syntax**:  
```csharp
var result = from outer in outerCollection
             join inner in innerCollection
             on outer.Key equals inner.Key into innerGroup
             select new { outer.Prop, Items = innerGroup };
```

---

### **Klíčové vlastnosti**  

1. **Vlastní transformace výsledku**:  
   ```csharp
   var customersWithOrders = customers.GroupJoin(
       orders,
       c => c.Id,
       o => o.CustomerId,
       (c, orders) => new { 
           Customer = c.Name, 
           TotalOrders = orders.Sum(o => o.Amount) 
       }
   );
   ```

2. **Práce s prázdnými skupinami**:  
   - Pokud neexistují odpovídající prvky, `innerGroup` je prázdná kolekce.  
   ```csharp
   var result = customers.GroupJoin(
       orders,
       c => c.Id,
       o => o.CustomerId,
       (c, orders) => new { 
           c.Name, 
           OrderCount = orders.Count() // 0, pokud nejsou objednávky
       }
   );
   ```

3. **Použití s `DefaultIfEmpty` pro plochý výsledek**:  
   ```csharp
   // Transformace na plochý seznam (LEFT JOIN):
   var flatResult = customers
       .GroupJoin(
           orders,
           c => c.Id,
           o => o.CustomerId,
           (c, orders) => new { c, orders }
       )
       .SelectMany(x => x.orders.DefaultIfEmpty(), (c, o) => new { c.c.Name, Order = o?.Amount });
   ```

4. **Podpora pro EF Core**:  
   - Převádí se na SQL `LEFT JOIN`.  
   ```csharp
   var query = dbContext.Customers
       .GroupJoin(
           dbContext.Orders,
           c => c.Id,
           o => o.CustomerId,
           (c, orders) => new { c.Name, Orders = orders }
       )
       .ToList(); // Generuje SQL LEFT JOIN
   ```

---

### **Příklady použití**  

#### **1. Seskupení objednávek podle zákazníka**  

```csharp
class Customer { public int Id; public string Name; }
class Order { public int CustomerId; public decimal Amount; }

List<Customer> customers = new List<Customer> { /* ... */ };
List<Order> orders = new List<Order> { /* ... */ };

var customersWithOrders = customers.GroupJoin(
    orders,
    c => c.Id,
    o => o.CustomerId,
    (c, orders) => new { 
        Customer = c.Name, 
        Total = orders.Sum(o => o.Amount) 
    }
);
```

#### **2. Víceúrovňové seskupení s anonymními typy**  

```csharp
var employeesWithProjects = employees.GroupJoin(
    projects,
    e => new { e.DepartmentId, e.Role },
    p => new { p.DepartmentId, p.Role },
    (e, projects) => new { e.Name, Projects = projects }
);
```

#### **3. Kombinace s `SelectMany` pro plochý výstup**  

```csharp
var flatList = customers
    .GroupJoin(
        orders,
        c => c.Id,
        o => o.CustomerId,
        (c, orders) => new { c, orders }
    )
    .SelectMany(
        x => x.orders.DefaultIfEmpty(),
        (c, o) => new { c.c.Name, OrderAmount = o?.Amount ?? 0 }
    );
```

---

### **Rozdíl mezi `GroupJoin` a `Join`**  

| Vlastnost               | **`GroupJoin`**                       | **`Join`**                             |
|-------------------------|----------------------------------------|----------------------------------------|
| **Výstup**             | Hierarchický (1:N).                   | Plochý seznam párů (1:1).              |
| **Typ spojení**         | Left outer join.                      | Inner join.                            |
| **Použití**             | Když potřebujete seskupit odpovídající prvky. | Když potřebujete párovat prvky 1:1.    |

---

### **Časté chyby**  

1. **Záměna pořadí klíčů v query syntaxi**:  
   ```csharp
   // Chyba: "inner.Key equals outer.Key" místo "outer.Key equals inner.Key"
   var invalid = from c in customers join o in orders on o.CustomerId equals c.Id into ordersGroup select ...;
   ```

2. **Ignorování `DefaultIfEmpty` při ploché transformaci**:  
   ```csharp
   // Bez DefaultIfEmpty by SelectMany vrátil pouze zákazníky s objednávkami!
   var risky = customers.GroupJoin(...).SelectMany(x => x.orders, ...);
   ```

3. **Nesprávné použití u kolekcí v paměti**:  
   - Pro malé kolekce je `GroupJoin` efektivní, pro velké preferujte databázové řešení.  

---

### **Tipy pro efektivní použití**  

- **Filtrace vnitřní kolekce před spojením**:  
  ```csharp
  var filtered = customers.GroupJoin(
      orders.Where(o => o.Date.Year == 2023),
      c => c.Id,
      o => o.CustomerId,
      ...
  );
  ```
- **Kombinace s `Select` pro agregace**:  
  ```csharp
  var stats = customers.GroupJoin(
      orders,
      c => c.Id,
      o => o.CustomerId,
      (c, orders) => new { c.Name, AvgOrder = orders.Average(o => o.Amount) }
  );
  ```

---

### **Shrnutí**  

- `GroupJoin` vytváří **hierarchická data** propojením dvou kolekcí (left outer join).  
- Umožňuje **seskupení odpovídajících prvků** pod každý prvek z první kolekce.  
- Efektivně spolupracuje s **EF Core** (převod na SQL `LEFT JOIN`).  
- Pro plochý výstup kombinujte s `SelectMany` a `DefaultIfEmpty`.  

📖 **Zdroj**: [Microsoft Docs – GroupJoin](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.groupjoin)
