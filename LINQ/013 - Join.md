
### **`Join`**  

Metoda `Join` slouží k **kombinaci dvou kolekcí** na základě společného klíče, podobně jako `INNER JOIN` v SQL. Umožňuje propojit data z různých zdrojů (např. objednávky a zákazníci) a vytvořit výsledky obsahující informace z obou kolekcí.

---

#### **Základní charakteristika**  

- **Účel**: Propojit prvky dvou kolekcí, kde **klíče odpovídají**.  
- **Typ spojení**: **Inner join** (výsledek obsahuje pouze páry s nalezenými klíči).  
- **Návratový typ**: `IEnumerable<TResult>` (nebo `IQueryable<TResult>` pro externí zdroje dat).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  

---

### **Syntaxe**  

**1. Method Syntax**:  
```csharp
var result = outerCollection.Join(
    innerCollection,                     // Druhá kolekce
    outerKeySelector: outer => outer.Key, // Klíč z první kolekce
    innerKeySelector: inner => inner.Key, // Klíč z druhé kolekce
    resultSelector: (outer, inner) => new { outer.Prop, inner.Prop } // Výsledný objekt
);
```

**2. Query Syntax**:  
```csharp
var result = from outer in outerCollection
             join inner in innerCollection
             on outer.Key equals inner.Key
             select new { outer.Prop, inner.Prop };
```

---

### **Klíčové vlastnosti**  

1. **Podpora pro složené klíče**:  
   ```csharp
   // Spojení podle více vlastností pomocí anonymního typu
   var joined = orders.Join(
       customers,
       o => new { o.CustomerId, o.Year },
       c => new { c.CustomerId, c.Year },
       (o, c) => new { o.OrderId, c.Name }
   );
   ```

2. **Vlastní porovnávač**:  
   ```csharp
   // Spojení s case-insensitive porovnáním řetězců
   var caseInsensitiveJoin = products.Join(
       categories,
       p => p.CategoryName,
       c => c.Name,
       (p, c) => p,
       StringComparer.OrdinalIgnoreCase
   );
   ```

3. **Optimalizace v LINQ to Entities (EF Core)**:  
   - Převádí se na SQL `INNER JOIN`.  
   ```csharp
   var query = dbContext.Orders
       .Join(
           dbContext.Customers,
           o => o.CustomerId,
           c => c.Id,
           (o, c) => new { o.Id, c.Name }
       )
       .ToList(); // Generuje SQL JOIN
   ```

---

### **Příklady použití**  

#### **1. Spojení dvou kolekcí objektů**  

```csharp
class Employee { public int DepartmentId; public string Name; }
class Department { public int Id; public string Name; }

List<Employee> employees = new List<Employee> { /* ... */ };
List<Department> departments = new List<Department> { /* ... */ };

var employeeDetails = employees.Join(
    departments,
    e => e.DepartmentId,
    d => d.Id,
    (e, d) => new { e.Name, Department = d.Name }
);
```

#### **2. Spojení s anonymními typy**  

```csharp
var orders = new List<Order> { /* ... */ };
var customers = new List<Customer> { /* ... */ };

var orderCustomerPairs = orders.Join(
    customers,
    o => o.CustomerId,
    c => c.Id,
    (o, c) => new { OrderId = o.Id, c.Name, o.Total }
);
```

#### **3. Spojení s podmínkou**  

```csharp
// Spojení pouze aktivních zákazníků
var activeOrders = orders.Join(
    customers.Where(c => c.IsActive),
    o => o.CustomerId,
    c => c.Id,
    (o, c) => o
);
```

---

### **Rozdíl mezi `Join` a `GroupJoin`**  

| Vlastnost               | **`Join`**                             | **`GroupJoin`**                       |
|-------------------------|----------------------------------------|----------------------------------------|
| **Výsledek**            | Plochý seznam párů (1:1).             | Hierarchická struktura (1:N).          |
| **Typ spojení**         | Inner join.                            | Left outer join (s `DefaultIfEmpty`).  |
| **Použití**             | Když potřebujete párovat prvky 1:1.   | Když jeden prvek může mít více záznamů. |

---

### **Časté chyby**  

1. **Záměna pořadí klíčů v query syntaxi**:  
   ```csharp
   // Špatně: "inner.Key equals outer.Key" místo "outer.Key equals inner.Key"
   var invalid = from o in orders join c in customers on c.Id equals o.CustomerId select ...;
   ```

2. **Ignorování `null` klíčů**:  
   ```csharp
   // Pokud CustomerId je null, prvek se do výsledku nedostane!
   var risky = orders.Join(customers, o => o.CustomerId, c => c.Id, ...);
   ```

3. **Nesprávné použití u kolekcí v paměti**:  
   - Pro velké kolekce preferujte `Join` přes `Where` s vnořenými cykly (efektivnější).  

---

### **Tipy pro efektivní použití**  

- **Filtrujte kolekce před spojením**:  
  ```csharp
  var filteredJoin = orders
      .Where(o => o.Date.Year == 2023)
      .Join(customers, ...);
  ```
- **Používejte `Join` s EF Core**:  
  - Generuje optimalizované SQL dotazy (lepší než ruční řešení v paměti).  
- **Kombinujte s `Select` pro projekci**:  
  ```csharp
  var result = orders.Join(
      customers,
      o => o.CustomerId,
      c => c.Id,
      (o, c) => new OrderDTO { Id = o.Id, CustomerName = c.Name }
  );
  ```

---

### **Shrnutí**  

- `Join` slouží k **propojení dvou kolekcí** na základě společného klíče (inner join).  
- Podporuje **složené klíče**, **vlastní porovnávače** a **EF Core** (převod na SQL).  
- Výsledek obsahuje pouze prvky s **odpovídajícími klíči** v obou kolekcích.  

📖 **Zdroj**: [Microsoft Docs – Join](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.join)
