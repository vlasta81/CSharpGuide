
### **1. Definice getterù a setterù**  

Getters (`get`) a setters (`set`) jsou **pøístupové metody vlastností**, které umožòují:  
- **Ètení hodnoty** (`get`).  
- **Zápis hodnoty** (`set`).  
Slouží k zapouzdøení dat a kontrole pøístupu k privátním polím tøíd.

---

### **2. Základní syntaxe**  

```csharp
public class Osoba 
{
    private string _jmeno; // Privátní pole (backing field)

    // Vlastnost s getterem a setterem
    public string Jmeno 
    {
        get { return _jmeno; } // Getter
        set { _jmeno = value; } // Setter (value = nová hodnota)
    }
}
```

---

### **3. Typy pøístupových metod**  

#### **a) Automatické vlastnosti (auto-implemented)**  

- Kompilátor generuje backing field.  
```csharp
public int Vek { get; set; } // Implicitní get a set
```

#### **b) Read-only vlastnost**  

- Pouze getter (hodnota se nastavuje v konstruktoru nebo inicializátoru).  
```csharp
public string ID { get; } = Guid.NewGuid().ToString();
```

#### **c) Private setter**  

- Zápis možný pouze uvnitø tøídy.  
```csharp
public string Email { get; private set; }
```

#### **d) Init-only setter (C# 9+)**  

- Hodnotu lze nastavit **pouze pøi inicializaci objektu**.  
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
            throw new ArgumentException("Neplatný vìk");
        _vek = value;
    }
}
```

---

### **5. Expression-bodied èleny (C# 7+)**  

- Zjednodušená syntaxe pro jednoduché gettery/settery.  
```csharp
public string CeleJmeno => $"{Jmeno} {Prijmeni}"; // Get-only
private string _heslo;
public string Heslo { get => _heslo; set => _heslo = value ?? ""; }
```

---

### **6. Kdy použít gettery/settery vs. metody**  

| **Kritérium**       | **Vlastnosti (get/set)**        | **Metody**                |  
|----------------------|---------------------------------|---------------------------|  
| **Úèel**             | Pøístup k datùm                 | Provádìní operací         |  
| **Èitelnost**        | `obj.Hodnota = 5`               | `obj.NastavHodnotu(5)`    |  
| **Složitost**        | Jednoduchá logika               | Složitá logika            |  

---

### **7. Bìžné chyby**  

- **Nekoneèná rekurze**:  
```csharp
public string Jmeno 
{
    get => Jmeno; // Chyba: getter volá sám sebe!
    set => Jmeno = value; 
}
```

- **Zapomenutá validace**:  
```csharp
public decimal Zustatek { get; set; } // Riziko záporné hodnoty!
```

---

### **8. Doporuèené postupy**  

1. **Zapouzdøujte data**: Vždy používejte vlastnosti místo veøejných polí.  
2. **Minimalizujte veøejné settery**: Chraòte integritu dat.  
3. **Používejte `init` pro nemìnné objekty** (napø. DTO, konfigurace).  
4. **Vyhnìte se složité logice ve vlastnostech**: Pro složité operace použijte metody.  

---

### **9. Ukázky kódu**  
#### **Vlastnost s podmínìným pøístupem**  

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
            _data ??= new List<string>(); // Inicializace pøi prvním pøístupu
            return _data;
        }
    }
}
```

---

### **10. Doporuèené zdroje** 

- **Oficiální dokumentace**: [Properties (C#)](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/properties)  
