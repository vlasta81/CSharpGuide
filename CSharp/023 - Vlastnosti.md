
### **1. Definice vlastností**

Vlastnosti (properties) jsou **èleny tøíd**, které umožòují kontrolovaný pøístup k datùm. Kombinují funkènost metod s jednoduchou syntaxí polí. Slouží k:
- **Zapouzdøení**: Ochrana privátních polí pøed neplatnými hodnotami.
- **Validaci**: Kontrola vstupù pøi ètení/zápisu.
- **Výpoètùm**: Dynamické generování hodnot na základì jiných dat.

---

### **2. Základní syntaxe**

#### **a) Plná syntaxe (s backing field)**

```csharp
public class Osoba 
{
    private string _jmeno; // Privátní pole (backing field)

    public string Jmeno 
    {
        get { return _jmeno; } // Ètení hodnoty
        set { _jmeno = value ?? "Neznámý"; } // Zápis s validací
    }
}
```

#### **b) Automatické vlastnosti (auto-implemented)**

```csharp
public class Ucet 
{
    public decimal Zustatek { get; set; } // Backing field generuje kompilátor
}
```

---

### **3. Typy vlastností**

#### **a) Read-only vlastnost**  

- Lze èíst, ale nelze mìnit (má pouze `get`).  
```csharp
public string ID { get; } = Guid.NewGuid().ToString();
```

#### **b) Write-only vlastnost**  

- Lze mìnit, ale nelze èíst (má pouze `set`). Vzácné.  
```csharp
private string _heslo;
public string Heslo { set => _heslo = value; }
```

#### **c) Vypoèítaná vlastnost**  

- Hodnota se dynamicky poèítá.  
```csharp
public DateTime DatumNarozeni { get; set; }
public int Vek => DateTime.Now.Year - DatumNarozeni.Year; // Pouze get
```

#### **d) Init-only vlastnost (C# 9+)**  

- Lze nastavit **pouze pøi inicializaci objektu**.  
```csharp
public string Jmeno { get; init; }
// Použití:
Osoba osoba = new Osoba { Jmeno = "Anna" }; // OK
// osoba.Jmeno = "Karel"; // Chyba!
```

#### **e) Indexery**  

- Umožòují pøístup k objektu jako k poli.  
```csharp
public class Seznam 
{
    private string[] _data = new string[10];
    public string this[int index] 
    {
        get => _data[index];
        set => _data[index] = value;
    }
}
// Použití:
Seznam s = new Seznam();
s[0] = "Ahoj";
```

---

### **4. Modifikátory pøístupu**

- **Vlastní pøístup pro `get`/`set`**:  
```csharp
public string Email 
{
    get; 
    private set; // Zmìnitelný pouze uvnitø tøídy
}
```

- **Protected setter**:  
```csharp
public string Role { get; protected set; }
```

---

### **5. Validace a logika ve vlastnostech**

```csharp
public class BankovniUcet 
{
    private decimal _zustatek;
    
    public decimal Zustatek 
    {
        get => _zustatek;
        set 
        {
            if (value < 0) 
                throw new ArgumentException("Zùstatek nemùže být záporný.");
            _zustatek = value;
        }
    }
}
```

---

### **6. Statické vlastnosti**

- Patøí tøídì, ne instanci.  
```csharp
public class Nastaveni 
{
    public static int MaxPokusu { get; set; } = 3;
}
// Použití:
Nastaveni.MaxPokusu = 5;
```

---

### **7. Porovnání s poli**

| **Vlastnost**       | **Vlastnost**                  | **Pole**                   |  
|----------------------|--------------------------------|----------------------------|  
| **Validace**         | Ano (v setteru)                | Ne                          |  
| **Výpoèty**         | Ano (vypoèítané vlastnosti)    | Ne                          |  
| **Zapouzdøení**     | Ano                            | Ne (veøejná pole jsou riziková) |  

---

### **8. Bìžné chyby**

- **Zámìna s poli**: Vlastnosti by mìly být preferovány pro veøejný pøístup k datùm.  
- **Nekoneèná rekurze**:  
```csharp
public string Jmeno 
{
    get => Jmeno; // Chyba: getter volá sám sebe
}
```

- **Pøíliš složitá logika ve vlastnostech**: Vlastnosti by mìly být jednoduché (pro složitou logiku použijte metody).

---

### **9. Doporuèené postupy**

1. **Automatické vlastnosti**: Pro jednoduché pøípady bez logiky.  
2. **Minimalizujte veøejné settery**: Chraòte integritu dat.  
3. **Používejte `init` pro nemìnné objekty** (napø. DTO).  
4. **Vyhnìte se veøejným polím**: Vždy použijte vlastnosti.  

---

### **10. Ukázky kódu**

#### **Vlastnost s lazy loading**

```csharp
public class DataManager 
{
    private List<string> _data;
    public List<string> Data 
    {
        get 
        {
            _data ??= new List<string>(); // Inicializace pøi prvním pøístupu
            return _data;
        }
    }
}
```

#### **Vlastnost s podmínìným pøístupem**

```csharp
public class Uzivatel 
{
    private DateTime _registrace;
    
    public DateTime Registrace 
    {
        get => _registrace;
        set => _registrace = (value > DateTime.Now) ? DateTime.Now : value;
    }
}
```

---

### **11. Doporuèené zdroje**

- **Oficiální dokumentace**: [Properties (C#)](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/properties)  
