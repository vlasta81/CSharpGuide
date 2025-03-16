
### **1. Definice struktury**  

Struktura je **hodnotov� typ** (ukl�d� data p��mo v pam�ti, typicky na z�sobn�ku), kter� slou�� k vytv��en� lehk�ch objekt� pro ukl�d�n� dat.  
- **Kl��ov� vlastnosti**:  
  - Nem��e d�dit z jin� t��dy/struktury (krom� implementace rozhran�).  
  - M� v�choz� konstruktor bez parametr� (nelze jej odstranit).  
  - Vhodn� pro mal�, nem�nn� datov� struktury (nap�. sou�adnice, barvy).  

---

### **2. Syntaxe a p��klad**  

```csharp
public struct Bod 
{
    public int X; 
    public int Y;

    // Konstruktor s parametry (mus� inicializovat v�echna pole)
    public Bod(int x, int y) 
    {
        X = x;
        Y = y;
    }

    // Metoda ve struktu�e
    public void Posun(int dx, int dy) 
    {
        X += dx;
        Y += dy;
    }
}

// Pou�it�:
Bod bod = new Bod(10, 20);
bod.Posun(5, 5); // X=15, Y=25
```

---

### **3. Kl��ov� vlastnosti** 

#### **a) Hodnotov� s�mantika**  

- P�i�azen� vytv��� **kopii dat** (ne odkaz).  
```csharp
Bod b1 = new Bod(5, 5);
Bod b2 = b1; // Kopie hodnoty
b2.X = 10;   // b1.X z�st�v� 5
```

#### **b) ��dn� d�di�nost**  

- Struktura m��e implementovat rozhran�, ale nem��e d�dit z jin� struktury/t��dy.  
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

- Doporu�uje se pro struktury, aby se p�ede�lo neo�ek�van�mu chov�n� (kop�rov�n� hodnot).  
```csharp
public readonly struct Nem�nnyBod 
{
    public readonly int X;
    public readonly int Y;
    
    public Nem�nnyBod(int x, int y) 
    {
        X = x;
        Y = y;
    }
}
```

---

### **4. Struktura vs. t��da**  

| **Krit�rium**       | **Struktura (struct)**          | **T��da (class)**             |  
|----------------------|---------------------------------|-------------------------------|  
| **Typ**              | Hodnotov� (stack)               | Referen�n� (heap)             |  
| **D�di�nost**        | Nelze d�dit                     | Podporuje d�di�nost           |  
| **V�choz� konstruktor** | Automatick� (nelze odstranit) | Bez parametru (lze definovat) |  
| **Nullabilita**      | Nelze (krom� `Nullable<T>`)     | Ano                           |  
| **Velikost**         | Doporu�eno do 16�24 byt�        | ��dn� omezen�                 |  

---

### **5. Kdy pou��t strukturu?** 

- **Mal� datov� struktury**: Nap�. `DateTime`, `Point`, `Complex`.  
- **�ast� kop�rov�n�**: Kdy� je kop�rov�n� levn�j�� ne� spr�va referenc�.  
- **Kr�tk� �ivotnost**: Data nepot�ebuj� dlouhodob� uchov�n�.  

---

### **6. Omezen� struktur**  

- Nelze definovat **destruktor**.  
- Nelze pou��t `abstract`, `sealed`, nebo `protected` �leny.  
- Nelze m�t **implicitn� bezparametrick� konstruktor** (v C# 10+ lze, ale pouze pokud jsou v�echna pole inicializov�na).  

---

### **7. B�n� chyby**  

- **Mutovateln� struktury**: Zm�na kopie neovlivn� origin�l, co� m��e v�st k chyb�m.  
- **Velk� struktury**: �ast� kop�rov�n� m��e sn�it v�kon.  
- **Boxing/Unboxing**: P�evod na `object` zp�sob� ztr�tu v�hod hodnotov�ho typu.  

---

### **8. Uk�zky k�du**  

#### **Struktura s rozhran�m**  

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

### **9. Doporu�en� postupy**  

1. **Zachovejte struktury mal�**: Ide�ln� pod 16 byt�.  
2. **Preferujte imutabilitu**: Pou��vejte `readonly struct` a `readonly` pole.  
3. **Pou��vejte pro jednoduch� data**: Nap�. geometrick� tvary, konfigura�n� hodnoty.  
4. **Vyhn�te se boxing**: Nep�i�azujte struktury do `object` zbyte�n�.  

---

### **10. Shrnut�** 

- **Struktury** jsou hodnotov� typy pro efektivn� pr�ci s mal�mi daty.  
- **Kop�ruj� se cel�**, ne odkazem � vhodn� pro �ast� kr�tkodob� operace.  
- **Imutabilita** je kl��ov� pro p�edv�dateln� chov�n�.  
