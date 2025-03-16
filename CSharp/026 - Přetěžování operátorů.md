
### **1. �vod do p�et�ov�n� oper�tor�**

P�et�ov�n� oper�tor� umo��uje **definovat vlastn� chov�n� oper�tor�** (nap�. `+`, `==`, `>`) pro u�ivatelsk� typy (t��dy, struktury).  
**��el**: Zjednodu�en� syntaxe a zp�ehledn�n� k�du pro pr�ci s objekty.

---

### **2. Z�kladn� pravidla**

- **Statick� metody**: P�et�en� oper�tory mus� b�t `static` a `public`.
- **Typy oper�tor�**:
  - Un�rn� (nap�. `++`, `-`, `!`).
  - Bin�rn� (nap�. `+`, `-`, `*`, `/`, `==`, `!=`).
- **Nelze p�et�it**: 
  - Oper�tory p�i�azen� (`=`, `+=`, `&&`, `||`).
  - Oper�tor `??`, `?.`, `?[]`, `new`, `is`, `as`.

---

### **3. Syntaxe a p��klady**

#### **a) Bin�rn� oper�tor (`+`)**  

```csharp
public class Vektor 
{
    public int X { get; set; }
    public int Y { get; set; }

    public static Vektor operator +(Vektor a, Vektor b) 
    {
        return new Vektor { X = a.X + b.X, Y = a.Y + b.Y };
    }
}

// Pou�it�:
Vektor v1 = new Vektor { X = 2, Y = 3 };
Vektor v2 = new Vektor { X = 1, Y = 4 };
Vektor soucet = v1 + v2; // X=3, Y=7
```

#### **b) Porovn�vac� oper�tory (`==`, `!=`)**  

```csharp
public static bool operator ==(Vektor a, Vektor b) 
{
    return a?.X == b?.X && a?.Y == b?.Y;
}

public static bool operator !=(Vektor a, Vektor b) 
{
    return !(a == b);
}

// Nutno p�epsat Equals a GetHashCode!
public override bool Equals(object obj) => obj is Vektor v && this == v;
public override int GetHashCode() => HashCode.Combine(X, Y);
```

#### **c) Un�rn� oper�tor (`++`)**  

```csharp
public static Vektor operator ++(Vektor v) 
{
    return new Vektor { X = v.X + 1, Y = v.Y + 1 };
}

// Pou�it�:
Vektor v = new Vektor { X = 5, Y = 5 };
v++; // X=6, Y=6
```

---

### **4. Implicitn� a explicitn� p�evody**

#### **a) Implicitn� p�evod**  

- Prob�hne automaticky (bez ztr�ty dat).  
```csharp
public static implicit operator double(Vektor v) 
{
    return Math.Sqrt(v.X * v.X + v.Y * v.Y); // D�lka vektoru
}

// Pou�it�:
double delka = new Vektor { X = 3, Y = 4 }; // 5.0
```

#### **b) Explicitn� p�evod**  

- Vy�aduje explicitn� p�etypov�n�.  
```csharp
public static explicit operator int(Vektor v) 
{
    return v.X + v.Y;
}

// Pou�it�:
int suma = (int)new Vektor { X = 2, Y = 3 }; // 5
```

---

### **5. D�le�it� tipy**

1. **Dvojice oper�tor�**: Pokud p�et�ujete `==`, mus�te p�et�it i `!=` (a naopak).
2. **Konzistence s `Equals` a `GetHashCode`**: Zajist�te, aby `==` a `Equals` d�valy stejn� v�sledek.
3. **P�ehlednost**: Pou��vejte p�et�ov�n� pouze tam, kde d�v� smysl (nap�. matematick� operace).
4. **V�kon**: P�et�ov�n� m��e skr�vat n�kladn� operace � bu�te transparentn�.

---

### **6. Uk�zka re�ln�ho pou�it�**

#### **Komplexn� ��sla** 

```csharp
public class KomplexniCislo 
{
    public double Real { get; set; }
    public double Imag { get; set; }

    public static KomplexniCislo operator +(KomplexniCislo a, KomplexniCislo b) 
    {
        return new KomplexniCislo 
        { 
            Real = a.Real + b.Real, 
            Imag = a.Imag + b.Imag 
        };
    }

    public override string ToString() => $"{Real} + {Imag}i";
}

// Pou�it�:
KomplexniCislo c1 = new KomplexniCislo { Real = 2, Imag = 3 };
KomplexniCislo c2 = new KomplexniCislo { Real = 1, Imag = 4 };
Console.WriteLine(c1 + c2); // "3 + 7i"
```

---

### **7. Omezen�**

- Nelze p�et�it oper�tor p�i�azen� (`=`).
- Nelze zm�nit prioritu nebo asociativitu oper�tor�.
- Oper�tory `true` a `false` lze p�et�it, ale mus� b�t v p�ru.

---

### **8. Doporu�en� zdroje**

- **Ofici�ln� dokumentace**: [Operator Overloading](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/operators/operator-overloading)  
