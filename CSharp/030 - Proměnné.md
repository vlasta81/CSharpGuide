
### **1. Definice proměnných**  

Proměnná je **pojmenovaná oblast paměti** určená k ukládání dat. Každá proměnná má:  
- **Typ** (např. `int`, `string`, `bool`).  
- **Název** (identifikátor).  
- **Hodnotu** (data, která uchovává).  

---

### **2. Deklarace a inicializace**  

#### **a) Explicitní typ**  

```csharp
int age = 25; // Deklarace s inicializací
string name;   // Deklarace bez inicializace (lze až později přiřadit hodnotu)
name = "Alice";
```

#### **b) Implicitní typ (`var`)**  

Typ je odvozen od přiřazené hodnoty (musí být inicializována při deklaraci).  
```csharp
var isValid = true; // Kompilátor určí typ jako `bool`
var numbers = new List<int>(); // Typ `List<int>`
```

#### **c) Konstanty (`const`)**  

Neměnné hodnoty známé při kompilaci.  
```csharp
const double PI = 3.1415;
```

---

### **3. Typy proměnných**  

#### **a) Hodnotové typy (Value Types)**  

- Ukládají **data přímo v paměti** (na zásobníku).  
- Patří sem:  
  - Primitivní typy: `int`, `double`, `bool`, `char`, `struct`.  
  - Výčty (`enum`).  
```csharp
int number = 10;
DateTime date = DateTime.Now; // Struktura (value type)
```

#### **b) Referenční typy (Reference Types)**  

- Ukládají **odkaz na data** (na haldě).  
- Patří sem: `class`, `string`, `array`, `delegate`, `interface`.  
```csharp
string text = "Hello";
List<int> list = new List<int>();
```

#### **c) Nullable typy**  

- Umožňují hodnotovým typům přiřadit `null` pomocí `?`.  
```csharp
int? nullableNumber = null;
DateTime? date = null;
```

---

### **4. Rozsah platnosti (Scope)** 

- **Blokový scope**: Proměnné deklarované v `{}` (cykly, podmínky).  
- **Metodický scope**: Platné v celé metodě.  
- **Třídní scope (pole)**: Platné v celé třídě (instance nebo statické).  

```csharp
public class Example 
{
    private int _classField; // Třídní proměnná (pole)

    public void Method() 
    {
        int localVar = 5; // Metodická proměnná
        if (true) 
        {
            int blockVar = 10; // Bloková proměnná
        }
    }
}
```

---

### **5. Výchozí hodnoty**  

- **Hodnotové typy**: Inicializovány na výchozí hodnotu (např. `0` pro `int`, `false` pro `bool`).  
- **Referenční typy**: Inicializovány na `null`.  

```csharp
int defaultInt;         // 0
bool defaultBool;       // false
string defaultString;   // null
```

---

### **6. Životnost proměnných**  

- **Hodnotové typy**: Zničeny při opuštění scope.  
- **Referenční typy**: Uvolněny garbage collectorem (GC), když nejsou odkazy.  

---

### **7. Klíčové modifikátory**  

- **`readonly`**: Proměnnou lze nastavit pouze v deklaraci nebo konstruktoru (pro třídní pole).  
- **`static`**: Proměnná patří třídě, ne instanci.  
```csharp
public class Car 
{
    public static int Count = 0; // Statická proměnná
    public readonly string Model; // Readonly proměnná

    public Car(string model) 
    {
        Model = model;
        Count++;
    }
}
```

---

### **8. Běžné chyby**  

- **Použití neinicializované proměnné**:  
```csharp
int x;
Console.WriteLine(x); // Chyba kompilace!
```  
- **Záměna hodnotových a referenčních typů**:  
```csharp
Point p1 = new Point(1, 2);
Point p2 = p1; // Kopie hodnoty (struct)
p2.X = 5;      // p1.X zůstává 1

// U referenčních typů by změna ovlivnila obě proměnné!
```  

---

### **9. Doporučené postupy**  

1. **Popisné názvy**: `totalPrice` místo `tp`.  
2. **Inicializace proměnných**: Při deklaraci, pokud možno.  
3. **Minimalizujte scope**: Deklarujte proměnné co nejblíže místu použití.  
4. **Používejte `var` pro zjednodušení**: Když je typ zřejmý.  

---

### **10. Příklady**  

#### **Hodnotové vs. referenční typy**  

```csharp
// Hodnotový typ (struct)
int a = 5;
int b = a; // Kopie hodnoty
b = 10;    // a zůstává 5

// Referenční typ (class)
StringBuilder sb1 = new StringBuilder();
StringBuilder sb2 = sb1; // Kopie reference
sb2.Append("Ahoj");       // sb1 i sb2 odkazují na stejný objekt
```

#### **Nullable typy** 

```csharp
int? cislo = null;
if (cislo.HasValue)
    Console.WriteLine(cislo.Value);
```

---

### **11. Shrnutí** 

- **Proměnné** slouží k ukládání dat s určeným typem.  
- **Hodnotové typy** přímo obsahují data, **referenční typy** obsahují odkazy.  
- **`var`** zjednodušuje deklaraci, když je typ zřejmý.  
- **Scope a životnost** určují dostupnost a existenci proměnné.  
