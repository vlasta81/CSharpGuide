
### **1. Definice promìnných**  

Promìnná je **pojmenovaná oblast pamìti** urèená k ukládání dat. Každá promìnná má:  
- **Typ** (napø. `int`, `string`, `bool`).  
- **Název** (identifikátor).  
- **Hodnotu** (data, která uchovává).  

---

### **2. Deklarace a inicializace**  

#### **a) Explicitní typ**  

```csharp
int age = 25; // Deklarace s inicializací
string name;   // Deklarace bez inicializace (lze až pozdìji pøiøadit hodnotu)
name = "Alice";
```

#### **b) Implicitní typ (`var`)**  

Typ je odvozen od pøiøazené hodnoty (musí být inicializována pøi deklaraci).  
```csharp
var isValid = true; // Kompilátor urèí typ jako `bool`
var numbers = new List<int>(); // Typ `List<int>`
```

#### **c) Konstanty (`const`)**  

Nemìnné hodnoty známé pøi kompilaci.  
```csharp
const double PI = 3.1415;
```

---

### **3. Typy promìnných**  

#### **a) Hodnotové typy (Value Types)**  

- Ukládají **data pøímo v pamìti** (na zásobníku).  
- Patøí sem:  
  - Primitivní typy: `int`, `double`, `bool`, `char`, `struct`.  
  - Výèty (`enum`).  
```csharp
int number = 10;
DateTime date = DateTime.Now; // Struktura (value type)
```

#### **b) Referenèní typy (Reference Types)**  

- Ukládají **odkaz na data** (na haldì).  
- Patøí sem: `class`, `string`, `array`, `delegate`, `interface`.  
```csharp
string text = "Hello";
List<int> list = new List<int>();
```

#### **c) Nullable typy**  

- Umožòují hodnotovým typùm pøiøadit `null` pomocí `?`.  
```csharp
int? nullableNumber = null;
DateTime? date = null;
```

---

### **4. Rozsah platnosti (Scope)** 

- **Blokový scope**: Promìnné deklarované v `{}` (cykly, podmínky).  
- **Metodický scope**: Platné v celé metodì.  
- **Tøídní scope (pole)**: Platné v celé tøídì (instance nebo statické).  

```csharp
public class Example 
{
    private int _classField; // Tøídní promìnná (pole)

    public void Method() 
    {
        int localVar = 5; // Metodická promìnná
        if (true) 
        {
            int blockVar = 10; // Bloková promìnná
        }
    }
}
```

---

### **5. Výchozí hodnoty**  

- **Hodnotové typy**: Inicializovány na výchozí hodnotu (napø. `0` pro `int`, `false` pro `bool`).  
- **Referenèní typy**: Inicializovány na `null`.  

```csharp
int defaultInt;         // 0
bool defaultBool;       // false
string defaultString;   // null
```

---

### **6. Životnost promìnných**  

- **Hodnotové typy**: Znièeny pøi opuštìní scope.  
- **Referenèní typy**: Uvolnìny garbage collectorem (GC), když nejsou odkazy.  

---

### **7. Klíèové modifikátory**  

- **`readonly`**: Promìnnou lze nastavit pouze v deklaraci nebo konstruktoru (pro tøídní pole).  
- **`static`**: Promìnná patøí tøídì, ne instanci.  
```csharp
public class Car 
{
    public static int Count = 0; // Statická promìnná
    public readonly string Model; // Readonly promìnná

    public Car(string model) 
    {
        Model = model;
        Count++;
    }
}
```

---

### **8. Bìžné chyby**  

- **Použití neinicializované promìnné**:  
```csharp
int x;
Console.WriteLine(x); // Chyba kompilace!
```  
- **Zámìna hodnotových a referenèních typù**:  
```csharp
Point p1 = new Point(1, 2);
Point p2 = p1; // Kopie hodnoty (struct)
p2.X = 5;      // p1.X zùstává 1

// U referenèních typù by zmìna ovlivnila obì promìnné!
```  

---

### **9. Doporuèené postupy**  

1. **Popisné názvy**: `totalPrice` místo `tp`.  
2. **Inicializace promìnných**: Pøi deklaraci, pokud možno.  
3. **Minimalizujte scope**: Deklarujte promìnné co nejblíže místu použití.  
4. **Používejte `var` pro zjednodušení**: Když je typ zøejmý.  

---

### **10. Pøíklady**  

#### **Hodnotové vs. referenèní typy**  

```csharp
// Hodnotový typ (struct)
int a = 5;
int b = a; // Kopie hodnoty
b = 10;    // a zùstává 5

// Referenèní typ (class)
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

- **Promìnné** slouží k ukládání dat s urèeným typem.  
- **Hodnotové typy** pøímo obsahují data, **referenèní typy** obsahují odkazy.  
- **`var`** zjednodušuje deklaraci, když je typ zøejmý.  
- **Scope a životnost** urèují dostupnost a existenci promìnné.  
