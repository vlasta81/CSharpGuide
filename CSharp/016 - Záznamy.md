
### **1. Co jsou záznamy (records)?**

- **Imutabilní datové struktury** urèené pro reprezentaci dat s **automaticky generovanou logikou pro rovnost, kopírování a formátování**.
- **Úèel**: Zjednodušení práce s nemìnnými daty (napø. DTO, hodnotové objekty).
- **Klíèové vlastnosti**:
  - Imutabilita (výchozí chování, ale lze použít mutable vlastnosti).
  - Value-based equality (rovnost založená na hodnotách vlastností).
  - Podpora `with` pro ne-destruktivní mutace.
  - Lze dìdit (jen od jiných záznamù).

---

### **2. Deklarace a syntaxe**

#### **a) Základní deklarace (C# 9+)**  

```csharp
// Pozicionální syntax (vlastnosti se inicializují pøes konstruktor)
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

### **3. Klíèové vlastnosti**

#### **a) Value-based Equality**  

- Dva záznamy jsou si rovny, pokud mají **stejné hodnoty všech vlastností** (na rozdíl od tøíd, které používají referenèní rovnost).  
```csharp
var person1 = new Person("Karel", 30);
var person2 = new Person("Karel", 30);
Console.WriteLine(person1 == person2); // True
```

#### **b) Ne-destruktivní mutace (`with`)**  

- Vytvoøí **kopii** záznamu s upravenými vlastnostmi.  
```csharp
var original = new Person("Anna", 25);
var modified = original with { Age = 26 };
```

#### **c) Automatický `ToString()`**  

- Generuje formátovaný øetìzec s hodnotami vlastností.  
```csharp
var person = new Person("Eva", 28);
Console.WriteLine(person); // Person { Name = Eva, Age = 28 }
```

#### **d) Dekonstrukce**  

- Umožòuje rozložit záznam na jednotlivé hodnoty.  
```csharp
var (name, age) = person; // Dekonstrukce
```

---

### **4. Dìdiènost**

- Záznamy mohou dìdit od jiných záznamù.  
```csharp
public record Student(string Name, int Age, string University) : Person(Name, Age);
```

---

### **5. Porovnání s tøídami a strukturami**

| **Vlastnost**          | **Záznam (record)**       | **Tøída (class)**        | **Struktura (struct)**     |  
|-------------------------|---------------------------|--------------------------|----------------------------|  
| **Imutabilita**         | Výchozí                   | Nepovinná                | Nepovinná                  |  
| **Rovnost**             | Value-based               | Reference-based          | Value-based                |  
| **Dìdiènost**           | Jen mezi záznamy          | Podporuje                | Nepodporuje                |  
| **Použití**             | DTO, hodnotové objekty    | Obecné úèely             | Malé hodnotové typy        |  

---

### **6. Kdy použít záznamy?**

- **Data Transfer Objects (DTO)**: Pøenos dat mezi vrstvami.  
- **Hodnotové objekty**: Napø. penìžní èástky, souøadnice.  
- **Imutabilní konfigurace**: Bezpeènost ve vícevláknovém prostøedí.  

---

### **7. Bìžné chyby a tipy**

- **Pokus o zmìnu imutabilní vlastnosti**:
  ```csharp
  var person = new Person("Karel", 30);
  person.Name = "Jan"; // Chyba: Init-only vlastnost!
  ```
- **Zámìna `record` a `class`**: Pro mìnitelné stavy použijte tøídu.  
- **Použití `record struct` (C# 10+)**: Imutabilní struktury.  
  ```csharp
  public record struct Point(int X, int Y);
  ```

---

### **8. Ukázky kódu**

#### **DTO pro API odpovìï**

```csharp
public record ApiResponse(bool Success, string Data, DateTime Timestamp);
var response = new ApiResponse(true, "OK", DateTime.UtcNow);
```

#### **Dìdiènost a rozšíøení**

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

### **9. Doporuèené zdroje**

- **Oficiální dokumentace**: [Records in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/builtin-types/record)
