
### **1. Co jsou z�znamy (records)?**

- **Imutabiln� datov� struktury** ur�en� pro reprezentaci dat s **automaticky generovanou logikou pro rovnost, kop�rov�n� a form�tov�n�**.
- **��el**: Zjednodu�en� pr�ce s nem�nn�mi daty (nap�. DTO, hodnotov� objekty).
- **Kl��ov� vlastnosti**:
  - Imutabilita (v�choz� chov�n�, ale lze pou��t mutable vlastnosti).
  - Value-based equality (rovnost zalo�en� na hodnot�ch vlastnost�).
  - Podpora `with` pro ne-destruktivn� mutace.
  - Lze d�dit (jen od jin�ch z�znam�).

---

### **2. Deklarace a syntaxe**

#### **a) Z�kladn� deklarace (C# 9+)**  

```csharp
// Pozicion�ln� syntax (vlastnosti se inicializuj� p�es konstruktor)
public record Person(string Name, int Age);

// Nominal syntax (explicitn� definice vlastnost�)
public record Car 
{
    public string Model { get; init; } // init-only pro imutabilitu
    public int Year { get; init; }
}
```

#### **b) Mutable z�znamy**  

```csharp
public record MutablePoint 
{
    public int X { get; set; } // Mutable vlastnost
    public int Y { get; set; }
}
```

---

### **3. Kl��ov� vlastnosti**

#### **a) Value-based Equality**  

- Dva z�znamy jsou si rovny, pokud maj� **stejn� hodnoty v�ech vlastnost�** (na rozd�l od t��d, kter� pou��vaj� referen�n� rovnost).  
```csharp
var person1 = new Person("Karel", 30);
var person2 = new Person("Karel", 30);
Console.WriteLine(person1 == person2); // True
```

#### **b) Ne-destruktivn� mutace (`with`)**  

- Vytvo�� **kopii** z�znamu s upraven�mi vlastnostmi.  
```csharp
var original = new Person("Anna", 25);
var modified = original with { Age = 26 };
```

#### **c) Automatick� `ToString()`**  

- Generuje form�tovan� �et�zec s hodnotami vlastnost�.  
```csharp
var person = new Person("Eva", 28);
Console.WriteLine(person); // Person { Name = Eva, Age = 28 }
```

#### **d) Dekonstrukce**  

- Umo��uje rozlo�it z�znam na jednotliv� hodnoty.  
```csharp
var (name, age) = person; // Dekonstrukce
```

---

### **4. D�di�nost**

- Z�znamy mohou d�dit od jin�ch z�znam�.  
```csharp
public record Student(string Name, int Age, string University) : Person(Name, Age);
```

---

### **5. Porovn�n� s t��dami a strukturami**

| **Vlastnost**          | **Z�znam (record)**       | **T��da (class)**        | **Struktura (struct)**     |  
|-------------------------|---------------------------|--------------------------|----------------------------|  
| **Imutabilita**         | V�choz�                   | Nepovinn�                | Nepovinn�                  |  
| **Rovnost**             | Value-based               | Reference-based          | Value-based                |  
| **D�di�nost**           | Jen mezi z�znamy          | Podporuje                | Nepodporuje                |  
| **Pou�it�**             | DTO, hodnotov� objekty    | Obecn� ��ely             | Mal� hodnotov� typy        |  

---

### **6. Kdy pou��t z�znamy?**

- **Data Transfer Objects (DTO)**: P�enos dat mezi vrstvami.  
- **Hodnotov� objekty**: Nap�. pen�n� ��stky, sou�adnice.  
- **Imutabiln� konfigurace**: Bezpe�nost ve v�cevl�knov�m prost�ed�.  

---

### **7. B�n� chyby a tipy**

- **Pokus o zm�nu imutabiln� vlastnosti**:
  ```csharp
  var person = new Person("Karel", 30);
  person.Name = "Jan"; // Chyba: Init-only vlastnost!
  ```
- **Z�m�na `record` a `class`**: Pro m�niteln� stavy pou�ijte t��du.  
- **Pou�it� `record struct` (C# 10+)**: Imutabiln� struktury.  
  ```csharp
  public record struct Point(int X, int Y);
  ```

---

### **8. Uk�zky k�du**

#### **DTO pro API odpov��**

```csharp
public record ApiResponse(bool Success, string Data, DateTime Timestamp);
var response = new ApiResponse(true, "OK", DateTime.UtcNow);
```

#### **D�di�nost a roz���en�**

```csharp
public record Vehicle(string Manufacturer);
public record Car(string Manufacturer, int Doors) : Vehicle(Manufacturer);

Car car = new Car("Toyota", 4);
Console.WriteLine(car); // Car { Manufacturer = Toyota, Doors = 4 }
```

#### **Ne-destruktivn� mutace**

```csharp
var original = new Car("Skoda", 5);
var updated = original with { Manufacturer = "BMW" };
Console.WriteLine(updated); // Car { Manufacturer = BMW, Doors = 5 }
```

---

### **9. Doporu�en� zdroje**

- **Ofici�ln� dokumentace**: [Records in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/builtin-types/record)
