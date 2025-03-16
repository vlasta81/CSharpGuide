
### **1. Kategorie operátorù**

#### **a) Aritmetické operátory**

- `+` (sèítání), `-` (odèítání), `*` (násobení), `/` (dìlení), `%` (zbytek po dìlení).
- **Pøíklad**:  
  ```csharp
  int x = 10 + 5; // 15
  double y = 20.5 / 2; // 10.25
  ```

#### **b) Porovnávací operátory**

- `==` (rovnost), `!=` (nerovnost), `>` (vìtší než), `<` (menší než), `>=` (vìtší nebo rovno), `<=` (menší nebo rovno).
- **Pøíklad**:  
  ```csharp
  bool jeRovno = (5 == 5); // true
  bool jeVetsi = (10 > 20); // false
  ```

#### **c) Logické operátory**

- `&&` (logické AND), `||` (logické OR), `!` (logické NOT).
- **Short-circuit vyhodnocování**:  
  `&&` a `||` zastaví vyhodnocování, pokud je výsledek jasný.
  ```csharp
  if (x != null && x.Value > 0) { /* Bezpeèné, pokud x je null, druhá podmínka se nevyhodnocuje */ }
  ```

#### **d) Bitové operátory**

- `&` (AND), `|` (OR), `^` (XOR), `~` (doplnìk), `<<` (posun vlevo), `>>` (posun vpravo).
- **Pøíklad**:  
  ```csharp
  int a = 5; // 0101 v bin
  int b = 3; // 0011 v bin
  int c = a & b; // 0001 (1)
  ```

#### **e) Pøiøazovací operátory**

- `=` (základní pøiøazení), `+=`, `-=`, `*=`, `/=` (složené operátory).
- **Pøíklad**:  
  ```csharp
  int x = 10;
  x += 5; // x = 15
  ```

#### **f) Inkrementace a dekrementace**

- `++` (zvýšení o 1), `--` (snížení o 1).
- **Prefix vs. postfix**:  
  ```csharp
  int a = 5;
  int b = a++; // b = 5, a = 6
  int c = ++a; // c = 7, a = 7
  ```

#### **g) Podmínkový operátor (`?:`)**

- Ternární operátor pro podmínìné pøiøazení.
- **Syntax**: `podmínka ? hodnota_pokud_true : hodnota_pokud_false`.
- **Pøíklad**:  
  ```csharp
  int max = (a > b) ? a : b;
  ```

#### **h) Operátory pro práci s typy**

- `is` (kontrola typu), `as` (bezpeèný pøevod typu), `typeof` (získání typu), `cast` (explicitní pøevod).
- **Pøíklad**:  
  ```csharp
  if (obj is string s) { /* Použití promìnné s */ }
  object text = "Ahoj";
  string? str = text as string; // str = "Ahoj"
  ```

#### **i) Null operátory**

- `??` (null-coalescing), `?.` (null-conditional), `??=` (null-coalescing assignment).
- **Pøíklad**:  
  ```csharp
  string jmeno = null;
  string vychozi = jmeno ?? "Neznámé"; // "Neznámé"
  int? delka = jmeno?.Length; // null
  jmeno ??= "Default"; // jmeno = "Default"
  ```

---

### **2. Priorita operátorù**

- Operátory s vyšší prioritou se vyhodnocují døíve.  
- **Pøíklad poøadí** (od nejvyšší priority):  
  1. `()` (závorky)  
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

### **3. Výrazy vs. pøíkazy**

- **Výraz (expression)**: Vrací hodnotu (napø. `a + b`, `x > 5`).  
- **Pøíkaz (statement)**: Provádí akci (napø. `if`, `for`, `return`).  

---

### **4. Speciální operátory**

- **`nameof`**: Vrátí název promìnné, typu nebo èlena jako øetìzec.  
  ```csharp
  string nazev = nameof(Console.WriteLine); // "WriteLine"
  ```
- **`sizeof`**: Vrátí velikost typu v bajtech (pouze v unsafe kontextu).  
  ```csharp
  int velikost = sizeof(int); // 4
  ```
- **`=>` (lambda operátor)**: Definuje lambda výraz.  
  ```csharp
  Func<int, int> kvadrat = x => x * x;
  ```

---

### **5. Pøetížení operátorù**

- Umožòuje definovat vlastní chování operátorù pro tøídy/struktury.  
- **Pøíklad**:  
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

### **6. Bìžné výrazy**

- **Lambda výrazy**:  
  ```csharp
  Func<int, bool> jeSude = x => x % 2 == 0;
  ```
- **LINQ dotazy**:  
  ```csharp
  var vysledky = from cislo in numbers where cislo > 10 select cislo;
  ```
- **Switch výrazy** (C# 8.0+):  
  ```csharp
  string stav = cas switch {
      < 12 => "Dopoledne",
      >= 12 and < 18 => "Odpoledne",
      _ => "Veèer"
  };
  ```

---

### **7. Tipy a èasté chyby**

- **Zámìna `==` a `.Equals()`**:  
  `==` porovnává reference u referenèních typù (pokud není pøetížen), `.Equals()` porovnává obsah.  
- **Short-circuit vs. non-short-circuit**:  
  `&&` a `||` vs. `&` a `|` (u èíselných typù bitové operace).  
- **Pøeteèení (overflow)**:  
  ```csharp
  checked { int max = int.MaxValue + 1; } // Vyvolá výjimku
  ```
- **Imutabilita øetìzcù**:  
  ```csharp
  string s = "Ahoj";
  s += "!"; // Vytvoøí nový øetìzec, neupraví pùvodní.
  ```

---

### **8. Ukázka kódu**

```csharp
// Pøetížení operátoru a použití null operátorù
public class Bod {
    public int X { get; }
    public int Y { get; }
    
    public Bod(int x, int y) => (X, Y) = (x, y);
    
    public static Bod operator +(Bod a, Bod b) => new Bod(a.X + b.X, a.Y + b.Y);
}

// Použití
Bod? b1 = new Bod(2, 3);
Bod b2 = b1 ?? new Bod(0, 0);
Bod soucet = b1 + b2;

// Lambda a switch výraz
var cisla = new List<int> { 1, 2, 3, 4 };
var suda = cisla.Where(x => x % 2 == 0).ToList();

string den = DenVTydnu.Pondeli;
string typDne = den switch {
    "Pondeli" or "Utery" or "Streda" or "Ctvrtek" or "Patek" => "Pracovní den",
    _ => "Víkend"
};
```
