
### **1. Definice statiky**  

Statické prvky patøí **typu samotnému**, nikoli jeho instancím. Jsou inicializovány jednou a sdíleny napøíè všemi instancemi.  
**Klíèové slovo**: `static`.

---

### **2. Typy statickıch prvkù**  

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

// Pouití:
new Pocitadlo();
new Pocitadlo();
Console.WriteLine(Pocitadlo.PocetInstanci); // 2
```

#### **b) Statická metoda (static method)**  

- Volána pøes název tøídy, nemá pøístup k instanèním èlenùm.  
```csharp
public class Matematika 
{
    public static double Umocni(double zaklad, int exponent) 
    {
        return Math.Pow(zaklad, exponent);
    }
}

// Pouití:
double vysledek = Matematika.Umocni(2, 3); // 8
```

#### **c) Statickı konstruktor (static constructor)**  

- Spustí se **jednou pøi prvním pouití tøídy**. Inicializuje statická pole.  
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

// Pouití:
Nastaveni.MaxPokusu = 5;
```

#### **e) Statická tøída (static class)**  

- Nelze vytvoøit instanci, obsahuje **pouze statické èleny**.  
```csharp
public static class Validator 
{
    public static bool JePlatnyEmail(string email) 
    {
        return email.Contains("@");
    }
}

// Pouití:
bool jePlatny = Validator.JePlatnyEmail("test@example.com");
```

---

### **3. Klíèové vlastnosti**  

- **Inicializace**: Statické prvky existují od prvního pouití tøídy a do konce programu.  
- **Pøístup**: Statické metody/vlastnosti nelze volat z instance (pouze pøes název tøídy).  
- **Dìdiènost**: Statické èleny se nedìdí, ale jsou pøístupné pøes název základní tøídy.  

---

### **4. Bìné pouití**  

- **Utility tøídy** (napø. `Math`, `Console`).  
- **Sdílené prostøedky** (napø. konfigurace, cache, logger).  
- **Singleton pattern** (zajištìní jediné instance):  
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

| **Kritérium**       | **Statické prvky**         | **Instanèní prvky**        |  
|----------------------|----------------------------|----------------------------|  
| **Pøíslušnost**      | Tøída                      | Instance                   |  
| **Pamì**            | Sdílená mezi všemi instancemi | Kadá instance má vlastní |  
| **Volání**           | `Trida.Metoda()`           | `instance.Metoda()`        |  

---

### **6. Èasté chyby a tipy**  

- **Thread-safety**: Statické prvky mohou zpùsobit problémy ve vícevláknovém prostøedí.  
- **Zneuití statiky**: Nepouívejte pro stavové objekty (napø. HTTP kontext).  
- **Statické using direktivy** (C# 6+):  
```csharp
using static System.Math; // Import statickıch metod
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

// Pouití:
Produkt p = Produkt.VytvorProdukt("Kniha");
```

#### **Statická tøída s rozšíøením (extension methods)**  

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

// Pouití:
string obraceny = "Ahoj".Obrat(); // "johA"
```

---

### **8. Doporuèené zdroje**  

- **Oficiální dokumentace**: [Static Classes and Members](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members)  
