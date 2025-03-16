
### **1. Definice p�et�ov�n�**

- **P�et�ov�n�** umo��uje definovat **v�ce metod se stejn�m n�zvem**, ale s **odli�n�mi parametry** (po�et, typ, po�ad�).
- **��el**: Zjednodu�en� API a zlep�en� �itelnosti k�du (voliteln� parametry, r�zn� varianty metody).
- **Typ polymorfismu**: Statick� (rozhoduje se p�i **kompilaci**).

---

### **2. Z�kladn� pravidla**

1. **Stejn� n�zev metody**.
2. **Odli�n� parametry**:
   - Po�et parametr�,
   - Typ parametr�,
   - Po�ad� parametr�.
3. **N�vratov� typ nesta��**! Metody se stejn�m n�zvem a parametry, ale r�zn�m n�vratov�m typem, nelze p�et�it.
4. **Modifik�tory parametr�** (nap�. `ref`, `out`, `in`) mohou rozli�it p�et�en�.

---

### **3. P��klady p�et�ov�n�**

#### **a) P�et�en� podle po�tu parametr�**

```csharp
public class Kalkulacka 
{
    public int Secti(int a, int b) => a + b;
    public int Secti(int a, int b, int c) => a + b + c;
}

// Pou�it�:
Kalkulacka k = new Kalkulacka();
Console.WriteLine(k.Secti(2, 3));      // 5
Console.WriteLine(k.Secti(2, 3, 4));  // 9
```

#### **b) P�et�en� podle typu parametr�**

```csharp
public class Tiskarna 
{
    public void Tisk(int cislo) => Console.WriteLine($"��slo: {cislo}");
    public void Tisk(string text) => Console.WriteLine($"Text: {text}");
}

// Pou�it�:
Tiskarna t = new Tiskarna();
t.Tisk(10);       // "��slo: 10"
t.Tisk("Ahoj");   // "Text: Ahoj"
```

#### **c) P�et�en� konstruktor�**

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

// Pou�it�:
Uzivatel u1 = new Uzivatel("Anna");
Uzivatel u2 = new Uzivatel("Karel", 30);
```

---

### **4. Speci�ln� p��pady**

#### **a) Voliteln� parametry vs. p�et�en�**

- Voliteln� parametry mohou zp�sobit nejednozna�nost s p�et�en�mi metodami:
  ```csharp
  public void Log(string zprava, bool isError = false) { ... }
  public void Log(string zprava) { ... } // Chyba: Nelze rozli�it p�i vol�n� Log("test")
  ```

#### **b) P�et�en� s `params`**

- Metoda s `params` m��e kolidovat s jin�mi p�et�en�mi:
  ```csharp
  public void Zpracuj(params int[] cisla) { ... }
  public void Zpracuj(int a, int b) { ... } // OK � kompil�tor up�ednostn� tuto metodu pro Zpracuj(5, 10)
  ```

---

### **5. B�n� chyby a varov�n�**

- **Nejednozna�n� p�et�en�**:
  ```csharp
  public void Metoda(int a, double b) { ... }
  public void Metoda(double a, int b) { ... }
  
  Metoda(5, 5); // Chyba: Nelze rozhodnout mezi p�et�en�mi
  ```
- **Ignorov�n� n�vratov�ho typu**:
  ```csharp
  public int GetValue() => 42;
  public string GetValue() => "42"; // Chyba: Stejn� parametry, jin� n�vratov� typ
  ```

---

### **6. V�hody p�et�ov�n�**

- **Jednotn� rozhran�**: Vol�n� podobn�ch operac� stejn�m n�zvem.
- **Flexibilita**: R�zn� zp�soby vol�n� metody (nap�. s v�choz�mi hodnotami).
- **�itelnost**: K�d je intuitivn�j�� (nap�. `Math.Max` pro r�zn� typy).

---

### **7. Doporu�en� postupy**

1. **Dodr�ujte konzistenci**: P�et�en� metody by m�ly d�lat podobn� v�ci.
2. **Pou��vejte voliteln� parametry opatrn�**: Mohou zp�sobit konflikty s p�et�en�m.
3. **Preferujte p�et�en� p�ed metodami s r�zn�mi n�zvy**: Nap�. `Add(int)`, `Add(double)` m�sto `AddInt`, `AddDouble`.

---

### **8. Uk�zka re�ln�ho pou�it�**

```csharp
public class Logger 
{
    // P�et�en� pro r�zn� typy zpr�v
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

// Pou�it�:
Logger logger = new Logger();
logger.Log("Aplikace spu�t�na");
logger.Log("Chyba p�ipojen�", LogLevel.Warning);
logger.Log(new InvalidOperationException("Neplatn� operace"));
```

---

### **9. Shrnut� kl��ov�ch bod�**

- P�et�ov�n� umo��uje **v�ce metod se stejn�m n�zvem**, ale **odli�n�mi parametry**.
- N�vratov� typ **nerozli�uje** p�et�en� metody.
- Pou��v� se pro **zjednodu�en� API** a **flexibilitu**.
- Pozor na **nejednozna�nost** a **konflikty s voliteln�mi parametry**.
