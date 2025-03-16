
### **1. Statick� konstruktor**  

#### **Definice**  

- **��el**: Inicializace **statick�ch �len�** t��dy (pole, vlastnosti).  
- **Spu�t�n�**: Automaticky **jednou p�i prvn�m pou�it� t��dy** (nap�. vytvo�en� instance, p��stup ke statick�mu �lenu).  
- **Syntaxe**:  
  ```csharp
  public class Trida 
  {
      static Trida() 
      {
          // Inicializace statick�ch �len�
      }
  }
  ```

#### **Kl��ov� vlastnosti**  

- **��dn� parametry/modifik�tory p��stupu**: Nelze volat manu�ln�.  
- **Po�ad� inicializace**:  
  - Pokud existuje jak statick�, tak instan�n� konstruktor, **statick� se spust� d��ve**.  
- **Thread-safe**: CLR zaji��uje, �e se spust� pouze jednou, i ve v�cevl�knov�m prost�ed�.  

#### **P��klad**  

```csharp
public class Konfigurace 
{
    public static string ConnectionString;
    
    static Konfigurace() 
    {
        ConnectionString = "Server=..."; // Inicializace statick�ho pole
    }
}

// Pou�it�:
Console.WriteLine(Konfigurace.ConnectionString); // Spust� statick� konstruktor
```

#### **Varov�n�**  

- **V�jimky**: Pokud statick� konstruktor vyvol� v�jimku, t��da je **nepou�iteln�** po celou dobu �ivota aplikace (v dan�m `AppDomain`).  
- **Optimalizace**: Nevkl�dejte �asov� n�ro�n� k�d (zpomal� prvn� p��stup k t��d�).  

---

### **2. Destruktory (Finaliz�ry)**  

#### **Definice**  

- **��el**: Uvoln�n� **nemanaged resources** (nap�. soubory, s�ov� p�ipojen�) p�ed uvoln�n�m objektu z pam�ti.  
- **Spu�t�n�**: Vol� **Garbage Collector (GC)** p�ed odstran�n�m objektu (nen� deterministick�).  
- **Syntaxe**:  
  ```csharp
  public class Trida 
  {
      ~Trida() 
      {
          // Uvoln�n� prost�edk�
      }
  }
  ```

#### **Kl��ov� vlastnosti**  

- **Pouze pro instance**: Destruktory nelze definovat jako statick�.  
- **Implementace**: Destruktor je p�eveden na metodu `Finalize()`, kterou vol� GC.  
- **D�di�nost**: Pokud t��da d�d�, GC vol� destruktor v opa�n�m po�ad� ne� konstruktory (od nejodvozen�j�� k z�kladn�).  

#### **P��klad**  

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
        _reader?.Dispose(); // Uvoln�n� prost�edk�
    }
}
```

#### **Omezen� a doporu�en�**  

- **Nepou��vejte pro managed resources**: GC automaticky spravuje pam� (nap�. objekty v hald�).  
- **Preferujte `IDisposable`**: Pro deterministick� uvoln�n� prost�edk� pou�ijte `Dispose()` a `using`:  
  ```csharp
  public class SouborovyHandler : IDisposable 
  {
      private StreamReader _reader;
      
      public void Dispose() 
      {
          _reader?.Dispose();
          GC.SuppressFinalize(this); // Zabr�n� vol�n� finaliz�ru
      }
  }

  // Pou�it�:
  using (var handler = new SouborovyHandler("cesta")) 
  {
      // Pr�ce se souborem
  } // Dispose() se zav�e automaticky
  ```

---

### **3. Statick� destruktor?**  

- **Neexistuje**: C# nepodporuje statick� destruktory.  
- **Alternativa**: Pro uvoln�n� statick�ch prost�edk� pou�ijte:  
  - **Metodu `AppDomain.ProcessExit`**: Reakce na ukon�en� aplikace.  
  - **Manu�ln� spr�vu**: Nap�. `static void Cleanup()`.  

---

### **4. Shrnut�**  

| **Prvek**             | **Statick� konstruktor**      | **Destruktor**               |  
|-----------------------|-------------------------------|------------------------------|  
| **��el**              | Inicializace statick�ch �len� | Uvoln�n� nemanaged prost�edk� |  
| **Spu�t�n�**          | P�i prvn�m pou�it� t��dy      | P�ed uvoln�n�m objektu (GC)  |  
| **Statick�**          | Ano                           | Ne                           |  
| **Determinismus**     | Ano (jednou)                  | Ne (z�vis� na GC)            |  

---

### **5. Doporu�en� zdroje**  

- **Ofici�ln� dokumentace**:  
  - [Static Constructors](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/static-constructors)  
  - [Finalizers](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/destructors)  
