
### **1. Definice struktury**  

Struktura je **hodnotový typ** (ukládá data pøímo v pamìti, typicky na zásobníku), který slouží k vytváøení lehkých objektù pro ukládání dat.  
- **Klíèové vlastnosti**:  
  - Nemùže dìdit z jiné tøídy/struktury (kromì implementace rozhraní).  
  - Má výchozí konstruktor bez parametrù (nelze jej odstranit).  
  - Vhodná pro malé, nemìnné datové struktury (napø. souøadnice, barvy).  

---

### **2. Syntaxe a pøíklad**  

```csharp
public struct Bod 
{
    public int X; 
    public int Y;

    // Konstruktor s parametry (musí inicializovat všechna pole)
    public Bod(int x, int y) 
    {
        X = x;
        Y = y;
    }

    // Metoda ve struktuøe
    public void Posun(int dx, int dy) 
    {
        X += dx;
        Y += dy;
    }
}

// Použití:
Bod bod = new Bod(10, 20);
bod.Posun(5, 5); // X=15, Y=25
```

---

### **3. Klíèové vlastnosti** 

#### **a) Hodnotová sémantika**  

- Pøiøazení vytváøí **kopii dat** (ne odkaz).  
```csharp
Bod b1 = new Bod(5, 5);
Bod b2 = b1; // Kopie hodnoty
b2.X = 10;   // b1.X zùstává 5
```

#### **b) Žádná dìdiènost**  

- Struktura mùže implementovat rozhraní, ale nemùže dìdit z jiné struktury/tøídy.  
```csharp
public interface IZobrazitelny 
{
    void Zobraz();
}

public struct Bod : IZobrazitelny 
{
    public void Zobraz() => Console.WriteLine($"{X}, {Y}");
}
```

#### **c) Imutabilita**  

- Doporuèuje se pro struktury, aby se pøedešlo neoèekávanému chování (kopírování hodnot).  
```csharp
public readonly struct NemìnnyBod 
{
    public readonly int X;
    public readonly int Y;
    
    public NemìnnyBod(int x, int y) 
    {
        X = x;
        Y = y;
    }
}
```

---

### **4. Struktura vs. tøída**  

| **Kritérium**       | **Struktura (struct)**          | **Tøída (class)**             |  
|----------------------|---------------------------------|-------------------------------|  
| **Typ**              | Hodnotový (stack)               | Referenèní (heap)             |  
| **Dìdiènost**        | Nelze dìdit                     | Podporuje dìdiènost           |  
| **Výchozí konstruktor** | Automatický (nelze odstranit) | Bez parametru (lze definovat) |  
| **Nullabilita**      | Nelze (kromì `Nullable<T>`)     | Ano                           |  
| **Velikost**         | Doporuèeno do 16–24 bytù        | Žádné omezení                 |  

---

### **5. Kdy použít strukturu?** 

- **Malé datové struktury**: Napø. `DateTime`, `Point`, `Complex`.  
- **Èasté kopírování**: Když je kopírování levnìjší než správa referencí.  
- **Krátká životnost**: Data nepotøebují dlouhodobé uchování.  

---

### **6. Omezení struktur**  

- Nelze definovat **destruktor**.  
- Nelze použít `abstract`, `sealed`, nebo `protected` èleny.  
- Nelze mít **implicitní bezparametrický konstruktor** (v C# 10+ lze, ale pouze pokud jsou všechna pole inicializována).  

---

### **7. Bìžné chyby**  

- **Mutovatelné struktury**: Zmìna kopie neovlivní originál, což mùže vést k chybám.  
- **Velké struktury**: Èasté kopírování mùže snížit výkon.  
- **Boxing/Unboxing**: Pøevod na `object` zpùsobí ztrátu výhod hodnotového typu.  

---

### **8. Ukázky kódu**  

#### **Struktura s rozhraním**  

```csharp
public struct RGBColor : IEquatable<RGBColor> 
{
    public byte R, G, B;

    public bool Equals(RGBColor other) 
    {
        return R == other.R && G == other.G && B == other.B;
    }
}
```

#### **Readonly struktura (C# 7.2+)**  

```csharp
public readonly struct Velikost 
{
    public readonly int Sirka;
    public readonly int Vyska;

    public Velikost(int sirka, int vyska) 
    {
        Sirka = sirka;
        Vyska = vyska;
    }
}
```

---

### **9. Doporuèené postupy**  

1. **Zachovejte struktury malé**: Ideálnì pod 16 bytù.  
2. **Preferujte imutabilitu**: Používejte `readonly struct` a `readonly` pole.  
3. **Používejte pro jednoduchá data**: Napø. geometrické tvary, konfiguraèní hodnoty.  
4. **Vyhnìte se boxing**: Nepøiøazujte struktury do `object` zbyteènì.  

---

### **10. Shrnutí** 

- **Struktury** jsou hodnotové typy pro efektivní práci s malými daty.  
- **Kopírují se celé**, ne odkazem – vhodné pro èasté krátkodobé operace.  
- **Imutabilita** je klíèová pro pøedvídatelné chování.  
