
### **1. Definice vlastnost�**

Vlastnosti (properties) jsou **�leny t��d**, kter� umo��uj� kontrolovan� p��stup k dat�m. Kombinuj� funk�nost metod s jednoduchou syntax� pol�. Slou�� k:
- **Zapouzd�en�**: Ochrana priv�tn�ch pol� p�ed neplatn�mi hodnotami.
- **Validaci**: Kontrola vstup� p�i �ten�/z�pisu.
- **V�po�t�m**: Dynamick� generov�n� hodnot na z�klad� jin�ch dat.

---

### **2. Z�kladn� syntaxe**

#### **a) Pln� syntaxe (s backing field)**

```csharp
public class Osoba 
{
    private string _jmeno; // Priv�tn� pole (backing field)

    public string Jmeno 
    {
        get { return _jmeno; } // �ten� hodnoty
        set { _jmeno = value ?? "Nezn�m�"; } // Z�pis s validac�
    }
}
```

#### **b) Automatick� vlastnosti (auto-implemented)**

```csharp
public class Ucet 
{
    public decimal Zustatek { get; set; } // Backing field generuje kompil�tor
}
```

---

### **3. Typy vlastnost�**

#### **a) Read-only vlastnost**  

- Lze ��st, ale nelze m�nit (m� pouze `get`).  
```csharp
public string ID { get; } = Guid.NewGuid().ToString();
```

#### **b) Write-only vlastnost**  

- Lze m�nit, ale nelze ��st (m� pouze `set`). Vz�cn�.  
```csharp
private string _heslo;
public string Heslo { set => _heslo = value; }
```

#### **c) Vypo��tan� vlastnost**  

- Hodnota se dynamicky po��t�.  
```csharp
public DateTime DatumNarozeni { get; set; }
public int Vek => DateTime.Now.Year - DatumNarozeni.Year; // Pouze get
```

#### **d) Init-only vlastnost (C# 9+)**  

- Lze nastavit **pouze p�i inicializaci objektu**.  
```csharp
public string Jmeno { get; init; }
// Pou�it�:
Osoba osoba = new Osoba { Jmeno = "Anna" }; // OK
// osoba.Jmeno = "Karel"; // Chyba!
```

#### **e) Indexery**  

- Umo��uj� p��stup k objektu jako k poli.  
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
// Pou�it�:
Seznam s = new Seznam();
s[0] = "Ahoj";
```

---

### **4. Modifik�tory p��stupu**

- **Vlastn� p��stup pro `get`/`set`**:  
```csharp
public string Email 
{
    get; 
    private set; // Zm�niteln� pouze uvnit� t��dy
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
                throw new ArgumentException("Z�statek nem��e b�t z�porn�.");
            _zustatek = value;
        }
    }
}
```

---

### **6. Statick� vlastnosti**

- Pat�� t��d�, ne instanci.  
```csharp
public class Nastaveni 
{
    public static int MaxPokusu { get; set; } = 3;
}
// Pou�it�:
Nastaveni.MaxPokusu = 5;
```

---

### **7. Porovn�n� s poli**

| **Vlastnost**       | **Vlastnost**                  | **Pole**                   |  
|----------------------|--------------------------------|----------------------------|  
| **Validace**         | Ano (v setteru)                | Ne                          |  
| **V�po�ty**         | Ano (vypo��tan� vlastnosti)    | Ne                          |  
| **Zapouzd�en�**     | Ano                            | Ne (ve�ejn� pole jsou rizikov�) |  

---

### **8. B�n� chyby**

- **Z�m�na s poli**: Vlastnosti by m�ly b�t preferov�ny pro ve�ejn� p��stup k dat�m.  
- **Nekone�n� rekurze**:  
```csharp
public string Jmeno 
{
    get => Jmeno; // Chyba: getter vol� s�m sebe
}
```

- **P��li� slo�it� logika ve vlastnostech**: Vlastnosti by m�ly b�t jednoduch� (pro slo�itou logiku pou�ijte metody).

---

### **9. Doporu�en� postupy**

1. **Automatick� vlastnosti**: Pro jednoduch� p��pady bez logiky.  
2. **Minimalizujte ve�ejn� settery**: Chra�te integritu dat.  
3. **Pou��vejte `init` pro nem�nn� objekty** (nap�. DTO).  
4. **Vyhn�te se ve�ejn�m pol�m**: V�dy pou�ijte vlastnosti.  

---

### **10. Uk�zky k�du**

#### **Vlastnost s lazy loading**

```csharp
public class DataManager 
{
    private List<string> _data;
    public List<string> Data 
    {
        get 
        {
            _data ??= new List<string>(); // Inicializace p�i prvn�m p��stupu
            return _data;
        }
    }
}
```

#### **Vlastnost s podm�n�n�m p��stupem**

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

### **11. Doporu�en� zdroje**

- **Ofici�ln� dokumentace**: [Properties (C#)](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/properties)  
