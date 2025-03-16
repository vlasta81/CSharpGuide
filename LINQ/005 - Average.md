
### **`Average`**  

Metoda `Average` slouží k **výpočtu průměrné hodnoty** z kolekce numerických dat (např. `int`, `double`, `decimal`). Je součástí standardních agregací LINQ a často se používá pro analýzu datových sad.

---

#### **Základní charakteristika**  

- **Účel**: Vypočítat aritmetický průměr prvků v kolekci.  
- **Podporované typy**: `int`, `long`, `float`, `double`, `decimal`, `nullable` verze těchto typů.  
- **Návratový typ**:  
  - `double` pro `int`, `long`, `float`.  
  - `decimal` pro `decimal`.  
  - `T?` pro nullable typy (pokud je kolekce prázdná nebo obsahuje pouze `null`, vrátí `null`).  
- **Prázdná kolekce**: Vyvolá výjimku `InvalidOperationException`, pokud kolekce neobsahuje žádné prvky (u non-nullable typů).  

---

### **Syntaxe**  

```csharp
// Pro kolekci čísel:
double avg = kolekce.Average();

// Pro kolekci objektů s výběrem vlastnosti:
double avg = kolekce.Average(x => x.Vlastnost);
```

---

### **Klíčové vlastnosti**  

1. **Automatické přeskočení `null` hodnot** u nullable typů:  
   ```csharp
   List<int?> numbers = new List<int?> { 10, null, 20, null };
   double? avg = numbers.Average(); // (10 + 20) / 2 = 15
   ```

2. **Použití s projekcí** (lambda výraz):  
   ```csharp
   List<Person> people = new List<Person> { new Person { Age = 30 }, new Person { Age = 40 } };
   double avgAge = people.Average(p => p.Age); // 35
   ```

3. **Integrace s dalšími operátory** (např. `Where`):  
   ```csharp
   double avgEven = numbers.Where(n => n % 2 == 0).Average();
   ```

---

### **Příklady použití**  

#### **1. Základní výpočet průměru**  

```csharp
List<int> temperatures = new List<int> { 15, 20, 25, 18 };
double avgTemp = temperatures.Average(); // 19.5
```

#### **2. Průměr u objektů s vlastností**  

```csharp
class Product { public decimal Price { get; set; } }
List<Product> products = new List<Product> 
{ 
    new Product { Price = 100 }, 
    new Product { Price = 200 } 
};

decimal avgPrice = products.Average(p => p.Price); // 150
```

#### **3. Průměr s nullable typy**  

```csharp
List<double?> measurements = new List<double?> { 5.5, null, 10.5, null };
double? avgMeasurement = measurements.Average(); // 8.0
```

---

### **Časté chyby a upozornění**  

1. **Prázdná kolekce u non-nullable typů**:  
   ```csharp
   List<int> empty = new List<int>();
   double avg = empty.Average(); // Vyvolá InvalidOperationException
   ```

2. **Neplatné použití pro nečíselné typy**:  
   ```csharp
   List<string> texts = new List<string> { "Ahoj", "světe" };
   // Chyba: nelze použít Average() bez selektoru na nečíselné typy!
   double avgLength = texts.Average(s => s.Length); // Správně: průměr délek řetězců
   ```

3. **Neošetřené `null` u referenčních typů**:  
   ```csharp
   List<Person> people = new List<Person> { null, new Person { Age = 30 } };
   double avg = people.Average(p => p.Age); // Vyvolá NullReferenceException
   ```

---

### **Tipy pro použití**  

- **Kontrola prázdné kolekce**:  
  ```csharp
  if (kolekce.Any()) 
  {
      double avg = kolekce.Average();
  }
  ```
- **Kombinace s `Where` pro filtraci**:  
  ```csharp
  var avgPositive = numbers.Where(n => n > 0).Average();
  ```
- **Přesnost u `decimal`**:  
  Pro finanční hodnoty preferujte `decimal` místo `double`.  

---

### **Shrnutí**  

- `Average` je **efektivní nástroj pro výpočet průměru** numerických hodnot.  
- Podporuje **nullable typy** a **automatické přeskočení `null`**.  
- Vyžaduje **ne-prázdnou kolekci** u non-nullable typů.  
- Lze kombinovat s dalšími operátory LINQ pro komplexní dotazy.  

📖 **Zdroj**: [Microsoft Docs – Average](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.average)
