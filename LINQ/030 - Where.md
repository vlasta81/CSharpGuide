
### **`Where`**  

Metoda `Where` slouží k **filtrování prvků v kolekci** na základě zadané podmínky. Je jedním z nejpoužívanějších operátorů LINQ a funguje podobně jako klauzule `WHERE` v SQL, ale s plnou integrací do syntaxe C#.

---

#### **Základní charakteristika**  

- **Účel**: Vybrat prvky, které splňují definovanou podmínku.  
- **Návratový typ**: `IEnumerable<T>` nebo `IQueryable<T>` (pro externí zdroje dat).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  
- **Podpora indexu**: Přetížení s parametrem indexu umožňuje filtrovat prvky podle jejich pozice v kolekci.  

---

### **Syntaxe**  

**1. Method Syntax** (lambda výrazy):  
```csharp
var filtered = kolekce.Where(prvek => podmínka);
```

**2. Query Syntax** (SQL-like styl):  
```csharp
var filtered = from prvek in kolekce
               where podmínka
               select prvek;
```

**3. Filtrování s indexem**:  
```csharp
var filtered = kolekce.Where((prvek, index) => index % 2 == 0); // Sudé pozice
```

---

### **Klíčové vlastnosti**  

1. **Kombinace podmínek**:  
   - Lze řetězit více `Where` metod nebo kombinovat podmínky pomocí logických operátorů.  
   ```csharp
   // Ekvivalentní zápisy:
   var result1 = kolekce.Where(x => x.A > 5 && x.B < 10);
   var result2 = kolekce.Where(x => x.A > 5).Where(x => x.B < 10);
   ```

2. **Práce s `IQueryable` (Entity Framework)**:  
   - Podmínky se převádějí na SQL `WHERE` (optimalizace na straně databáze).  
   ```csharp
   var adults = dbContext.People.Where(p => p.Age >= 18).ToList(); // Generuje SQL WHERE Age >= 18
   ```

3. **Filtrování `null` hodnot**:  
   ```csharp
   var validItems = kolekce.Where(x => x != null && x.Name != "");
   ```

---

### **Příklady použití**  

#### **1. Základní filtrování čísel**  

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
var evenNumbers = numbers.Where(n => n % 2 == 0); // { 2, 4 }
```

#### **2. Filtrování objektů**  

```csharp
class Person { public string Name { get; set; } public int Age { get; set; } }
List<Person> people = new List<Person> { /* ... */ };

var adults = people.Where(p => p.Age >= 18);
```

#### **3. Použití indexu**  

```csharp
List<string> fruits = new List<string> { "Apple", "Banana", "Cherry" };
var everySecond = fruits.Where((f, index) => index % 2 == 0); // "Apple", "Cherry"
```

#### **4. Kombinace s dalšími operátory**  

```csharp
var result = people
    .Where(p => p.Age > 18)
    .OrderBy(p => p.Name)
    .Select(p => p.Name);
```

---

### **Časté chyby a upozornění**  

1. **Nesprávné použití u `IQueryable`**:  
   - Pokud podmínka obsahuje kód, který nelze převést na SQL (např. volání metod), dojde k chybě za běhu.  
   ```csharp
   // Chyba: Nelze převést na SQL!
   var invalid = dbContext.People.Where(p => p.Name.ToUpper() == "ALICE");
   ```

2. **Nadbytečné volání `Where`**:  
   ```csharp
   // Zbytečné: Stačí jeden Where
   var redundant = people.Where(p => p.Age > 18).Where(p => p.Age < 30);
   ```

3. **Ignorování `null` hodnot**:  
   ```csharp
   // Vyvolá výjimku, pokud je některý prvek null!
   var risky = kolekce.Where(x => x.Name.Length > 0);
   ```

---

### **Výkonnostní tipy**  

- **Optimalizace pro databáze**:  
  - Používejte `Where` před `Select`, aby se filtrace provedla před projekcí (méně přenášených dat).  
- **Vyhněte se vnořeným dotazům**:  
  ```csharp
  // Méně efektivní:
  var bad = people.Where(p => p.Orders.Any(o => o.Total > 100));

  // Efektivnější (pro Entity Framework):
  var good = people.Where(p => dbContext.Orders.Any(o => o.PersonId == p.Id && o.Total > 100));
  ```

---

### **Shrnutí**  

- `Where` slouží k **filtrování prvků** podle podmínky.  
- Podporuje **deferred execution**, **indexované podmínky** a kombinace s dalšími operátory.  
- U `IQueryable` generuje optimalizované SQL dotazy.  
- Ošetřujte `null` hodnoty a nepřevoditelné podmínky v externích zdrojích dat.  

📖 **Zdroj**: [Microsoft Docs – Where](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.where)
