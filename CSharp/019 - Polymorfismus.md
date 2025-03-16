
### **1. Definice polymorfismu**  

Polymorfismus umo��uje **objekt�m r�zn�ch t��d reagovat odli�n� na stejnou metodu** (vol�n� stejn� metody m��e m�t r�zn� chov�n� v z�vislosti na typu objektu).  
- **Kl��ov� princip OOP**: "Jedno rozhran�, mnoho implementac�."  
- **Typy**:  
  - **Statick� polymorfismus** (compile-time): P�et�en� metod (overloading).  
  - **Dynamick� polymorfismus** (runtime): P�eps�n� metod (overriding) a pr�ce s rozhran�mi.  

---

### **2. Statick� polymorfismus (p�et�en� metod)**  

- **Metody stejn�ho n�zvu**, ale **r�zn� parametry** (po�et, typ, po�ad�).  
- **Vyhodnocuje se p�i kompilaci**.  

#### **P��klad**:  

```csharp
public class Kalkulacka 
{
    // P�et�en� metody Add
    public int Add(int a, int b) => a + b;
    public double Add(double a, double b) => a + b;
    public string Add(string a, string b) => a + b;
}

// Pou�it�:
Kalkulacka k = new Kalkulacka();
Console.WriteLine(k.Add(5, 3));      // 8
Console.WriteLine(k.Add("Ahoj", "!")); // "Ahoj!"
```

---

### **3. Dynamick� polymorfismus (p�eps�n� metod)**  

- **Z�kladn� t��da** ozna�� metodu jako `virtual`.  
- **Odvozen� t��da** ji p�ep�e pomoc� `override`.  
- **Vyhodnocuje se za b�hu** (runtime).  

#### **P��klad**:  

```csharp
public class Zvire 
{
    public virtual void Zvuk() => Console.WriteLine("Zvuk zv��ete");
}

public class Pes : Zvire 
{
    public override void Zvuk() => Console.WriteLine("Haf!");
}

public class Kocka : Zvire 
{
    public override void Zvuk() => Console.WriteLine("M�au!");
}

// Pou�it�:
Zvire z1 = new Pes();
Zvire z2 = new Kocka();
z1.Zvuk(); // "Haf!"
z2.Zvuk(); // "M�au!"
```

---

### **4. Kl��ov� slova**  

- **`virtual`**: Ozna�uje metodu, kterou lze p�epsat v odvozen� t��d�.  
- **`override`**: P�episuje metodu z�kladn� t��dy.  
- **`new`**: Skryje metodu z�kladn� t��dy (bez p�eps�n�).  
- **`abstract`**: Vynut� p�eps�n� metody v odvozen�ch t��d�ch (v abstraktn�ch t��d�ch).  

---

### **5. Rozhran� a polymorfismus**  

- Objekty r�zn�ch t��d implementuj�c� stejn� rozhran� mohou b�t pou�it� **zam�niteln�**.  

#### **P��klad**:  

```csharp
public interface IShape 
{
    void Draw();
}

public class Circle : IShape 
{
    public void Draw() => Console.WriteLine("Kresl�m kruh");
}

public class Square : IShape 
{
    public void Draw() => Console.WriteLine("Kresl�m �tverec");
}

// Pou�it�:
List<IShape> shapes = new List<IShape> { new Circle(), new Square() };
foreach (var shape in shapes) 
{
    shape.Draw(); // Vol� spr�vnou implementaci
}
```

---

### **6. Porovn�n� p�eps�n� (`override`) a skryt� (`new`)**  

| **Vlastnost**       | `override`                  | `new`                      |  
|----------------------|-----------------------------|----------------------------|  
| **Vztah k z�kladn� t��d�** | P�ep�e metodu         | Skryje metodu              |  
| **Typ vazby**        | Dynamick� (runtime)         | Statick� (compile-time)    |  
| **Pou�it�**          | Zm�na chov�n� metody        | Nov� implementace metody   |  

---

### **7. Uzav�en� metody (`sealed`)**  

- Zabr�n� dal��mu p�eps�n� metody v odvozen�ch t��d�ch.  
```csharp
public class Rodic 
{
    public virtual void Metoda() { }
}

public class Potomek : Rodic 
{
    public sealed override void Metoda() { } // Nelze d�le p�epsat
}
```

---

### **8. V�hody polymorfismu**  

- **Flexibilita**: Jedno rozhran� pro r�zn� typy.  
- **Roz�i�itelnost**: Snadn� p�id�v�n� nov�ch t��d bez zm�ny existuj�c�ho k�du.  
- **�itelnost**: K�d pracuje s abstraktn�mi typy, ne konkr�tn�mi implementacemi.  

---

### **9. B�n� chyby a tipy**  

- **Zapomenut� `override`**: Metoda nebude p�eps�na.  
- **Z�m�na `new` a `override`**: `new` skryje metodu, neposkytuje runtime polymorfismus.  
- **Pou�it� `as` a `is` pro kontrolu typu**:  
  ```csharp
  if (zviratko is Pes pes) 
  {
      pes.Stekat();
  }
  ```

---

### **10. Uk�zka komplexn�ho pou�it�**  

```csharp
public abstract class Ucet 
{
    public decimal Zustatek { get; protected set; }
    public abstract void Vloz(decimal castka);
}

public class SporiciUcet : Ucet 
{
    public override void Vloz(decimal castka) => Zustatek += castka * 1.05m; // 5% �rok
}

public class BeznyUcet : Ucet 
{
    public override void Vloz(decimal castka) => Zustatek += castka;
}

// Pou�it�:
List<Ucet> ucty = new List<Ucet> { new SporiciUcet(), new BeznyUcet() };
foreach (var ucet in ucty) 
{
    ucet.Vloz(1000);
    Console.WriteLine(ucet.Zustatek); // 1050 / 1000
}
```
