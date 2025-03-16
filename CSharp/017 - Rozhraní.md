
### **1. Definice rozhraní**  

- **Rozhraní** definuje **kontrakt**, který třídy nebo struktury musí implementovat.  
- **Obsahuje pouze deklarace** metod, vlastností, událostí nebo indexerů (bez implementace).  
- **Klíčový účel**: Abstrakce, polymorfismus a podpora více "dědičností" (C# nepodporuje vícenásobnou dědičnost tříd).

---

### **2. Syntaxe a základní použití**  

#### **a) Deklarace rozhraní**  

```csharp
public interface ILetajici 
{
    void Let(); // Metoda bez implementace
    int MaxVyska { get; } // Vlastnost
}
```

#### **b) Implementace rozhraní**  

```csharp
public class Ptak : ILetajici 
{
    public int MaxVyska { get; } = 1000;
    
    public void Let() 
    {
        Console.WriteLine("Pták letí");
    }
}
```

---

### **3. Klíčové vlastnosti**  

#### **a) Vícenásobná implementace**  

- Třída může implementovat **více rozhraní** najednou.  
```csharp
public class Superman : ILetajici, ISilny 
{
    // Implementace obou rozhraní
}
```

#### **b) Explicitní implementace**  

- Řeší kolize názvů mezi rozhraními.  
```csharp
public class Hybrid : ILetajici, IPlyvajici 
{
    void ILetajici.Let() { /* Let */ }
    void IPlyvajici.Let() { /* Plavba */ }
}

// Použití:
Hybrid hybrid = new Hybrid();
((ILetajici)hybrid).Let(); // Volá metodu z ILetajici
```

#### **c) Výchozí implementace (C# 8+)**  

- Rozhraní může obsahovat **defaultní implementace** metod.  
```csharp
public interface ILogger 
{
    void Log(string message) => Console.WriteLine(message); // Výchozí kód
}
```

---

### **4. Dědičnost rozhraní**  

- Rozhraní může dědit od jiných rozhraní.  
```csharp
public interface IUlozitelny : ISerializable, IExportovatelny 
{
    void UlozDoSouboru(string cesta);
}
```

---

### **5. Kdy použít rozhraní?**  

1. **Definice kontraktu**: Vynucení společného chování pro různé třídy.  
2. **Dependency Injection**: Vytváření volně provázaných komponent.  
3. **Testování**: Mockování rozhraní pro unit testy.  
4. **Plug-in architektury**: Rozšiřitelnost pomocí externích implementací.

---

### **6. Porovnání s abstraktními třídami**  

| **Vlastnost**          | **Rozhraní**               | **Abstraktní třída**       |  
|-------------------------|----------------------------|----------------------------|  
| **Implementace kódu**   | Pouze výchozí metody (C#8+) | Ano (částečná implementace) |  
| **Dědičnost**           | Více rozhraní              | Jedna třída                |  
| **Pole/konstruktory**   | Ne                         | Ano                        |  
| **Použití**             | Kontrakt, polymorfismus    | Sdílení kódu a logiky      |  

---

### **7. Běžné chyby a tipy**  

- **Zapomenutá implementace člena**: Třída musí implementovat všechny členy rozhraní.  
- **Nadbytečná rozhraní**: Dodržujte **Interface Segregation Principle** (ISP) – malá, specializovaná rozhraní.  
- **Výběr názvů**: Začínají velkým písmenem **I** (např. `IComparable`).  

---

### **8. Ukázky kódu**  

#### **Rozhraní s výchozí implementací**  

```csharp
public interface IKreslici 
{
    void Kresli();
    void Reset() => Console.WriteLine("Resetování nástroje"); // Výchozí kód
}

public class Stetec : IKreslici 
{
    public void Kresli() => Console.WriteLine("Kresba štětcem");
    // Reset() není nutné implementovat – použije se výchozí
}
```

#### **Dependency Injection s rozhraním**  

```csharp
public interface IEmailService 
{
    void SendEmail(string to, string message);
}

public class SmtpEmailService : IEmailService 
{
    public void SendEmail(string to, string message) { /* ... */ }
}

public class UserService 
{
    private readonly IEmailService _emailService;
    
    public UserService(IEmailService emailService) 
    {
        _emailService = emailService;
    }
}
```

---

### **9. Doporučené zdroje** 

- **Oficiální dokumentace**: [Interfaces in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/keywords/interface)  
