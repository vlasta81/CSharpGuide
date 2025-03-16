
### **1. Definice rozhraní**  

- **Rozhraní** definuje **kontrakt**, který tøídy nebo struktury musí implementovat.  
- **Obsahuje pouze deklarace** metod, vlastností, událostí nebo indexerù (bez implementace).  
- **Klíèový úèel**: Abstrakce, polymorfismus a podpora více "dìdièností" (C# nepodporuje vícenásobnou dìdiènost tøíd).

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

### **3. Klíèové vlastnosti**  

#### **a) Vícenásobná implementace**  

- Tøída mùže implementovat **více rozhraní** najednou.  
```csharp
public class Superman : ILetajici, ISilny 
{
    // Implementace obou rozhraní
}
```

#### **b) Explicitní implementace**  

- Øeší kolize názvù mezi rozhraními.  
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

- Rozhraní mùže obsahovat **defaultní implementace** metod.  
```csharp
public interface ILogger 
{
    void Log(string message) => Console.WriteLine(message); // Výchozí kód
}
```

---

### **4. Dìdiènost rozhraní**  

- Rozhraní mùže dìdit od jiných rozhraní.  
```csharp
public interface IUlozitelny : ISerializable, IExportovatelny 
{
    void UlozDoSouboru(string cesta);
}
```

---

### **5. Kdy použít rozhraní?**  

1. **Definice kontraktu**: Vynucení spoleèného chování pro rùzné tøídy.  
2. **Dependency Injection**: Vytváøení volnì provázaných komponent.  
3. **Testování**: Mockování rozhraní pro unit testy.  
4. **Plug-in architektury**: Rozšiøitelnost pomocí externích implementací.

---

### **6. Porovnání s abstraktními tøídami**  

| **Vlastnost**          | **Rozhraní**               | **Abstraktní tøída**       |  
|-------------------------|----------------------------|----------------------------|  
| **Implementace kódu**   | Pouze výchozí metody (C#8+) | Ano (èásteèná implementace) |  
| **Dìdiènost**           | Více rozhraní              | Jedna tøída                |  
| **Pole/konstruktory**   | Ne                         | Ano                        |  
| **Použití**             | Kontrakt, polymorfismus    | Sdílení kódu a logiky      |  

---

### **7. Bìžné chyby a tipy**  

- **Zapomenutá implementace èlena**: Tøída musí implementovat všechny èleny rozhraní.  
- **Nadbyteèná rozhraní**: Dodržujte **Interface Segregation Principle** (ISP) – malá, specializovaná rozhraní.  
- **Výbìr názvù**: Zaèínají velkým písmenem **I** (napø. `IComparable`).  

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
    public void Kresli() => Console.WriteLine("Kresba štìtcem");
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

### **9. Doporuèené zdroje** 

- **Oficiální dokumentace**: [Interfaces in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/keywords/interface)  
