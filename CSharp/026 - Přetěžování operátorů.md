
### **1. Úvod do pøetìžování operátorù**

Pøetìžování operátorù umožòuje **definovat vlastní chování operátorù** (napø. `+`, `==`, `>`) pro uživatelské typy (tøídy, struktury).  
**Úèel**: Zjednodušení syntaxe a zpøehlednìní kódu pro práci s objekty.

---

### **2. Základní pravidla**

- **Statické metody**: Pøetížené operátory musí být `static` a `public`.
- **Typy operátorù**:
  - Unární (napø. `++`, `-`, `!`).
  - Binární (napø. `+`, `-`, `*`, `/`, `==`, `!=`).
- **Nelze pøetížit**: 
  - Operátory pøiøazení (`=`, `+=`, `&&`, `||`).
  - Operátor `??`, `?.`, `?[]`, `new`, `is`, `as`.

---

### **3. Syntaxe a pøíklady**

#### **a) Binární operátor (`+`)**  

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

// Použití:
Vektor v1 = new Vektor { X = 2, Y = 3 };
Vektor v2 = new Vektor { X = 1, Y = 4 };
Vektor soucet = v1 + v2; // X=3, Y=7
```

#### **b) Porovnávací operátory (`==`, `!=`)**  

```csharp
public static bool operator ==(Vektor a, Vektor b) 
{
    return a?.X == b?.X && a?.Y == b?.Y;
}

public static bool operator !=(Vektor a, Vektor b) 
{
    return !(a == b);
}

// Nutno pøepsat Equals a GetHashCode!
public override bool Equals(object obj) => obj is Vektor v && this == v;
public override int GetHashCode() => HashCode.Combine(X, Y);
```

#### **c) Unární operátor (`++`)**  

```csharp
public static Vektor operator ++(Vektor v) 
{
    return new Vektor { X = v.X + 1, Y = v.Y + 1 };
}

// Použití:
Vektor v = new Vektor { X = 5, Y = 5 };
v++; // X=6, Y=6
```

---

### **4. Implicitní a explicitní pøevody**

#### **a) Implicitní pøevod**  

- Probìhne automaticky (bez ztráty dat).  
```csharp
public static implicit operator double(Vektor v) 
{
    return Math.Sqrt(v.X * v.X + v.Y * v.Y); // Délka vektoru
}

// Použití:
double delka = new Vektor { X = 3, Y = 4 }; // 5.0
```

#### **b) Explicitní pøevod**  

- Vyžaduje explicitní pøetypování.  
```csharp
public static explicit operator int(Vektor v) 
{
    return v.X + v.Y;
}

// Použití:
int suma = (int)new Vektor { X = 2, Y = 3 }; // 5
```

---

### **5. Dùležité tipy**

1. **Dvojice operátorù**: Pokud pøetìžujete `==`, musíte pøetížit i `!=` (a naopak).
2. **Konzistence s `Equals` a `GetHashCode`**: Zajistìte, aby `==` a `Equals` dávaly stejný výsledek.
3. **Pøehlednost**: Používejte pøetìžování pouze tam, kde dává smysl (napø. matematické operace).
4. **Výkon**: Pøetìžování mùže skrývat nákladné operace – buïte transparentní.

---

### **6. Ukázka reálného použití**

#### **Komplexní èísla** 

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

// Použití:
KomplexniCislo c1 = new KomplexniCislo { Real = 2, Imag = 3 };
KomplexniCislo c2 = new KomplexniCislo { Real = 1, Imag = 4 };
Console.WriteLine(c1 + c2); // "3 + 7i"
```

---

### **7. Omezení**

- Nelze pøetížit operátor pøiøazení (`=`).
- Nelze zmìnit prioritu nebo asociativitu operátorù.
- Operátory `true` a `false` lze pøetížit, ale musí být v páru.

---

### **8. Doporuèené zdroje**

- **Oficiální dokumentace**: [Operator Overloading](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/operators/operator-overloading)  
