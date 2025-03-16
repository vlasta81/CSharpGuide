
### **1. Úvod do přetěžování operátorů**

Přetěžování operátorů umožňuje **definovat vlastní chování operátorů** (např. `+`, `==`, `>`) pro uživatelské typy (třídy, struktury).  
**Účel**: Zjednodušení syntaxe a zpřehlednění kódu pro práci s objekty.

---

### **2. Základní pravidla**

- **Statické metody**: Přetížené operátory musí být `static` a `public`.
- **Typy operátorů**:
  - Unární (např. `++`, `-`, `!`).
  - Binární (např. `+`, `-`, `*`, `/`, `==`, `!=`).
- **Nelze přetížit**: 
  - Operátory přiřazení (`=`, `+=`, `&&`, `||`).
  - Operátor `??`, `?.`, `?[]`, `new`, `is`, `as`.

---

### **3. Syntaxe a příklady**

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

// Nutno přepsat Equals a GetHashCode!
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

### **4. Implicitní a explicitní převody**

#### **a) Implicitní převod**  

- Proběhne automaticky (bez ztráty dat).  
```csharp
public static implicit operator double(Vektor v) 
{
    return Math.Sqrt(v.X * v.X + v.Y * v.Y); // Délka vektoru
}

// Použití:
double delka = new Vektor { X = 3, Y = 4 }; // 5.0
```

#### **b) Explicitní převod**  

- Vyžaduje explicitní přetypování.  
```csharp
public static explicit operator int(Vektor v) 
{
    return v.X + v.Y;
}

// Použití:
int suma = (int)new Vektor { X = 2, Y = 3 }; // 5
```

---

### **5. Důležité tipy**

1. **Dvojice operátorů**: Pokud přetěžujete `==`, musíte přetížit i `!=` (a naopak).
2. **Konzistence s `Equals` a `GetHashCode`**: Zajistěte, aby `==` a `Equals` dávaly stejný výsledek.
3. **Přehlednost**: Používejte přetěžování pouze tam, kde dává smysl (např. matematické operace).
4. **Výkon**: Přetěžování může skrývat nákladné operace – buďte transparentní.

---

### **6. Ukázka reálného použití**

#### **Komplexní čísla** 

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

- Nelze přetížit operátor přiřazení (`=`).
- Nelze změnit prioritu nebo asociativitu operátorů.
- Operátory `true` a `false` lze přetížit, ale musí být v páru.

---

### **8. Doporučené zdroje**

- **Oficiální dokumentace**: [Operator Overloading](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/operators/operator-overloading)  
