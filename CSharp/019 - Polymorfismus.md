
### **1. Definice polymorfismu**  

Polymorfismus umožòuje **objektùm rùzných tøíd reagovat odlišnì na stejnou metodu** (volání stejné metody mùže mít rùzné chování v závislosti na typu objektu).  
- **Klíèový princip OOP**: "Jedno rozhraní, mnoho implementací."  
- **Typy**:  
  - **Statický polymorfismus** (compile-time): Pøetížení metod (overloading).  
  - **Dynamický polymorfismus** (runtime): Pøepsání metod (overriding) a práce s rozhraními.  

---

### **2. Statický polymorfismus (pøetížení metod)**  

- **Metody stejného názvu**, ale **rùzné parametry** (poèet, typ, poøadí).  
- **Vyhodnocuje se pøi kompilaci**.  

#### **Pøíklad**:  

```csharp
public class Kalkulacka 
{
    // Pøetížení metody Add
    public int Add(int a, int b) => a + b;
    public double Add(double a, double b) => a + b;
    public string Add(string a, string b) => a + b;
}

// Použití:
Kalkulacka k = new Kalkulacka();
Console.WriteLine(k.Add(5, 3));      // 8
Console.WriteLine(k.Add("Ahoj", "!")); // "Ahoj!"
```

---

### **3. Dynamický polymorfismus (pøepsání metod)**  

- **Základní tøída** oznaèí metodu jako `virtual`.  
- **Odvozená tøída** ji pøepíše pomocí `override`.  
- **Vyhodnocuje se za bìhu** (runtime).  

#### **Pøíklad**:  

```csharp
public class Zvire 
{
    public virtual void Zvuk() => Console.WriteLine("Zvuk zvíøete");
}

public class Pes : Zvire 
{
    public override void Zvuk() => Console.WriteLine("Haf!");
}

public class Kocka : Zvire 
{
    public override void Zvuk() => Console.WriteLine("Mòau!");
}

// Použití:
Zvire z1 = new Pes();
Zvire z2 = new Kocka();
z1.Zvuk(); // "Haf!"
z2.Zvuk(); // "Mòau!"
```

---

### **4. Klíèová slova**  

- **`virtual`**: Oznaèuje metodu, kterou lze pøepsat v odvozené tøídì.  
- **`override`**: Pøepisuje metodu základní tøídy.  
- **`new`**: Skryje metodu základní tøídy (bez pøepsání).  
- **`abstract`**: Vynutí pøepsání metody v odvozených tøídách (v abstraktních tøídách).  

---

### **5. Rozhraní a polymorfismus**  

- Objekty rùzných tøíd implementující stejné rozhraní mohou být použité **zamìnitelnì**.  

#### **Pøíklad**:  

```csharp
public interface IShape 
{
    void Draw();
}

public class Circle : IShape 
{
    public void Draw() => Console.WriteLine("Kreslím kruh");
}

public class Square : IShape 
{
    public void Draw() => Console.WriteLine("Kreslím ètverec");
}

// Použití:
List<IShape> shapes = new List<IShape> { new Circle(), new Square() };
foreach (var shape in shapes) 
{
    shape.Draw(); // Volá správnou implementaci
}
```

---

### **6. Porovnání pøepsání (`override`) a skrytí (`new`)**  

| **Vlastnost**       | `override`                  | `new`                      |  
|----------------------|-----------------------------|----------------------------|  
| **Vztah k základní tøídì** | Pøepíše metodu         | Skryje metodu              |  
| **Typ vazby**        | Dynamická (runtime)         | Statická (compile-time)    |  
| **Použití**          | Zmìna chování metody        | Nová implementace metody   |  

---

### **7. Uzavøení metody (`sealed`)**  

- Zabrání dalšímu pøepsání metody v odvozených tøídách.  
```csharp
public class Rodic 
{
    public virtual void Metoda() { }
}

public class Potomek : Rodic 
{
    public sealed override void Metoda() { } // Nelze dále pøepsat
}
```

---

### **8. Výhody polymorfismu**  

- **Flexibilita**: Jedno rozhraní pro rùzné typy.  
- **Rozšiøitelnost**: Snadné pøidávání nových tøíd bez zmìny existujícího kódu.  
- **Èitelnost**: Kód pracuje s abstraktními typy, ne konkrétními implementacemi.  

---

### **9. Bìžné chyby a tipy**  

- **Zapomenuté `override`**: Metoda nebude pøepsána.  
- **Zámìna `new` a `override`**: `new` skryje metodu, neposkytuje runtime polymorfismus.  
- **Použití `as` a `is` pro kontrolu typu**:  
  ```csharp
  if (zviratko is Pes pes) 
  {
      pes.Stekat();
  }
  ```

---

### **10. Ukázka komplexního použití**  

```csharp
public abstract class Ucet 
{
    public decimal Zustatek { get; protected set; }
    public abstract void Vloz(decimal castka);
}

public class SporiciUcet : Ucet 
{
    public override void Vloz(decimal castka) => Zustatek += castka * 1.05m; // 5% úrok
}

public class BeznyUcet : Ucet 
{
    public override void Vloz(decimal castka) => Zustatek += castka;
}

// Použití:
List<Ucet> ucty = new List<Ucet> { new SporiciUcet(), new BeznyUcet() };
foreach (var ucet in ucty) 
{
    ucet.Vloz(1000);
    Console.WriteLine(ucet.Zustatek); // 1050 / 1000
}
```
