
### **1. Co jsou záznamy (records)?**

- **Imutabilní datové struktury** určené pro reprezentaci dat s **automaticky generovanou logikou pro rovnost, kopírování a formátování**.
- **Účel**: Zjednodušení práce s neměnnými daty (např. DTO, hodnotové objekty).
- **Klíčové vlastnosti**:
  - Imutabilita (výchozí chování, ale lze použít mutable vlastnosti).
  - Value-based equality (rovnost založená na hodnotách vlastností).
  - Podpora `with` pro ne-destruktivní mutace.
  - Lze dědit (jen od jiných záznamů).

---

### **2. Deklarace a syntaxe**

#### **a) Základní deklarace (C# 9+)**  

```csharp
// Pozicionální syntax (vlastnosti se inicializují přes konstruktor)
public record Person(string Name, int Age);

// Nominal syntax (explicitní definice vlastností)
public record Car 
{
    public string Model { get; init; } // init-only pro imutabilitu
    public int Year { get; init; }
}
```

#### **b) Mutable záznamy**  

```csharp
public record MutablePoint 
{
    public int X { get; set; } // Mutable vlastnost
    public int Y { get; set; }
}
```

---

### **3. Klíčové vlastnosti**

#### **a) Value-based Equality**  

- Dva záznamy jsou si rovny, pokud mají **stejné hodnoty všech vlastností** (na rozdíl od tříd, které používají referenční rovnost).  
```csharp
var person1 = new Person("Karel", 30);
var person2 = new Person("Karel", 30);
Console.WriteLine(person1 == person2); // True
```

#### **b) Ne-destruktivní mutace (`with`)**  

- Vytvoří **kopii** záznamu s upravenými vlastnostmi.  
```csharp
var original = new Person("Anna", 25);
var modified = original with { Age = 26 };
```

#### **c) Automatický `ToString()`**  

- Generuje formátovaný řetězec s hodnotami vlastností.  
```csharp
var person = new Person("Eva", 28);
Console.WriteLine(person); // Person { Name = Eva, Age = 28 }
```

#### **d) Dekonstrukce**  

- Umožňuje rozložit záznam na jednotlivé hodnoty.  
```csharp
var (name, age) = person; // Dekonstrukce
```

---

### **4. Dědičnost**

- Záznamy mohou dědit od jiných záznamů.  
```csharp
public record Student(string Name, int Age, string University) : Person(Name, Age);
```

---

### **5. Porovnání s třídami a strukturami**

| **Vlastnost**          | **Záznam (record)**       | **Třída (class)**        | **Struktura (struct)**     |  
|-------------------------|---------------------------|--------------------------|----------------------------|  
| **Imutabilita**         | Výchozí                   | Nepovinná                | Nepovinná                  |  
| **Rovnost**             | Value-based               | Reference-based          | Value-based                |  
| **Dědičnost**           | Jen mezi záznamy          | Podporuje                | Nepodporuje                |  
| **Použití**             | DTO, hodnotové objekty    | Obecné účely             | Malé hodnotové typy        |  

---

### **6. Kdy použít záznamy?**

- **Data Transfer Objects (DTO)**: Přenos dat mezi vrstvami.  
- **Hodnotové objekty**: Např. peněžní částky, souřadnice.  
- **Imutabilní konfigurace**: Bezpečnost ve vícevláknovém prostředí.  

---

### **7. Běžné chyby a tipy**

- **Pokus o změnu imutabilní vlastnosti**:
  ```csharp
  var person = new Person("Karel", 30);
  person.Name = "Jan"; // Chyba: Init-only vlastnost!
  ```
- **Záměna `record` a `class`**: Pro měnitelné stavy použijte třídu.  
- **Použití `record struct` (C# 10+)**: Imutabilní struktury.  
  ```csharp
  public record struct Point(int X, int Y);
  ```

---

### **8. Ukázky kódu**

#### **DTO pro API odpověď**

```csharp
public record ApiResponse(bool Success, string Data, DateTime Timestamp);
var response = new ApiResponse(true, "OK", DateTime.UtcNow);
```

#### **Dědičnost a rozšíření**

```csharp
public record Vehicle(string Manufacturer);
public record Car(string Manufacturer, int Doors) : Vehicle(Manufacturer);

Car car = new Car("Toyota", 4);
Console.WriteLine(car); // Car { Manufacturer = Toyota, Doors = 4 }
```

#### **Ne-destruktivní mutace**

```csharp
var original = new Car("Skoda", 5);
var updated = original with { Manufacturer = "BMW" };
Console.WriteLine(updated); // Car { Manufacturer = BMW, Doors = 5 }
```

---

### **9. Doporučené zdroje**

- **Oficiální dokumentace**: [Records in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/builtin-types/record)
