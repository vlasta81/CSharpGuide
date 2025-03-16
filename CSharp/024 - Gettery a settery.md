
### **1. Definice getter� a setter�**  

Getters (`get`) a setters (`set`) jsou **p��stupov� metody vlastnost�**, kter� umo��uj�:  
- **�ten� hodnoty** (`get`).  
- **Z�pis hodnoty** (`set`).  
Slou�� k zapouzd�en� dat a kontrole p��stupu k priv�tn�m pol�m t��d.

---

### **2. Z�kladn� syntaxe**  

```csharp
public class Osoba 
{
    private string _jmeno; // Priv�tn� pole (backing field)

    // Vlastnost s getterem a setterem
    public string Jmeno 
    {
        get { return _jmeno; } // Getter
        set { _jmeno = value; } // Setter (value = nov� hodnota)
    }
}
```

---

### **3. Typy p��stupov�ch metod**  

#### **a) Automatick� vlastnosti (auto-implemented)**  

- Kompil�tor generuje backing field.  
```csharp
public int Vek { get; set; } // Implicitn� get a set
```

#### **b) Read-only vlastnost**  

- Pouze getter (hodnota se nastavuje v konstruktoru nebo inicializ�toru).  
```csharp
public string ID { get; } = Guid.NewGuid().ToString();
```

#### **c) Private setter**  

- Z�pis mo�n� pouze uvnit� t��dy.  
```csharp
public string Email { get; private set; }
```

#### **d) Init-only setter (C# 9+)**  

- Hodnotu lze nastavit **pouze p�i inicializaci objektu**.  
```csharp
public string Jmeno { get; init; }
```

---

### **4. Validace a logika v setteru**  

```csharp
private int _vek;
public int Vek 
{
    get => _vek;
    set 
    {
        if (value < 0 || value > 150)
            throw new ArgumentException("Neplatn� v�k");
        _vek = value;
    }
}
```

---

### **5. Expression-bodied �leny (C# 7+)**  

- Zjednodu�en� syntaxe pro jednoduch� gettery/settery.  
```csharp
public string CeleJmeno => $"{Jmeno} {Prijmeni}"; // Get-only
private string _heslo;
public string Heslo { get => _heslo; set => _heslo = value ?? ""; }
```

---

### **6. Kdy pou��t gettery/settery vs. metody**  

| **Krit�rium**       | **Vlastnosti (get/set)**        | **Metody**                |  
|----------------------|---------------------------------|---------------------------|  
| **��el**             | P��stup k dat�m                 | Prov�d�n� operac�         |  
| **�itelnost**        | `obj.Hodnota = 5`               | `obj.NastavHodnotu(5)`    |  
| **Slo�itost**        | Jednoduch� logika               | Slo�it� logika            |  

---

### **7. B�n� chyby**  

- **Nekone�n� rekurze**:  
```csharp
public string Jmeno 
{
    get => Jmeno; // Chyba: getter vol� s�m sebe!
    set => Jmeno = value; 
}
```

- **Zapomenut� validace**:  
```csharp
public decimal Zustatek { get; set; } // Riziko z�porn� hodnoty!
```

---

### **8. Doporu�en� postupy**  

1. **Zapouzd�ujte data**: V�dy pou��vejte vlastnosti m�sto ve�ejn�ch pol�.  
2. **Minimalizujte ve�ejn� settery**: Chra�te integritu dat.  
3. **Pou��vejte `init` pro nem�nn� objekty** (nap�. DTO, konfigurace).  
4. **Vyhn�te se slo�it� logice ve vlastnostech**: Pro slo�it� operace pou�ijte metody.  

---

### **9. Uk�zky k�du**  
#### **Vlastnost s podm�n�n�m p��stupem**  

```csharp
public class Ucet 
{
    private decimal _zustatek;
    
    public decimal Zustatek 
    {
        get => _zustatek;
        set => _zustatek = value >= 0 ? value : 0; // Validace
    }
}
```

#### **Vlastnost s lazy loading**  

```csharp
public class Cache 
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

---

### **10. Doporu�en� zdroje** 

- **Ofici�ln� dokumentace**: [Properties (C#)](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/properties)  
