
### **1. Definice přetěžování**

- **Přetěžování** umožňuje definovat **více metod se stejným názvem**, ale s **odlišnými parametry** (počet, typ, pořadí).
- **Účel**: Zjednodušení API a zlepšení čitelnosti kódu (volitelné parametry, různé varianty metody).
- **Typ polymorfismu**: Statický (rozhoduje se při **kompilaci**).

---

### **2. Základní pravidla**

1. **Stejný název metody**.
2. **Odlišné parametry**:
   - Počet parametrů,
   - Typ parametrů,
   - Pořadí parametrů.
3. **Návratový typ nestačí**! Metody se stejným názvem a parametry, ale různým návratovým typem, nelze přetížit.
4. **Modifikátory parametrů** (např. `ref`, `out`, `in`) mohou rozlišit přetížení.

---

### **3. Příklady přetěžování**

#### **a) Přetížení podle počtu parametrů**

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

#### **b) Přetížení podle typu parametrů**

```csharp
public class Tiskarna 
{
    public void Tisk(int cislo) => Console.WriteLine($"Číslo: {cislo}");
    public void Tisk(string text) => Console.WriteLine($"Text: {text}");
}

// Použití:
Tiskarna t = new Tiskarna();
t.Tisk(10);       // "Číslo: 10"
t.Tisk("Ahoj");   // "Text: Ahoj"
```

#### **c) Přetížení konstruktorů**

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

### **4. Speciální případy**

#### **a) Volitelné parametry vs. přetížení**

- Volitelné parametry mohou způsobit nejednoznačnost s přetíženými metodami:
  ```csharp
  public void Log(string zprava, bool isError = false) { ... }
  public void Log(string zprava) { ... } // Chyba: Nelze rozlišit při volání Log("test")
  ```

#### **b) Přetížení s `params`**

- Metoda s `params` může kolidovat s jinými přetíženími:
  ```csharp
  public void Zpracuj(params int[] cisla) { ... }
  public void Zpracuj(int a, int b) { ... } // OK – kompilátor upřednostní tuto metodu pro Zpracuj(5, 10)
  ```

---

### **5. Běžné chyby a varování**

- **Nejednoznačné přetížení**:
  ```csharp
  public void Metoda(int a, double b) { ... }
  public void Metoda(double a, int b) { ... }
  
  Metoda(5, 5); // Chyba: Nelze rozhodnout mezi přetíženími
  ```
- **Ignorování návratového typu**:
  ```csharp
  public int GetValue() => 42;
  public string GetValue() => "42"; // Chyba: Stejné parametry, jiný návratový typ
  ```

---

### **6. Výhody přetěžování**

- **Jednotné rozhraní**: Volání podobných operací stejným názvem.
- **Flexibilita**: Různé způsoby volání metody (např. s výchozími hodnotami).
- **Čitelnost**: Kód je intuitivnější (např. `Math.Max` pro různé typy).

---

### **7. Doporučené postupy**

1. **Dodržujte konzistenci**: Přetížené metody by měly dělat podobné věci.
2. **Používejte volitelné parametry opatrně**: Mohou způsobit konflikty s přetížením.
3. **Preferujte přetížení před metodami s různými názvy**: Např. `Add(int)`, `Add(double)` místo `AddInt`, `AddDouble`.

---

### **8. Ukázka reálného použití**

```csharp
public class Logger 
{
    // Přetížení pro různé typy zpráv
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
logger.Log("Aplikace spuštěna");
logger.Log("Chyba připojení", LogLevel.Warning);
logger.Log(new InvalidOperationException("Neplatná operace"));
```

---

### **9. Shrnutí klíčových bodů**

- Přetěžování umožňuje **více metod se stejným názvem**, ale **odlišnými parametry**.
- Návratový typ **nerozlišuje** přetížené metody.
- Používá se pro **zjednodušení API** a **flexibilitu**.
- Pozor na **nejednoznačnost** a **konflikty s volitelnými parametry**.
