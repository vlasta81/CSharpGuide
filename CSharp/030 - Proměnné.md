
### **1. Definice prom�nn�ch**  

Prom�nn� je **pojmenovan� oblast pam�ti** ur�en� k ukl�d�n� dat. Ka�d� prom�nn� m�:  
- **Typ** (nap�. `int`, `string`, `bool`).  
- **N�zev** (identifik�tor).  
- **Hodnotu** (data, kter� uchov�v�).  

---

### **2. Deklarace a inicializace**  

#### **a) Explicitn� typ**  

```csharp
int age = 25; // Deklarace s inicializac�
string name;   // Deklarace bez inicializace (lze a� pozd�ji p�i�adit hodnotu)
name = "Alice";
```

#### **b) Implicitn� typ (`var`)**  

Typ je odvozen od p�i�azen� hodnoty (mus� b�t inicializov�na p�i deklaraci).  
```csharp
var isValid = true; // Kompil�tor ur�� typ jako `bool`
var numbers = new List<int>(); // Typ `List<int>`
```

#### **c) Konstanty (`const`)**  

Nem�nn� hodnoty zn�m� p�i kompilaci.  
```csharp
const double PI = 3.1415;
```

---

### **3. Typy prom�nn�ch**  

#### **a) Hodnotov� typy (Value Types)**  

- Ukl�daj� **data p��mo v pam�ti** (na z�sobn�ku).  
- Pat�� sem:  
  - Primitivn� typy: `int`, `double`, `bool`, `char`, `struct`.  
  - V��ty (`enum`).  
```csharp
int number = 10;
DateTime date = DateTime.Now; // Struktura (value type)
```

#### **b) Referen�n� typy (Reference Types)**  

- Ukl�daj� **odkaz na data** (na hald�).  
- Pat�� sem: `class`, `string`, `array`, `delegate`, `interface`.  
```csharp
string text = "Hello";
List<int> list = new List<int>();
```

#### **c) Nullable typy**  

- Umo��uj� hodnotov�m typ�m p�i�adit `null` pomoc� `?`.  
```csharp
int? nullableNumber = null;
DateTime? date = null;
```

---

### **4. Rozsah platnosti (Scope)** 

- **Blokov� scope**: Prom�nn� deklarovan� v `{}` (cykly, podm�nky).  
- **Metodick� scope**: Platn� v cel� metod�.  
- **T��dn� scope (pole)**: Platn� v cel� t��d� (instance nebo statick�).  

```csharp
public class Example 
{
    private int _classField; // T��dn� prom�nn� (pole)

    public void Method() 
    {
        int localVar = 5; // Metodick� prom�nn�
        if (true) 
        {
            int blockVar = 10; // Blokov� prom�nn�
        }
    }
}
```

---

### **5. V�choz� hodnoty**  

- **Hodnotov� typy**: Inicializov�ny na v�choz� hodnotu (nap�. `0` pro `int`, `false` pro `bool`).  
- **Referen�n� typy**: Inicializov�ny na `null`.  

```csharp
int defaultInt;         // 0
bool defaultBool;       // false
string defaultString;   // null
```

---

### **6. �ivotnost prom�nn�ch**  

- **Hodnotov� typy**: Zni�eny p�i opu�t�n� scope.  
- **Referen�n� typy**: Uvoln�ny garbage collectorem (GC), kdy� nejsou odkazy.  

---

### **7. Kl��ov� modifik�tory**  

- **`readonly`**: Prom�nnou lze nastavit pouze v deklaraci nebo konstruktoru (pro t��dn� pole).  
- **`static`**: Prom�nn� pat�� t��d�, ne instanci.  
```csharp
public class Car 
{
    public static int Count = 0; // Statick� prom�nn�
    public readonly string Model; // Readonly prom�nn�

    public Car(string model) 
    {
        Model = model;
        Count++;
    }
}
```

---

### **8. B�n� chyby**  

- **Pou�it� neinicializovan� prom�nn�**:  
```csharp
int x;
Console.WriteLine(x); // Chyba kompilace!
```  
- **Z�m�na hodnotov�ch a referen�n�ch typ�**:  
```csharp
Point p1 = new Point(1, 2);
Point p2 = p1; // Kopie hodnoty (struct)
p2.X = 5;      // p1.X z�st�v� 1

// U referen�n�ch typ� by zm�na ovlivnila ob� prom�nn�!
```  

---

### **9. Doporu�en� postupy**  

1. **Popisn� n�zvy**: `totalPrice` m�sto `tp`.  
2. **Inicializace prom�nn�ch**: P�i deklaraci, pokud mo�no.  
3. **Minimalizujte scope**: Deklarujte prom�nn� co nejbl�e m�stu pou�it�.  
4. **Pou��vejte `var` pro zjednodu�en�**: Kdy� je typ z�ejm�.  

---

### **10. P��klady**  

#### **Hodnotov� vs. referen�n� typy**  

```csharp
// Hodnotov� typ (struct)
int a = 5;
int b = a; // Kopie hodnoty
b = 10;    // a z�st�v� 5

// Referen�n� typ (class)
StringBuilder sb1 = new StringBuilder();
StringBuilder sb2 = sb1; // Kopie reference
sb2.Append("Ahoj");       // sb1 i sb2 odkazuj� na stejn� objekt
```

#### **Nullable typy** 

```csharp
int? cislo = null;
if (cislo.HasValue)
    Console.WriteLine(cislo.Value);
```

---

### **11. Shrnut�** 

- **Prom�nn�** slou�� k ukl�d�n� dat s ur�en�m typem.  
- **Hodnotov� typy** p��mo obsahuj� data, **referen�n� typy** obsahuj� odkazy.  
- **`var`** zjednodu�uje deklaraci, kdy� je typ z�ejm�.  
- **Scope a �ivotnost** ur�uj� dostupnost a existenci prom�nn�.  
