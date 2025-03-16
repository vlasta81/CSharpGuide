
### **1. Statickı konstruktor**  

#### **Definice**  

- **Úèel**: Inicializace **statickıch èlenù** tøídy (pole, vlastnosti).  
- **Spuštìní**: Automaticky **jednou pøi prvním pouití tøídy** (napø. vytvoøení instance, pøístup ke statickému èlenu).  
- **Syntaxe**:  
  ```csharp
  public class Trida 
  {
      static Trida() 
      {
          // Inicializace statickıch èlenù
      }
  }
  ```

#### **Klíèové vlastnosti**  

- **ádné parametry/modifikátory pøístupu**: Nelze volat manuálnì.  
- **Poøadí inicializace**:  
  - Pokud existuje jak statickı, tak instanèní konstruktor, **statickı se spustí døíve**.  
- **Thread-safe**: CLR zajišuje, e se spustí pouze jednou, i ve vícevláknovém prostøedí.  

#### **Pøíklad**  

```csharp
public class Konfigurace 
{
    public static string ConnectionString;
    
    static Konfigurace() 
    {
        ConnectionString = "Server=..."; // Inicializace statického pole
    }
}

// Pouití:
Console.WriteLine(Konfigurace.ConnectionString); // Spustí statickı konstruktor
```

#### **Varování**  

- **Vıjimky**: Pokud statickı konstruktor vyvolá vıjimku, tøída je **nepouitelná** po celou dobu ivota aplikace (v daném `AppDomain`).  
- **Optimalizace**: Nevkládejte èasovì nároènı kód (zpomalí první pøístup k tøídì).  

---

### **2. Destruktory (Finalizéry)**  

#### **Definice**  

- **Úèel**: Uvolnìní **nemanaged resources** (napø. soubory, síová pøipojení) pøed uvolnìním objektu z pamìti.  
- **Spuštìní**: Volá **Garbage Collector (GC)** pøed odstranìním objektu (není deterministické).  
- **Syntaxe**:  
  ```csharp
  public class Trida 
  {
      ~Trida() 
      {
          // Uvolnìní prostøedkù
      }
  }
  ```

#### **Klíèové vlastnosti**  

- **Pouze pro instance**: Destruktory nelze definovat jako statické.  
- **Implementace**: Destruktor je pøeveden na metodu `Finalize()`, kterou volá GC.  
- **Dìdiènost**: Pokud tøída dìdí, GC volá destruktor v opaèném poøadí ne konstruktory (od nejodvozenìjší k základní).  

#### **Pøíklad**  

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
        _reader?.Dispose(); // Uvolnìní prostøedkù
    }
}
```

#### **Omezení a doporuèení**  

- **Nepouívejte pro managed resources**: GC automaticky spravuje pamì (napø. objekty v haldì).  
- **Preferujte `IDisposable`**: Pro deterministické uvolnìní prostøedkù pouijte `Dispose()` a `using`:  
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

  // Pouití:
  using (var handler = new SouborovyHandler("cesta")) 
  {
      // Práce se souborem
  } // Dispose() se zavøe automaticky
  ```

---

### **3. Statickı destruktor?**  

- **Neexistuje**: C# nepodporuje statické destruktory.  
- **Alternativa**: Pro uvolnìní statickıch prostøedkù pouijte:  
  - **Metodu `AppDomain.ProcessExit`**: Reakce na ukonèení aplikace.  
  - **Manuální správu**: Napø. `static void Cleanup()`.  

---

### **4. Shrnutí**  

| **Prvek**             | **Statickı konstruktor**      | **Destruktor**               |  
|-----------------------|-------------------------------|------------------------------|  
| **Úèel**              | Inicializace statickıch èlenù | Uvolnìní nemanaged prostøedkù |  
| **Spuštìní**          | Pøi prvním pouití tøídy      | Pøed uvolnìním objektu (GC)  |  
| **Statickı**          | Ano                           | Ne                           |  
| **Determinismus**     | Ano (jednou)                  | Ne (závisí na GC)            |  

---

### **5. Doporuèené zdroje**  

- **Oficiální dokumentace**:  
  - [Static Constructors](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/static-constructors)  
  - [Finalizers](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/destructors)  
