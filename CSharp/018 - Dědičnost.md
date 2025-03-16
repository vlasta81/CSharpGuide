
### **1. Definice dědičnosti**  

Dědičnost umožňuje **vytvořit novou třídu (odvozenou) na základě existující třídy (základní)**. Odvozená třída zdědí členy (metody, vlastnosti, pole) základní třídy a může je rozšířit nebo upravit.  
- **Cíl**: Znovupoužití kódu, vytváření hierarchií a polymorfismus.  
- **Typy dědičnosti**:  
  - **Jednoduchá dědičnost** (C# podporuje pouze jednu základní třídu).  
  - **Implementace více rozhraní** (třída může dědit více rozhraní).  

---

### **2. Základní syntaxe**  

```csharp
public class ZakladniTrida 
{
    public void Metoda() => Console.WriteLine("Základní třída");
}

public class OdvozenaTrida : ZakladniTrida 
{
    // Dědí Metoda() a může přidat nové členy
    public void NovaMetoda() => Console.WriteLine("Odvozená třída");
}
```

---

### **3. Klíčové koncepty**  

#### **a) Přístupové modifikátory**  

- **`public`**: Dědí se.  
- **`protected`**: Dostupné **pouze v odvozených třídách**.  
- **`private`**: Nedědí se.  

#### **b) Volání konstruktorů základní třídy (`base`)**  

- Konstruktor základní třídy se volá **automaticky** (implicitní bezparametrický konstruktor).  
- Pokud základní třída **nemá bezparametrický konstruktor**, musí odvozená třída explicitně určit, který konstruktor zavolat:  
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

#### **c) Přepsání metod (`virtual` a `override`)**  

- **Základní třída**: Označuje metodu jako `virtual`.  
- **Odvozená třída**: Přepíše metodu pomocí `override`.  
  ```csharp
  public class Zvire 
  {
      public virtual void Zvuk() => Console.WriteLine("Zvuk zvířete");
  }

  public class Pes : Zvire 
  {
      public override void Zvuk() => Console.WriteLine("Haf!");
  }
  ```

#### **d) Skrytí metod (`new`)**  

- Pokud odvozená třída **nesmí přepsat metodu**, ale chce skrýt verzi základní třídy:  
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

#### **e) Abstraktní třídy a metody**  

- **Abstraktní třída**: Nelze instancovat, obsahuje abstraktní metody (bez implementace).  
- **Abstraktní metoda**: Musí být přepsána v odvozené třídě.  
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

#### **f) Uzavřené třídy a metody (`sealed`)**  

- **`sealed class`**: Zabrání dalšímu dědění.  
- **`sealed override`**: Zabrání přepsání metody v dalších odvozených třídách.  
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

- **Schopnost objektu mít různé formy** (např. základní třída může odkazovat na odvozený objekt).  
  ```csharp
  Zvire zvire = new Pes();
  zvire.Zvuk(); // Haf! (volá se metoda z Pes)
  ```

---

### **5. Dědičnost vs. rozhraní**  

| **Kritérium**       | **Dědičnost**               | **Rozhraní**               |  
|----------------------|-----------------------------|-----------------------------|  
| **Počet**            | Jedna základní třída        | Více rozhraní              |  
| **Implementace**     | Kód se dědí                 | Pouze kontrakt              |  
| **Využití**          | "Je-a" vztah (pes je zvíře) | "Umí" schopnost (ILetajici) |  

---

### **6. Běžné chyby a tipy**  

- **Diamantový problém**: C# nepodporuje vícenásobnou dědičnost tříd (řešte rozhraními).  
- **Příliš hluboká hierarchie**: Složitost udržujte pomocí kompozice (místo dědičnosti).  
- **Liskov Substituční Princip (LSP)**: Odvozené třídy by měly být zaměnitelné za základní.  

---

### **7. Ukázky kódu**  

#### **Hierarchie zaměstnanců**  

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

### **8. Doporučené zdroje**  

- **Oficiální dokumentace**: [Inheritance in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/fundamentals/object-oriented/inheritance)  
