
### **1. Definice polymorfismu**  

Polymorfismus umožňuje **objektům různých tříd reagovat odlišně na stejnou metodu** (volání stejné metody může mít různé chování v závislosti na typu objektu).  
- **Klíčový princip OOP**: "Jedno rozhraní, mnoho implementací."  
- **Typy**:  
  - **Statický polymorfismus** (compile-time): Přetížení metod (overloading).  
  - **Dynamický polymorfismus** (runtime): Přepsání metod (overriding) a práce s rozhraními.  

---

### **2. Statický polymorfismus (přetížení metod)**  

- **Metody stejného názvu**, ale **různé parametry** (počet, typ, pořadí).  
- **Vyhodnocuje se při kompilaci**.  

#### **Příklad**:  

```csharp
public class Kalkulacka 
{
    // Přetížení metody Add
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

### **3. Dynamický polymorfismus (přepsání metod)**  

- **Základní třída** označí metodu jako `virtual`.  
- **Odvozená třída** ji přepíše pomocí `override`.  
- **Vyhodnocuje se za běhu** (runtime).  

#### **Příklad**:  

```csharp
public class Zvire 
{
    public virtual void Zvuk() => Console.WriteLine("Zvuk zvířete");
}

public class Pes : Zvire 
{
    public override void Zvuk() => Console.WriteLine("Haf!");
}

public class Kocka : Zvire 
{
    public override void Zvuk() => Console.WriteLine("Mňau!");
}

// Použití:
Zvire z1 = new Pes();
Zvire z2 = new Kocka();
z1.Zvuk(); // "Haf!"
z2.Zvuk(); // "Mňau!"
```

---

### **4. Klíčová slova**  

- **`virtual`**: Označuje metodu, kterou lze přepsat v odvozené třídě.  
- **`override`**: Přepisuje metodu základní třídy.  
- **`new`**: Skryje metodu základní třídy (bez přepsání).  
- **`abstract`**: Vynutí přepsání metody v odvozených třídách (v abstraktních třídách).  

---

### **5. Rozhraní a polymorfismus**  

- Objekty různých tříd implementující stejné rozhraní mohou být použité **zaměnitelně**.  

#### **Příklad**:  

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
    public void Draw() => Console.WriteLine("Kreslím čtverec");
}

// Použití:
List<IShape> shapes = new List<IShape> { new Circle(), new Square() };
foreach (var shape in shapes) 
{
    shape.Draw(); // Volá správnou implementaci
}
```

---

### **6. Porovnání přepsání (`override`) a skrytí (`new`)**  

| **Vlastnost**       | `override`                  | `new`                      |  
|----------------------|-----------------------------|----------------------------|  
| **Vztah k základní třídě** | Přepíše metodu         | Skryje metodu              |  
| **Typ vazby**        | Dynamická (runtime)         | Statická (compile-time)    |  
| **Použití**          | Změna chování metody        | Nová implementace metody   |  

---

### **7. Uzavření metody (`sealed`)**  

- Zabrání dalšímu přepsání metody v odvozených třídách.  
```csharp
public class Rodic 
{
    public virtual void Metoda() { }
}

public class Potomek : Rodic 
{
    public sealed override void Metoda() { } // Nelze dále přepsat
}
```

---

### **8. Výhody polymorfismu**  

- **Flexibilita**: Jedno rozhraní pro různé typy.  
- **Rozšiřitelnost**: Snadné přidávání nových tříd bez změny existujícího kódu.  
- **Čitelnost**: Kód pracuje s abstraktními typy, ne konkrétními implementacemi.  

---

### **9. Běžné chyby a tipy**  

- **Zapomenuté `override`**: Metoda nebude přepsána.  
- **Záměna `new` a `override`**: `new` skryje metodu, neposkytuje runtime polymorfismus.  
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
