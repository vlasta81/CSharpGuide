
### **1. Definice struktury**  

Struktura je **hodnotový typ** (ukládá data přímo v paměti, typicky na zásobníku), který slouží k vytváření lehkých objektů pro ukládání dat.  
- **Klíčové vlastnosti**:  
  - Nemůže dědit z jiné třídy/struktury (kromě implementace rozhraní).  
  - Má výchozí konstruktor bez parametrů (nelze jej odstranit).  
  - Vhodná pro malé, neměnné datové struktury (např. souřadnice, barvy).  

---

### **2. Syntaxe a příklad**  

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

    // Metoda ve struktuře
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

### **3. Klíčové vlastnosti** 

#### **a) Hodnotová sémantika**  

- Přiřazení vytváří **kopii dat** (ne odkaz).  
```csharp
Bod b1 = new Bod(5, 5);
Bod b2 = b1; // Kopie hodnoty
b2.X = 10;   // b1.X zůstává 5
```

#### **b) Žádná dědičnost**  

- Struktura může implementovat rozhraní, ale nemůže dědit z jiné struktury/třídy.  
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

- Doporučuje se pro struktury, aby se předešlo neočekávanému chování (kopírování hodnot).  
```csharp
public readonly struct NeměnnyBod 
{
    public readonly int X;
    public readonly int Y;
    
    public NeměnnyBod(int x, int y) 
    {
        X = x;
        Y = y;
    }
}
```

---

### **4. Struktura vs. třída**  

| **Kritérium**       | **Struktura (struct)**          | **Třída (class)**             |  
|----------------------|---------------------------------|-------------------------------|  
| **Typ**              | Hodnotový (stack)               | Referenční (heap)             |  
| **Dědičnost**        | Nelze dědit                     | Podporuje dědičnost           |  
| **Výchozí konstruktor** | Automatický (nelze odstranit) | Bez parametru (lze definovat) |  
| **Nullabilita**      | Nelze (kromě `Nullable<T>`)     | Ano                           |  
| **Velikost**         | Doporučeno do 16–24 bytů        | Žádné omezení                 |  

---

### **5. Kdy použít strukturu?** 

- **Malé datové struktury**: Např. `DateTime`, `Point`, `Complex`.  
- **Časté kopírování**: Když je kopírování levnější než správa referencí.  
- **Krátká životnost**: Data nepotřebují dlouhodobé uchování.  

---

### **6. Omezení struktur**  

- Nelze definovat **destruktor**.  
- Nelze použít `abstract`, `sealed`, nebo `protected` členy.  
- Nelze mít **implicitní bezparametrický konstruktor** (v C# 10+ lze, ale pouze pokud jsou všechna pole inicializována).  

---

### **7. Běžné chyby**  

- **Mutovatelné struktury**: Změna kopie neovlivní originál, což může vést k chybám.  
- **Velké struktury**: Časté kopírování může snížit výkon.  
- **Boxing/Unboxing**: Převod na `object` způsobí ztrátu výhod hodnotového typu.  

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

### **9. Doporučené postupy**  

1. **Zachovejte struktury malé**: Ideálně pod 16 bytů.  
2. **Preferujte imutabilitu**: Používejte `readonly struct` a `readonly` pole.  
3. **Používejte pro jednoduchá data**: Např. geometrické tvary, konfigurační hodnoty.  
4. **Vyhněte se boxing**: Nepřiřazujte struktury do `object` zbytečně.  

---

### **10. Shrnutí** 

- **Struktury** jsou hodnotové typy pro efektivní práci s malými daty.  
- **Kopírují se celé**, ne odkazem – vhodné pro časté krátkodobé operace.  
- **Imutabilita** je klíčová pro předvídatelné chování.  
