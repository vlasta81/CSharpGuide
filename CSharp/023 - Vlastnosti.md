
### **1. Definice vlastností**

Vlastnosti (properties) jsou **členy tříd**, které umožňují kontrolovaný přístup k datům. Kombinují funkčnost metod s jednoduchou syntaxí polí. Slouží k:
- **Zapouzdření**: Ochrana privátních polí před neplatnými hodnotami.
- **Validaci**: Kontrola vstupů při čtení/zápisu.
- **Výpočtům**: Dynamické generování hodnot na základě jiných dat.

---

### **2. Základní syntaxe**

#### **a) Plná syntaxe (s backing field)**

```csharp
public class Osoba 
{
    private string _jmeno; // Privátní pole (backing field)

    public string Jmeno 
    {
        get { return _jmeno; } // Čtení hodnoty
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

- Lze číst, ale nelze měnit (má pouze `get`).  
```csharp
public string ID { get; } = Guid.NewGuid().ToString();
```

#### **b) Write-only vlastnost**  

- Lze měnit, ale nelze číst (má pouze `set`). Vzácné.  
```csharp
private string _heslo;
public string Heslo { set => _heslo = value; }
```

#### **c) Vypočítaná vlastnost**  

- Hodnota se dynamicky počítá.  
```csharp
public DateTime DatumNarozeni { get; set; }
public int Vek => DateTime.Now.Year - DatumNarozeni.Year; // Pouze get
```

#### **d) Init-only vlastnost (C# 9+)**  

- Lze nastavit **pouze při inicializaci objektu**.  
```csharp
public string Jmeno { get; init; }
// Použití:
Osoba osoba = new Osoba { Jmeno = "Anna" }; // OK
// osoba.Jmeno = "Karel"; // Chyba!
```

#### **e) Indexery**  

- Umožňují přístup k objektu jako k poli.  
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

### **4. Modifikátory přístupu**

- **Vlastní přístup pro `get`/`set`**:  
```csharp
public string Email 
{
    get; 
    private set; // Změnitelný pouze uvnitř třídy
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
                throw new ArgumentException("Zůstatek nemůže být záporný.");
            _zustatek = value;
        }
    }
}
```

---

### **6. Statické vlastnosti**

- Patří třídě, ne instanci.  
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
| **Výpočty**         | Ano (vypočítané vlastnosti)    | Ne                          |  
| **Zapouzdření**     | Ano                            | Ne (veřejná pole jsou riziková) |  

---

### **8. Běžné chyby**

- **Záměna s poli**: Vlastnosti by měly být preferovány pro veřejný přístup k datům.  
- **Nekonečná rekurze**:  
```csharp
public string Jmeno 
{
    get => Jmeno; // Chyba: getter volá sám sebe
}
```

- **Příliš složitá logika ve vlastnostech**: Vlastnosti by měly být jednoduché (pro složitou logiku použijte metody).

---

### **9. Doporučené postupy**

1. **Automatické vlastnosti**: Pro jednoduché případy bez logiky.  
2. **Minimalizujte veřejné settery**: Chraňte integritu dat.  
3. **Používejte `init` pro neměnné objekty** (např. DTO).  
4. **Vyhněte se veřejným polím**: Vždy použijte vlastnosti.  

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
            _data ??= new List<string>(); // Inicializace při prvním přístupu
            return _data;
        }
    }
}
```

#### **Vlastnost s podmíněným přístupem**

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

### **11. Doporučené zdroje**

- **Oficiální dokumentace**: [Properties (C#)](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/properties)  
