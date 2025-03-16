
### **1. Definice pøetìžování**

- **Pøetìžování** umožòuje definovat **více metod se stejným názvem**, ale s **odlišnými parametry** (poèet, typ, poøadí).
- **Úèel**: Zjednodušení API a zlepšení èitelnosti kódu (volitelné parametry, rùzné varianty metody).
- **Typ polymorfismu**: Statický (rozhoduje se pøi **kompilaci**).

---

### **2. Základní pravidla**

1. **Stejný název metody**.
2. **Odlišné parametry**:
   - Poèet parametrù,
   - Typ parametrù,
   - Poøadí parametrù.
3. **Návratový typ nestaèí**! Metody se stejným názvem a parametry, ale rùzným návratovým typem, nelze pøetížit.
4. **Modifikátory parametrù** (napø. `ref`, `out`, `in`) mohou rozlišit pøetížení.

---

### **3. Pøíklady pøetìžování**

#### **a) Pøetížení podle poètu parametrù**

```csharp
public class Kalkulacka 
{
    public int Secti(int a, int b) => a + b;
    public int Secti(int a, int b, int c) => a + b + c;
}

// Použití:
Kalkulacka k = new Kalkulacka();
Console.WriteLine(k.Secti(2, 3));      // 5
Console.WriteLine(k.Secti(2, 3, 4));  // 9
```

#### **b) Pøetížení podle typu parametrù**

```csharp
public class Tiskarna 
{
    public void Tisk(int cislo) => Console.WriteLine($"Èíslo: {cislo}");
    public void Tisk(string text) => Console.WriteLine($"Text: {text}");
}

// Použití:
Tiskarna t = new Tiskarna();
t.Tisk(10);       // "Èíslo: 10"
t.Tisk("Ahoj");   // "Text: Ahoj"
```

#### **c) Pøetížení konstruktorù**

```csharp
public class Uzivatel 
{
    public string Jmeno { get; }
    public int Vek { get; }

    public Uzivatel(string jmeno) 
    {
        Jmeno = jmeno;
        Vek = 0;
    }

    public Uzivatel(string jmeno, int vek) 
    {
        Jmeno = jmeno;
        Vek = vek;
    }
}

// Použití:
Uzivatel u1 = new Uzivatel("Anna");
Uzivatel u2 = new Uzivatel("Karel", 30);
```

---

### **4. Speciální pøípady**

#### **a) Volitelné parametry vs. pøetížení**

- Volitelné parametry mohou zpùsobit nejednoznaènost s pøetíženými metodami:
  ```csharp
  public void Log(string zprava, bool isError = false) { ... }
  public void Log(string zprava) { ... } // Chyba: Nelze rozlišit pøi volání Log("test")
  ```

#### **b) Pøetížení s `params`**

- Metoda s `params` mùže kolidovat s jinými pøetíženími:
  ```csharp
  public void Zpracuj(params int[] cisla) { ... }
  public void Zpracuj(int a, int b) { ... } // OK – kompilátor upøednostní tuto metodu pro Zpracuj(5, 10)
  ```

---

### **5. Bìžné chyby a varování**

- **Nejednoznaèné pøetížení**:
  ```csharp
  public void Metoda(int a, double b) { ... }
  public void Metoda(double a, int b) { ... }
  
  Metoda(5, 5); // Chyba: Nelze rozhodnout mezi pøetíženími
  ```
- **Ignorování návratového typu**:
  ```csharp
  public int GetValue() => 42;
  public string GetValue() => "42"; // Chyba: Stejné parametry, jiný návratový typ
  ```

---

### **6. Výhody pøetìžování**

- **Jednotné rozhraní**: Volání podobných operací stejným názvem.
- **Flexibilita**: Rùzné zpùsoby volání metody (napø. s výchozími hodnotami).
- **Èitelnost**: Kód je intuitivnìjší (napø. `Math.Max` pro rùzné typy).

---

### **7. Doporuèené postupy**

1. **Dodržujte konzistenci**: Pøetížené metody by mìly dìlat podobné vìci.
2. **Používejte volitelné parametry opatrnì**: Mohou zpùsobit konflikty s pøetížením.
3. **Preferujte pøetížení pøed metodami s rùznými názvy**: Napø. `Add(int)`, `Add(double)` místo `AddInt`, `AddDouble`.

---

### **8. Ukázka reálného použití**

```csharp
public class Logger 
{
    // Pøetížení pro rùzné typy zpráv
    public void Log(string message) 
    {
        Console.WriteLine($"[INFO] {message}");
    }

    public void Log(string message, LogLevel level) 
    {
        Console.WriteLine($"[{level}] {message}");
    }

    public void Log(Exception ex) 
    {
        Console.WriteLine($"[ERROR] {ex.Message}");
    }
}

// Použití:
Logger logger = new Logger();
logger.Log("Aplikace spuštìna");
logger.Log("Chyba pøipojení", LogLevel.Warning);
logger.Log(new InvalidOperationException("Neplatná operace"));
```

---

### **9. Shrnutí klíèových bodù**

- Pøetìžování umožòuje **více metod se stejným názvem**, ale **odlišnými parametry**.
- Návratový typ **nerozlišuje** pøetížené metody.
- Používá se pro **zjednodušení API** a **flexibilitu**.
- Pozor na **nejednoznaènost** a **konflikty s volitelnými parametry**.
