
### **1. Definice d�di�nosti**  

D�di�nost umo��uje **vytvo�it novou t��du (odvozenou) na z�klad� existuj�c� t��dy (z�kladn�)**. Odvozen� t��da zd�d� �leny (metody, vlastnosti, pole) z�kladn� t��dy a m��e je roz���it nebo upravit.  
- **C�l**: Znovupou�it� k�du, vytv��en� hierarchi� a polymorfismus.  
- **Typy d�di�nosti**:  
  - **Jednoduch� d�di�nost** (C# podporuje pouze jednu z�kladn� t��du).  
  - **Implementace v�ce rozhran�** (t��da m��e d�dit v�ce rozhran�).  

---

### **2. Z�kladn� syntaxe**  

```csharp
public class ZakladniTrida 
{
    public void Metoda() => Console.WriteLine("Z�kladn� t��da");
}

public class OdvozenaTrida : ZakladniTrida 
{
    // D�d� Metoda() a m��e p�idat nov� �leny
    public void NovaMetoda() => Console.WriteLine("Odvozen� t��da");
}
```

---

### **3. Kl��ov� koncepty**  

#### **a) P��stupov� modifik�tory**  

- **`public`**: D�d� se.  
- **`protected`**: Dostupn� **pouze v odvozen�ch t��d�ch**.  
- **`private`**: Ned�d� se.  

#### **b) Vol�n� konstruktor� z�kladn� t��dy (`base`)**  

- Konstruktor z�kladn� t��dy se vol� **automaticky** (implicitn� bezparametrick� konstruktor).  
- Pokud z�kladn� t��da **nem� bezparametrick� konstruktor**, mus� odvozen� t��da explicitn� ur�it, kter� konstruktor zavolat:  
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

#### **c) P�eps�n� metod (`virtual` a `override`)**  

- **Z�kladn� t��da**: Ozna�uje metodu jako `virtual`.  
- **Odvozen� t��da**: P�ep�e metodu pomoc� `override`.  
  ```csharp
  public class Zvire 
  {
      public virtual void Zvuk() => Console.WriteLine("Zvuk zv��ete");
  }

  public class Pes : Zvire 
  {
      public override void Zvuk() => Console.WriteLine("Haf!");
  }
  ```

#### **d) Skryt� metod (`new`)**  

- Pokud odvozen� t��da **nesm� p�epsat metodu**, ale chce skr�t verzi z�kladn� t��dy:  
  ```csharp
  public class Zakladni 
  {
      public void Metoda() => Console.WriteLine("Z�kladn�");
  }

  public class Odvozena : Zakladni 
  {
      public new void Metoda() => Console.WriteLine("Odvozen�");
  }
  ```

#### **e) Abstraktn� t��dy a metody**  

- **Abstraktn� t��da**: Nelze instancovat, obsahuje abstraktn� metody (bez implementace).  
- **Abstraktn� metoda**: Mus� b�t p�eps�na v odvozen� t��d�.  
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

#### **f) Uzav�en� t��dy a metody (`sealed`)**  

- **`sealed class`**: Zabr�n� dal��mu d�d�n�.  
- **`sealed override`**: Zabr�n� p�eps�n� metody v dal��ch odvozen�ch t��d�ch.  
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

- **Schopnost objektu m�t r�zn� formy** (nap�. z�kladn� t��da m��e odkazovat na odvozen� objekt).  
  ```csharp
  Zvire zvire = new Pes();
  zvire.Zvuk(); // Haf! (vol� se metoda z Pes)
  ```

---

### **5. D�di�nost vs. rozhran�**  

| **Krit�rium**       | **D�di�nost**               | **Rozhran�**               |  
|----------------------|-----------------------------|-----------------------------|  
| **Po�et**            | Jedna z�kladn� t��da        | V�ce rozhran�              |  
| **Implementace**     | K�d se d�d�                 | Pouze kontrakt              |  
| **Vyu�it�**          | "Je-a" vztah (pes je zv��e) | "Um�" schopnost (ILetajici) |  

---

### **6. B�n� chyby a tipy**  

- **Diamantov� probl�m**: C# nepodporuje v�cen�sobnou d�di�nost t��d (�e�te rozhran�mi).  
- **P��li� hlubok� hierarchie**: Slo�itost udr�ujte pomoc� kompozice (m�sto d�di�nosti).  
- **Liskov Substitu�n� Princip (LSP)**: Odvozen� t��dy by m�ly b�t zam�niteln� za z�kladn�.  

---

### **7. Uk�zky k�du**  

#### **Hierarchie zam�stnanc�**  

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

#### **Pou�it� polymorfismu**  

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

### **8. Doporu�en� zdroje**  

- **Ofici�ln� dokumentace**: [Inheritance in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/fundamentals/object-oriented/inheritance)  
