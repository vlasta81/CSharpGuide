
### **1. Definice rozhran�**  

- **Rozhran�** definuje **kontrakt**, kter� t��dy nebo struktury mus� implementovat.  
- **Obsahuje pouze deklarace** metod, vlastnost�, ud�lost� nebo indexer� (bez implementace).  
- **Kl��ov� ��el**: Abstrakce, polymorfismus a podpora v�ce "d�di�nost�" (C# nepodporuje v�cen�sobnou d�di�nost t��d).

---

### **2. Syntaxe a z�kladn� pou�it�**  

#### **a) Deklarace rozhran�**  

```csharp
public interface ILetajici 
{
    void Let(); // Metoda bez implementace
    int MaxVyska { get; } // Vlastnost
}
```

#### **b) Implementace rozhran�**  

```csharp
public class Ptak : ILetajici 
{
    public int MaxVyska { get; } = 1000;
    
    public void Let() 
    {
        Console.WriteLine("Pt�k let�");
    }
}
```

---

### **3. Kl��ov� vlastnosti**  

#### **a) V�cen�sobn� implementace**  

- T��da m��e implementovat **v�ce rozhran�** najednou.  
```csharp
public class Superman : ILetajici, ISilny 
{
    // Implementace obou rozhran�
}
```

#### **b) Explicitn� implementace**  

- �e�� kolize n�zv� mezi rozhran�mi.  
```csharp
public class Hybrid : ILetajici, IPlyvajici 
{
    void ILetajici.Let() { /* Let */ }
    void IPlyvajici.Let() { /* Plavba */ }
}

// Pou�it�:
Hybrid hybrid = new Hybrid();
((ILetajici)hybrid).Let(); // Vol� metodu z ILetajici
```

#### **c) V�choz� implementace (C# 8+)**  

- Rozhran� m��e obsahovat **defaultn� implementace** metod.  
```csharp
public interface ILogger 
{
    void Log(string message) => Console.WriteLine(message); // V�choz� k�d
}
```

---

### **4. D�di�nost rozhran�**  

- Rozhran� m��e d�dit od jin�ch rozhran�.  
```csharp
public interface IUlozitelny : ISerializable, IExportovatelny 
{
    void UlozDoSouboru(string cesta);
}
```

---

### **5. Kdy pou��t rozhran�?**  

1. **Definice kontraktu**: Vynucen� spole�n�ho chov�n� pro r�zn� t��dy.  
2. **Dependency Injection**: Vytv��en� voln� prov�zan�ch komponent.  
3. **Testov�n�**: Mockov�n� rozhran� pro unit testy.  
4. **Plug-in architektury**: Roz�i�itelnost pomoc� extern�ch implementac�.

---

### **6. Porovn�n� s abstraktn�mi t��dami**  

| **Vlastnost**          | **Rozhran�**               | **Abstraktn� t��da**       |  
|-------------------------|----------------------------|----------------------------|  
| **Implementace k�du**   | Pouze v�choz� metody (C#8+) | Ano (��ste�n� implementace) |  
| **D�di�nost**           | V�ce rozhran�              | Jedna t��da                |  
| **Pole/konstruktory**   | Ne                         | Ano                        |  
| **Pou�it�**             | Kontrakt, polymorfismus    | Sd�len� k�du a logiky      |  

---

### **7. B�n� chyby a tipy**  

- **Zapomenut� implementace �lena**: T��da mus� implementovat v�echny �leny rozhran�.  
- **Nadbyte�n� rozhran�**: Dodr�ujte **Interface Segregation Principle** (ISP) � mal�, specializovan� rozhran�.  
- **V�b�r n�zv�**: Za��naj� velk�m p�smenem **I** (nap�. `IComparable`).  

---

### **8. Uk�zky k�du**  

#### **Rozhran� s v�choz� implementac�**  

```csharp
public interface IKreslici 
{
    void Kresli();
    void Reset() => Console.WriteLine("Resetov�n� n�stroje"); // V�choz� k�d
}

public class Stetec : IKreslici 
{
    public void Kresli() => Console.WriteLine("Kresba �t�tcem");
    // Reset() nen� nutn� implementovat � pou�ije se v�choz�
}
```

#### **Dependency Injection s rozhran�m**  

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

### **9. Doporu�en� zdroje** 

- **Ofici�ln� dokumentace**: [Interfaces in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/keywords/interface)  
