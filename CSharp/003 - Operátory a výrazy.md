
### **1. Kategorie oper�tor�**

#### **a) Aritmetick� oper�tory**

- `+` (s��t�n�), `-` (od��t�n�), `*` (n�soben�), `/` (d�len�), `%` (zbytek po d�len�).
- **P��klad**:  
  ```csharp
  int x = 10 + 5; // 15
  double y = 20.5 / 2; // 10.25
  ```

#### **b) Porovn�vac� oper�tory**

- `==` (rovnost), `!=` (nerovnost), `>` (v�t�� ne�), `<` (men�� ne�), `>=` (v�t�� nebo rovno), `<=` (men�� nebo rovno).
- **P��klad**:  
  ```csharp
  bool jeRovno = (5 == 5); // true
  bool jeVetsi = (10 > 20); // false
  ```

#### **c) Logick� oper�tory**

- `&&` (logick� AND), `||` (logick� OR), `!` (logick� NOT).
- **Short-circuit vyhodnocov�n�**:  
  `&&` a `||` zastav� vyhodnocov�n�, pokud je v�sledek jasn�.
  ```csharp
  if (x != null && x.Value > 0) { /* Bezpe�n�, pokud x je null, druh� podm�nka se nevyhodnocuje */ }
  ```

#### **d) Bitov� oper�tory**

- `&` (AND), `|` (OR), `^` (XOR), `~` (dopln�k), `<<` (posun vlevo), `>>` (posun vpravo).
- **P��klad**:  
  ```csharp
  int a = 5; // 0101 v bin
  int b = 3; // 0011 v bin
  int c = a & b; // 0001 (1)
  ```

#### **e) P�i�azovac� oper�tory**

- `=` (z�kladn� p�i�azen�), `+=`, `-=`, `*=`, `/=` (slo�en� oper�tory).
- **P��klad**:  
  ```csharp
  int x = 10;
  x += 5; // x = 15
  ```

#### **f) Inkrementace a dekrementace**

- `++` (zv��en� o 1), `--` (sn�en� o 1).
- **Prefix vs. postfix**:  
  ```csharp
  int a = 5;
  int b = a++; // b = 5, a = 6
  int c = ++a; // c = 7, a = 7
  ```

#### **g) Podm�nkov� oper�tor (`?:`)**

- Tern�rn� oper�tor pro podm�n�n� p�i�azen�.
- **Syntax**: `podm�nka ? hodnota_pokud_true : hodnota_pokud_false`.
- **P��klad**:  
  ```csharp
  int max = (a > b) ? a : b;
  ```

#### **h) Oper�tory pro pr�ci s typy**

- `is` (kontrola typu), `as` (bezpe�n� p�evod typu), `typeof` (z�sk�n� typu), `cast` (explicitn� p�evod).
- **P��klad**:  
  ```csharp
  if (obj is string s) { /* Pou�it� prom�nn� s */ }
  object text = "Ahoj";
  string? str = text as string; // str = "Ahoj"
  ```

#### **i) Null oper�tory**

- `??` (null-coalescing), `?.` (null-conditional), `??=` (null-coalescing assignment).
- **P��klad**:  
  ```csharp
  string jmeno = null;
  string vychozi = jmeno ?? "Nezn�m�"; // "Nezn�m�"
  int? delka = jmeno?.Length; // null
  jmeno ??= "Default"; // jmeno = "Default"
  ```

---

### **2. Priorita oper�tor�**

- Oper�tory s vy��� prioritou se vyhodnocuj� d��ve.  
- **P��klad po�ad�** (od nejvy��� priority):  
  1. `()` (z�vorky)  
  2. `++`, `--` (postfix), `new`, `typeof`  
  3. `*`, `/`, `%`  
  4. `+`, `-`  
  5. `<<`, `>>`  
  6. `<`, `>`, `<=`, `>=`, `is`, `as`  
  7. `==`, `!=`  
  8. `&`, `^`, `|`  
  9. `&&`, `||`  
  10. `??`, `?:`  
  11. `=`, `+=`, `-=`  

---

### **3. V�razy vs. p��kazy**

- **V�raz (expression)**: Vrac� hodnotu (nap�. `a + b`, `x > 5`).  
- **P��kaz (statement)**: Prov�d� akci (nap�. `if`, `for`, `return`).  

---

### **4. Speci�ln� oper�tory**

- **`nameof`**: Vr�t� n�zev prom�nn�, typu nebo �lena jako �et�zec.  
  ```csharp
  string nazev = nameof(Console.WriteLine); // "WriteLine"
  ```
- **`sizeof`**: Vr�t� velikost typu v bajtech (pouze v unsafe kontextu).  
  ```csharp
  int velikost = sizeof(int); // 4
  ```
- **`=>` (lambda oper�tor)**: Definuje lambda v�raz.  
  ```csharp
  Func<int, int> kvadrat = x => x * x;
  ```

---

### **5. P�et�en� oper�tor�**

- Umo��uje definovat vlastn� chov�n� oper�tor� pro t��dy/struktury.  
- **P��klad**:  
  ```csharp
  public struct Vektor {
      public int X { get; }
      public int Y { get; }
      
      public Vektor(int x, int y) => (X, Y) = (x, y);
      
      public static Vektor operator +(Vektor a, Vektor b) {
          return new Vektor(a.X + b.X, a.Y + b.Y);
      }
  }
  ```

---

### **6. B�n� v�razy**

- **Lambda v�razy**:  
  ```csharp
  Func<int, bool> jeSude = x => x % 2 == 0;
  ```
- **LINQ dotazy**:  
  ```csharp
  var vysledky = from cislo in numbers where cislo > 10 select cislo;
  ```
- **Switch v�razy** (C# 8.0+):  
  ```csharp
  string stav = cas switch {
      < 12 => "Dopoledne",
      >= 12 and < 18 => "Odpoledne",
      _ => "Ve�er"
  };
  ```

---

### **7. Tipy a �ast� chyby**

- **Z�m�na `==` a `.Equals()`**:  
  `==` porovn�v� reference u referen�n�ch typ� (pokud nen� p�et�en), `.Equals()` porovn�v� obsah.  
- **Short-circuit vs. non-short-circuit**:  
  `&&` a `||` vs. `&` a `|` (u ��seln�ch typ� bitov� operace).  
- **P�ete�en� (overflow)**:  
  ```csharp
  checked { int max = int.MaxValue + 1; } // Vyvol� v�jimku
  ```
- **Imutabilita �et�zc�**:  
  ```csharp
  string s = "Ahoj";
  s += "!"; // Vytvo�� nov� �et�zec, neuprav� p�vodn�.
  ```

---

### **8. Uk�zka k�du**

```csharp
// P�et�en� oper�toru a pou�it� null oper�tor�
public class Bod {
    public int X { get; }
    public int Y { get; }
    
    public Bod(int x, int y) => (X, Y) = (x, y);
    
    public static Bod operator +(Bod a, Bod b) => new Bod(a.X + b.X, a.Y + b.Y);
}

// Pou�it�
Bod? b1 = new Bod(2, 3);
Bod b2 = b1 ?? new Bod(0, 0);
Bod soucet = b1 + b2;

// Lambda a switch v�raz
var cisla = new List<int> { 1, 2, 3, 4 };
var suda = cisla.Where(x => x % 2 == 0).ToList();

string den = DenVTydnu.Pondeli;
string typDne = den switch {
    "Pondeli" or "Utery" or "Streda" or "Ctvrtek" or "Patek" => "Pracovn� den",
    _ => "V�kend"
};
```
