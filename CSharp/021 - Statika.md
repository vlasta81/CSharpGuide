
### **1. Definice statiky**  

Statické prvky patří **typu samotnému**, nikoli jeho instancím. Jsou inicializovány jednou a sdíleny napříč všemi instancemi.  
**Klíčové slovo**: `static`.

---

### **2. Typy statických prvků**  

#### **a) Statické pole (static field)**  

- Sdílená hodnota pro všechny instance.  
```csharp
public class Pocitadlo 
{
    public static int PocetInstanci = 0;
    
    public Pocitadlo() 
    {
        PocetInstanci++;
    }
}

// Použití:
new Pocitadlo();
new Pocitadlo();
Console.WriteLine(Pocitadlo.PocetInstanci); // 2
```

#### **b) Statická metoda (static method)**  

- Volána přes název třídy, nemá přístup k instančním členům.  
```csharp
public class Matematika 
{
    public static double Umocni(double zaklad, int exponent) 
    {
        return Math.Pow(zaklad, exponent);
    }
}

// Použití:
double vysledek = Matematika.Umocni(2, 3); // 8
```

#### **c) Statický konstruktor (static constructor)**  

- Spustí se **jednou při prvním použití třídy**. Inicializuje statická pole.  
```csharp
public class Konfigurace 
{
    public static string ConnectionString;
    
    static Konfigurace() 
    {
        ConnectionString = "Server=...";
    }
}
```

#### **d) Statická vlastnost (static property)**  

- Sdílená vlastnost s gettery/settery.  
```csharp
public class Nastaveni 
{
    private static int _maxPokusu = 3;
    
    public static int MaxPokusu 
    {
        get => _maxPokusu;
        set => _maxPokusu = value > 0 ? value : 3;
    }
}

// Použití:
Nastaveni.MaxPokusu = 5;
```

#### **e) Statická třída (static class)**  

- Nelze vytvořit instanci, obsahuje **pouze statické členy**.  
```csharp
public static class Validator 
{
    public static bool JePlatnyEmail(string email) 
    {
        return email.Contains("@");
    }
}

// Použití:
bool jePlatny = Validator.JePlatnyEmail("test@example.com");
```

---

### **3. Klíčové vlastnosti**  

- **Inicializace**: Statické prvky existují od prvního použití třídy až do konce programu.  
- **Přístup**: Statické metody/vlastnosti nelze volat z instance (pouze přes název třídy).  
- **Dědičnost**: Statické členy se nedědí, ale jsou přístupné přes název základní třídy.  

---

### **4. Běžné použití**  

- **Utility třídy** (např. `Math`, `Console`).  
- **Sdílené prostředky** (např. konfigurace, cache, logger).  
- **Singleton pattern** (zajištění jediné instance):  
```csharp
public class Singleton 
{
    private static Singleton _instance;
    
    public static Singleton Instance => _instance ??= new Singleton();
    
    private Singleton() { }
}
```

---

### **5. Statika vs. instance**  

| **Kritérium**       | **Statické prvky**         | **Instanční prvky**        |  
|----------------------|----------------------------|----------------------------|  
| **Příslušnost**      | Třída                      | Instance                   |  
| **Paměť**            | Sdílená mezi všemi instancemi | Každá instance má vlastní |  
| **Volání**           | `Trida.Metoda()`           | `instance.Metoda()`        |  

---

### **6. Časté chyby a tipy**  

- **Thread-safety**: Statické prvky mohou způsobit problémy ve vícevláknovém prostředí.  
- **Zneužití statiky**: Nepoužívejte pro stavové objekty (např. HTTP kontext).  
- **Statické using direktivy** (C# 6+):  
```csharp
using static System.Math; // Import statických metod
double hodnota = Sqrt(16); // 4
```

---

### **7. Ukázky kódu**  

#### **Statická tovární metoda**  

```csharp
public class Produkt 
{
    public string Nazev { get; }
    
    private Produkt(string nazev) 
    {
        Nazev = nazev;
    }
    
    public static Produkt VytvorProdukt(string nazev) 
    {
        return new Produkt(nazev);
    }
}

// Použití:
Produkt p = Produkt.VytvorProdukt("Kniha");
```

#### **Statická třída s rozšířením (extension methods)**  

```csharp
public static class StringExtensions 
{
    public static string Obrat(this string text) 
    {
        char[] znaky = text.ToCharArray();
        Array.Reverse(znaky);
        return new string(znaky);
    }
}

// Použití:
string obraceny = "Ahoj".Obrat(); // "johA"
```

---

### **8. Doporučené zdroje**  

- **Oficiální dokumentace**: [Static Classes and Members](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members)  
