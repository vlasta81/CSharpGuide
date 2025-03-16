
### **1. Definice statiky**  

Statick� prvky pat�� **typu samotn�mu**, nikoli jeho instanc�m. Jsou inicializov�ny jednou a sd�leny nap��� v�emi instancemi.  
**Kl��ov� slovo**: `static`.

---

### **2. Typy statick�ch prvk�**  

#### **a) Statick� pole (static field)**  

- Sd�len� hodnota pro v�echny instance.  
```csharp
public class Pocitadlo 
{
    public static int PocetInstanci = 0;
    
    public Pocitadlo() 
    {
        PocetInstanci++;
    }
}

// Pou�it�:
new Pocitadlo();
new Pocitadlo();
Console.WriteLine(Pocitadlo.PocetInstanci); // 2
```

#### **b) Statick� metoda (static method)**  

- Vol�na p�es n�zev t��dy, nem� p��stup k instan�n�m �len�m.  
```csharp
public class Matematika 
{
    public static double Umocni(double zaklad, int exponent) 
    {
        return Math.Pow(zaklad, exponent);
    }
}

// Pou�it�:
double vysledek = Matematika.Umocni(2, 3); // 8
```

#### **c) Statick� konstruktor (static constructor)**  

- Spust� se **jednou p�i prvn�m pou�it� t��dy**. Inicializuje statick� pole.  
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

#### **d) Statick� vlastnost (static property)**  

- Sd�len� vlastnost s gettery/settery.  
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

// Pou�it�:
Nastaveni.MaxPokusu = 5;
```

#### **e) Statick� t��da (static class)**  

- Nelze vytvo�it instanci, obsahuje **pouze statick� �leny**.  
```csharp
public static class Validator 
{
    public static bool JePlatnyEmail(string email) 
    {
        return email.Contains("@");
    }
}

// Pou�it�:
bool jePlatny = Validator.JePlatnyEmail("test@example.com");
```

---

### **3. Kl��ov� vlastnosti**  

- **Inicializace**: Statick� prvky existuj� od prvn�ho pou�it� t��dy a� do konce programu.  
- **P��stup**: Statick� metody/vlastnosti nelze volat z instance (pouze p�es n�zev t��dy).  
- **D�di�nost**: Statick� �leny se ned�d�, ale jsou p��stupn� p�es n�zev z�kladn� t��dy.  

---

### **4. B�n� pou�it�**  

- **Utility t��dy** (nap�. `Math`, `Console`).  
- **Sd�len� prost�edky** (nap�. konfigurace, cache, logger).  
- **Singleton pattern** (zaji�t�n� jedin� instance):  
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

| **Krit�rium**       | **Statick� prvky**         | **Instan�n� prvky**        |  
|----------------------|----------------------------|----------------------------|  
| **P��slu�nost**      | T��da                      | Instance                   |  
| **Pam�**            | Sd�len� mezi v�emi instancemi | Ka�d� instance m� vlastn� |  
| **Vol�n�**           | `Trida.Metoda()`           | `instance.Metoda()`        |  

---

### **6. �ast� chyby a tipy**  

- **Thread-safety**: Statick� prvky mohou zp�sobit probl�my ve v�cevl�knov�m prost�ed�.  
- **Zneu�it� statiky**: Nepou��vejte pro stavov� objekty (nap�. HTTP kontext).  
- **Statick� using direktivy** (C# 6+):  
```csharp
using static System.Math; // Import statick�ch metod
double hodnota = Sqrt(16); // 4
```

---

### **7. Uk�zky k�du**  

#### **Statick� tov�rn� metoda**  

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

// Pou�it�:
Produkt p = Produkt.VytvorProdukt("Kniha");
```

#### **Statick� t��da s roz���en�m (extension methods)**  

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

// Pou�it�:
string obraceny = "Ahoj".Obrat(); // "johA"
```

---

### **8. Doporu�en� zdroje**  

- **Ofici�ln� dokumentace**: [Static Classes and Members](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/static-classes-and-static-class-members)  
