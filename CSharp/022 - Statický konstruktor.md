
### **1. Statický konstruktor**  

#### **Definice**  

- **Účel**: Inicializace **statických členů** třídy (pole, vlastnosti).  
- **Spuštění**: Automaticky **jednou při prvním použití třídy** (např. vytvoření instance, přístup ke statickému členu).  
- **Syntaxe**:  
  ```csharp
  public class Trida 
  {
      static Trida() 
      {
          // Inicializace statických členů
      }
  }
  ```

#### **Klíčové vlastnosti**  

- **Žádné parametry/modifikátory přístupu**: Nelze volat manuálně.  
- **Pořadí inicializace**:  
  - Pokud existuje jak statický, tak instanční konstruktor, **statický se spustí dříve**.  
- **Thread-safe**: CLR zajišťuje, že se spustí pouze jednou, i ve vícevláknovém prostředí.  

#### **Příklad**  

```csharp
public class Konfigurace 
{
    public static string ConnectionString;
    
    static Konfigurace() 
    {
        ConnectionString = "Server=..."; // Inicializace statického pole
    }
}

// Použití:
Console.WriteLine(Konfigurace.ConnectionString); // Spustí statický konstruktor
```

#### **Varování**  

- **Výjimky**: Pokud statický konstruktor vyvolá výjimku, třída je **nepoužitelná** po celou dobu života aplikace (v daném `AppDomain`).  
- **Optimalizace**: Nevkládejte časově náročný kód (zpomalí první přístup k třídě).  

---

### **2. Destruktory (Finalizéry)**  

#### **Definice**  

- **Účel**: Uvolnění **nemanaged resources** (např. soubory, síťová připojení) před uvolněním objektu z paměti.  
- **Spuštění**: Volá **Garbage Collector (GC)** před odstraněním objektu (není deterministické).  
- **Syntaxe**:  
  ```csharp
  public class Trida 
  {
      ~Trida() 
      {
          // Uvolnění prostředků
      }
  }
  ```

#### **Klíčové vlastnosti**  

- **Pouze pro instance**: Destruktory nelze definovat jako statické.  
- **Implementace**: Destruktor je převeden na metodu `Finalize()`, kterou volá GC.  
- **Dědičnost**: Pokud třída dědí, GC volá destruktor v opačném pořadí než konstruktory (od nejodvozenější k základní).  

#### **Příklad**  

```csharp
public class SouborovyHandler 
{
    private StreamReader _reader;
    
    public SouborovyHandler(string cesta) 
    {
        _reader = new StreamReader(cesta);
    }
    
    ~SouborovyHandler() 
    {
        _reader?.Dispose(); // Uvolnění prostředků
    }
}
```

#### **Omezení a doporučení**  

- **Nepoužívejte pro managed resources**: GC automaticky spravuje paměť (např. objekty v haldě).  
- **Preferujte `IDisposable`**: Pro deterministické uvolnění prostředků použijte `Dispose()` a `using`:  
  ```csharp
  public class SouborovyHandler : IDisposable 
  {
      private StreamReader _reader;
      
      public void Dispose() 
      {
          _reader?.Dispose();
          GC.SuppressFinalize(this); // Zabrání volání finalizéru
      }
  }

  // Použití:
  using (var handler = new SouborovyHandler("cesta")) 
  {
      // Práce se souborem
  } // Dispose() se zavře automaticky
  ```

---

### **3. Statický destruktor?**  

- **Neexistuje**: C# nepodporuje statické destruktory.  
- **Alternativa**: Pro uvolnění statických prostředků použijte:  
  - **Metodu `AppDomain.ProcessExit`**: Reakce na ukončení aplikace.  
  - **Manuální správu**: Např. `static void Cleanup()`.  

---

### **4. Shrnutí**  

| **Prvek**             | **Statický konstruktor**      | **Destruktor**               |  
|-----------------------|-------------------------------|------------------------------|  
| **Účel**              | Inicializace statických členů | Uvolnění nemanaged prostředků |  
| **Spuštění**          | Při prvním použití třídy      | Před uvolněním objektu (GC)  |  
| **Statický**          | Ano                           | Ne                           |  
| **Determinismus**     | Ano (jednou)                  | Ne (závisí na GC)            |  

---

### **5. Doporučené zdroje**  

- **Oficiální dokumentace**:  
  - [Static Constructors](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/static-constructors)  
  - [Finalizers](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/destructors)  
