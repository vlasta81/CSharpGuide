
### **1. Definice dìdiènosti**  

Dìdiènost umožòuje **vytvoøit novou tøídu (odvozenou) na základì existující tøídy (základní)**. Odvozená tøída zdìdí èleny (metody, vlastnosti, pole) základní tøídy a mùže je rozšíøit nebo upravit.  
- **Cíl**: Znovupoužití kódu, vytváøení hierarchií a polymorfismus.  
- **Typy dìdiènosti**:  
  - **Jednoduchá dìdiènost** (C# podporuje pouze jednu základní tøídu).  
  - **Implementace více rozhraní** (tøída mùže dìdit více rozhraní).  

---

### **2. Základní syntaxe**  

```csharp
public class ZakladniTrida 
{
    public void Metoda() => Console.WriteLine("Základní tøída");
}

public class OdvozenaTrida : ZakladniTrida 
{
    // Dìdí Metoda() a mùže pøidat nové èleny
    public void NovaMetoda() => Console.WriteLine("Odvozená tøída");
}
```

---

### **3. Klíèové koncepty**  

#### **a) Pøístupové modifikátory**  

- **`public`**: Dìdí se.  
- **`protected`**: Dostupné **pouze v odvozených tøídách**.  
- **`private`**: Nedìdí se.  

#### **b) Volání konstruktorù základní tøídy (`base`)**  

- Konstruktor základní tøídy se volá **automaticky** (implicitní bezparametrický konstruktor).  
- Pokud základní tøída **nemá bezparametrický konstruktor**, musí odvozená tøída explicitnì urèit, který konstruktor zavolat:  
  ```csharp
  public class Zakladni 
  {
      public Zakladni(int x) { /* ... */ }
  }

  public class Odvozena : Zakladni 
  {
      public Odvozena(int x) : base(x) { /* ... */ }
  }
  ```

#### **c) Pøepsání metod (`virtual` a `override`)**  

- **Základní tøída**: Oznaèuje metodu jako `virtual`.  
- **Odvozená tøída**: Pøepíše metodu pomocí `override`.  
  ```csharp
  public class Zvire 
  {
      public virtual void Zvuk() => Console.WriteLine("Zvuk zvíøete");
  }

  public class Pes : Zvire 
  {
      public override void Zvuk() => Console.WriteLine("Haf!");
  }
  ```

#### **d) Skrytí metod (`new`)**  

- Pokud odvozená tøída **nesmí pøepsat metodu**, ale chce skrýt verzi základní tøídy:  
  ```csharp
  public class Zakladni 
  {
      public void Metoda() => Console.WriteLine("Základní");
  }

  public class Odvozena : Zakladni 
  {
      public new void Metoda() => Console.WriteLine("Odvozená");
  }
  ```

#### **e) Abstraktní tøídy a metody**  

- **Abstraktní tøída**: Nelze instancovat, obsahuje abstraktní metody (bez implementace).  
- **Abstraktní metoda**: Musí být pøepsána v odvozené tøídì.  
  ```csharp
  public abstract class Tvar 
  {
      public abstract double VypocitejObsah();
  }

  public class Kruh : Tvar 
  {
      public double Polomer { get; set; }
      public override double VypocitejObsah() => Math.PI * Polomer * Polomer;
  }
  ```

#### **f) Uzavøené tøídy a metody (`sealed`)**  

- **`sealed class`**: Zabrání dalšímu dìdìní.  
- **`sealed override`**: Zabrání pøepsání metody v dalších odvozených tøídách.  
  ```csharp
  public sealed class NemuzuDedit { }

  public class Rodic 
  {
      public virtual void Metoda() { }
  }

  public class Potomek : Rodic 
  {
      public sealed override void Metoda() { }
  }
  ```

---

### **4. Polymorfismus**  

- **Schopnost objektu mít rùzné formy** (napø. základní tøída mùže odkazovat na odvozený objekt).  
  ```csharp
  Zvire zvire = new Pes();
  zvire.Zvuk(); // Haf! (volá se metoda z Pes)
  ```

---

### **5. Dìdiènost vs. rozhraní**  

| **Kritérium**       | **Dìdiènost**               | **Rozhraní**               |  
|----------------------|-----------------------------|-----------------------------|  
| **Poèet**            | Jedna základní tøída        | Více rozhraní              |  
| **Implementace**     | Kód se dìdí                 | Pouze kontrakt              |  
| **Využití**          | "Je-a" vztah (pes je zvíøe) | "Umí" schopnost (ILetajici) |  

---

### **6. Bìžné chyby a tipy**  

- **Diamantový problém**: C# nepodporuje vícenásobnou dìdiènost tøíd (øešte rozhraními).  
- **Pøíliš hluboká hierarchie**: Složitost udržujte pomocí kompozice (místo dìdiènosti).  
- **Liskov Substituèní Princip (LSP)**: Odvozené tøídy by mìly být zamìnitelné za základní.  

---

### **7. Ukázky kódu**  

#### **Hierarchie zamìstnancù**  

```csharp
public abstract class Zamestnanec 
{
    public string Jmeno { get; set; }
    public abstract decimal VypocitejPlat();
}

public class Manager : Zamestnanec 
{
    public decimal Bonus { get; set; }
    public override decimal VypocitejPlat() => 50000 + Bonus;
}

public class Programator : Zamestnanec 
{
    public int Hodiny { get; set; }
    public override decimal VypocitejPlat() => Hodiny * 1000;
}
```

#### **Použití polymorfismu**  

```csharp
List<Zamestnanec> zamestnanci = new List<Zamestnanec> 
{
    new Manager { Jmeno = "Karel", Bonus = 10000 },
    new Programator { Jmeno = "Anna", Hodiny = 40 }
};

foreach (var z in zamestnanci) 
{
    Console.WriteLine($"{z.Jmeno}: {z.VypocitejPlat()}");
}
```

---

### **8. Doporuèené zdroje**  

- **Oficiální dokumentace**: [Inheritance in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/fundamentals/object-oriented/inheritance)  
