
### **Anonymní typy v jazyce C#**

Anonymní typy umožňují vytvářet **dočasné objekty bez formální deklarace třídy**, například pro projekci dat v LINQ dotazech nebo pro rychlé seskupení hodnot. Jsou kompilátorem generované, **imutabilní** (vlastnosti nelze měnit) a jejich typ je odvozen automaticky.

---

### **1. Vytvoření anonymního typu**

- Syntaxe: `new { Vlastnost1 = hodnota1, Vlastnost2 = hodnota2 }`.
- Vlastnosti jsou **read-only** a typy jsou odvozeny automaticky.
- Povinné použití klíčového slova `var`:
  ```csharp
  var person = new { Name = "Alice", Age = 30 };
  Console.WriteLine($"{person.Name} ({person.Age})"); // Alice (30)
  ```

---

### **2. Klíčové vlastnosti**

- **Imutabilita**: Po vytvoření nelze hodnoty měnit.
  ```csharp
  person.Age = 40; // Chyba kompilace: Property 'Age' is read-only.
  ```
- **Odvození názvů vlastností**: Pokud název odpovídá proměnné, lze syntaxi zkrátit:
  ```csharp
  string name = "Bob";
  int age = 25;
  var person = new { name, age }; // Vlastnosti: name, age
  ```
- **Value-based rovnost**: Dva anonymní typy se stejnými vlastnostmi a hodnotami jsou považovány za stejné:
  ```csharp
  var a = new { X = 1, Y = 2 };
  var b = new { X = 1, Y = 2 };
  Console.WriteLine(a.Equals(b)); // True (hodnoty jsou stejné)
  ```

---

### **3. Hlavní použití**

#### **a) Projekce dat v LINQ**

```csharp
var users = new List<User> { /* ... */ };
var userData = users.Select(u => new { u.Name, u.Email }).ToList();

foreach (var data in userData)
    Console.WriteLine($"{data.Name}: {data.Email}");
```

#### **b) Seskupení hodnot pro dočasné účely**

```csharp
var result = new { Sum = 100, Count = 5, IsValid = true };
Console.WriteLine($"Průměr: {result.Sum / result.Count}");
```

---

### **4. Omezení**

- **Lokální použití**: Nelze je předávat mezi metodami (pokud nepoužijete `object`, ale ztratíte typovou informaci).
- **Žádné metody**: Obsahují pouze vlastnosti.
- **Nepodporují dědičnost**: Jsou uzavřené (sealed).
- **Nelze přidávat vlastnosti**: Struktura je pevně definována při vytvoření.

---

### **5. Porovnání s n-ticemi (tuples)**

| Vlastnost               | Anonymní typy                  | N-tice (`Tuple`/`ValueTuple`)       |
|-------------------------|--------------------------------|-------------------------------------|
| **Imutabilita**         | Všechny vlastnosti read-only   | N-tice jsou mutable (ValueTuple)    |
| **Pojmenování vlastností** | Explicitní (např. `Name`)     | N-tice mají výchozí názvy (`Item1`) |
| **Použití**             | LINQ, dočasné seskupení dat    | Vrácení více hodnot z metody        |

---

### **6. Příklady z praxe**

#### **Spojení dat z více zdrojů**

```csharp
var products = new List<Product> { /* ... */ };
var orders = new List<Order> { /* ... */ };

var joinedData = products.Join(orders,
    p => p.Id,
    o => o.ProductId,
    (p, o) => new { p.Name, o.Quantity, o.Date }
);

foreach (var item in joinedData)
    Console.WriteLine($"{item.Name}: {item.Quantity} ks");
```

#### **Dynamické formátování výstupu**

```csharp
var report = new { 
    TotalSales = 15000, 
    Average = 7500, 
    Timestamp = DateTime.Now 
};
Console.WriteLine($"Report z {report.Timestamp:dd.MM.yyyy}: {report.TotalSales} Kč");
```

---

### **7. Tipy a upozornění**

- **Nepoužívejte je pro složité struktury**: Pro trvalé nebo komplexní objekty definujte třídu/record.
- **Používejte v LINQ projekcích**: Zachovávají čitelnost a snižují množství přenášených dat.
- **Nahraďte n-ticemi**, pokud potřebujete předávat data mezi metodami.

---

### **Shrnutí**

- **Anonymní typy**: Dočasné, imutabilní objekty s automaticky generovanými vlastnostmi.
- **Použití**: Projekce dat v LINQ, dočasné seskupení hodnot.
- **Výhody**: Čitelnost, value-based rovnost, imutabilita.
- **Omezení**: Lokální rozsah, žádné metody.

📌 **Příklad – Filtrace a projekce v LINQ**:
```csharp
var filteredUsers = users
    .Where(u => u.Age > 18)
    .Select(u => new { u.Id, u.Name })
    .ToList();
```
