
### **1. Escape sekvence v �et�zc�ch**

Speci�ln� znaky pro form�tov�n� textu a reprezentaci neviditeln�ch symbol�.  

- `\"` � Uvozovka uvnit� �et�zce.  
  ```csharp
  string text = "�ekl: \"Ahoj!\"";
  ```
- `\'` � Apostrof uvnit� char liter�lu.  
  ```csharp
  char apostrof = '\'';
  ```
- `\\` � Zp�tn� lom�tko (nap�. pro cesty v Windows).  
  ```csharp
  string cesta = "C:\\Users\\U�ivatel";
  ```
- `\n` � Nov� ��dek.  
- `\t` � Tabul�tor.  
- `\r` � N�vrat voz�ku (�asto s `\n` jako `\r\n`).  
- `\uXXXX` � Unicode znak (nap�. `\u00A9` pro �).  
- `\xXXXX` � Hexadecim�ln� reprezentace znaku.  

---

### **2. Verbatim identifik�tor `@`**

- **Ignoruje escape sekvence** v �et�zc�ch a umo��uje v�ce��dkov� texty.  
  ```csharp
  string cesta = @"C:\Users\U�ivatel\Dokumenty";
  string html = @"<html>
                  <body></body>
                </html>";
  ```
- **Pou�it� kl��ov�ch slov jako identifik�tor�**:  
  ```csharp
  int @class = 10; // Prom�nn� se jm�nem "class"
  ```

---

### **3. Interpolace �et�zc� `$`**

- Vkl�d�n� v�raz� p��mo do �et�zc�.  
  ```csharp
  int v�k = 25;
  string zpr�va = $"V�k: {v�k}"; // "V�k: 25"
  ```
- **Form�tov�n�**:  
  ```csharp
  double cena = 99.95;
  string v�pis = $"{cena:C2}"; // "99,95 K�"
  ```

---

### **4. Oper�tor `nameof`**

- Vr�t� n�zev prom�nn�, metody nebo typu jako �et�zec.  
  ```csharp
  string n�zev = nameof(Console.WriteLine); // "WriteLine"
  ```

---

### **5. Null oper�tory**

- `?.` (**null-conditional**) � Bezpe�n� p��stup k �len�m objektu.  
  ```csharp
  string? text = null;
  int? d�lka = text?.Length; // null (nevyvol� v�jimku)
  ```
- `??` (**null-coalescing**) � V�choz� hodnota pro `null`.  
  ```csharp
  string jm�no = null;
  string v�sledek = jm�no ?? "Nezn�m�"; // "Nezn�m�"
  ```
- `??=` (**null-coalescing assignment**) � P�i�azen�, pokud je prom�nn� `null`.  
  ```csharp
  string? hodnota = null;
  hodnota ??= "Default"; // hodnota = "Default"
  ```

---

### **6. Lambda oper�tor `=>`**

- Definice lambda v�raz� a t�la metod.  
  ```csharp
  Func<int, int> druh�Mocnina = x => x * x;
  ```

---

### **7. Bitov� a logick� oper�tory**

- `&` (AND), `|` (OR), `^` (XOR), `~` (dopln�k).  
  ```csharp
  int a = 5 & 3; // 1 (0101 & 0011 = 0001)
  ```
- `<<` (posun vlevo), `>>` (posun vpravo).  
  ```csharp
  int b = 8 << 1; // 16 (posun o 1 bit vlevo).
  ```

---

### **8. Oper�tor `#` (preprocesorov� direktivy)**

- Instrukce pro kompil�tor p�ed zpracov�n�m k�du.  
  ```csharp
  #define DEBUG
  #if DEBUG
      Console.WriteLine("Debug re�im");
  #endif
  ```

---

### **9. Pointerov� oper�tory (unsafe kontext)**

- `*` (dereference ukazatele), `&` (adresa prom�nn�).  
  ```csharp
  unsafe {
      int x = 10;
      int* ptr = &x;
      Console.WriteLine(*ptr); // 10
  }
  ```

---

### **10. Raw string literals (C# 11+)**

- **V�ce��dkov� �et�zce bez escape sekvenc�** pomoc� `"""`.  
  ```csharp
  string json = """
  {
    "Jm�no": "Karel",
    "V�k": 30
  }
  """;
  ```

---

### **11. Speci�ln� symboly v koment���ch**

- `///` � Dokumenta�n� koment��e (pro XML dokumentaci).  
- `/* */` � V�ce��dkov� koment��e.  
- `//` � Jedno��dkov� koment��.  

---

### **12. B�n� chyby a tipy**

- **Z�m�na `""` a `''`**:  
  ```csharp
  char znak = 'A'; // Spr�vn�
  string text = "A"; // Spr�vn�
  ```
- **Kombinace `@` a `$`**:  
  ```csharp
  string cesta = $@"C:\U�ivatel\{jm�no}\Dokumenty";
  ```
- **Escape uvnit� interpolovan�ch �et�zc�**:  
  ```csharp
  string cesta = $"C:\\Users\\{jm�no}"; // Nutn� escapovat!
  ```

---

### **13. Uk�zka k�du**

```csharp
// Kombinace oper�tor�
string? input = null;
string output = input?.ToUpper() ?? "NIC"; // "NIC"

// Raw string literal
string html = """
    <div class="container">
        <p>Ahoj!</p>
    </div>
    """;

// Lambda v�raz
var ��sla = new List<int> { 1, 2, 3 };
var sud� = ��sla.Where(x => x % 2 == 0).ToList();
```
