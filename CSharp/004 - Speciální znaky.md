
### **1. Escape sekvence v øetìzcích**

Speciální znaky pro formátování textu a reprezentaci neviditelných symbolù.  

- `\"` – Uvozovka uvnitø øetìzce.  
  ```csharp
  string text = "Øekl: \"Ahoj!\"";
  ```
- `\'` – Apostrof uvnitø char literálu.  
  ```csharp
  char apostrof = '\'';
  ```
- `\\` – Zpìtné lomítko (napø. pro cesty v Windows).  
  ```csharp
  string cesta = "C:\\Users\\Uživatel";
  ```
- `\n` – Nový øádek.  
- `\t` – Tabulátor.  
- `\r` – Návrat vozíku (èasto s `\n` jako `\r\n`).  
- `\uXXXX` – Unicode znak (napø. `\u00A9` pro ©).  
- `\xXXXX` – Hexadecimální reprezentace znaku.  

---

### **2. Verbatim identifikátor `@`**

- **Ignoruje escape sekvence** v øetìzcích a umožòuje víceøádkové texty.  
  ```csharp
  string cesta = @"C:\Users\Uživatel\Dokumenty";
  string html = @"<html>
                  <body></body>
                </html>";
  ```
- **Použití klíèových slov jako identifikátorù**:  
  ```csharp
  int @class = 10; // Promìnná se jménem "class"
  ```

---

### **3. Interpolace øetìzcù `$`**

- Vkládání výrazù pøímo do øetìzcù.  
  ```csharp
  int vìk = 25;
  string zpráva = $"Vìk: {vìk}"; // "Vìk: 25"
  ```
- **Formátování**:  
  ```csharp
  double cena = 99.95;
  string výpis = $"{cena:C2}"; // "99,95 Kè"
  ```

---

### **4. Operátor `nameof`**

- Vrátí název promìnné, metody nebo typu jako øetìzec.  
  ```csharp
  string název = nameof(Console.WriteLine); // "WriteLine"
  ```

---

### **5. Null operátory**

- `?.` (**null-conditional**) – Bezpeèný pøístup k èlenùm objektu.  
  ```csharp
  string? text = null;
  int? délka = text?.Length; // null (nevyvolá výjimku)
  ```
- `??` (**null-coalescing**) – Výchozí hodnota pro `null`.  
  ```csharp
  string jméno = null;
  string výsledek = jméno ?? "Neznámý"; // "Neznámý"
  ```
- `??=` (**null-coalescing assignment**) – Pøiøazení, pokud je promìnná `null`.  
  ```csharp
  string? hodnota = null;
  hodnota ??= "Default"; // hodnota = "Default"
  ```

---

### **6. Lambda operátor `=>`**

- Definice lambda výrazù a tìla metod.  
  ```csharp
  Func<int, int> druháMocnina = x => x * x;
  ```

---

### **7. Bitové a logické operátory**

- `&` (AND), `|` (OR), `^` (XOR), `~` (doplnìk).  
  ```csharp
  int a = 5 & 3; // 1 (0101 & 0011 = 0001)
  ```
- `<<` (posun vlevo), `>>` (posun vpravo).  
  ```csharp
  int b = 8 << 1; // 16 (posun o 1 bit vlevo).
  ```

---

### **8. Operátor `#` (preprocesorové direktivy)**

- Instrukce pro kompilátor pøed zpracováním kódu.  
  ```csharp
  #define DEBUG
  #if DEBUG
      Console.WriteLine("Debug režim");
  #endif
  ```

---

### **9. Pointerové operátory (unsafe kontext)**

- `*` (dereference ukazatele), `&` (adresa promìnné).  
  ```csharp
  unsafe {
      int x = 10;
      int* ptr = &x;
      Console.WriteLine(*ptr); // 10
  }
  ```

---

### **10. Raw string literals (C# 11+)**

- **Víceøádkové øetìzce bez escape sekvencí** pomocí `"""`.  
  ```csharp
  string json = """
  {
    "Jméno": "Karel",
    "Vìk": 30
  }
  """;
  ```

---

### **11. Speciální symboly v komentáøích**

- `///` – Dokumentaèní komentáøe (pro XML dokumentaci).  
- `/* */` – Víceøádkové komentáøe.  
- `//` – Jednoøádkový komentáø.  

---

### **12. Bìžné chyby a tipy**

- **Zámìna `""` a `''`**:  
  ```csharp
  char znak = 'A'; // Správnì
  string text = "A"; // Správnì
  ```
- **Kombinace `@` a `$`**:  
  ```csharp
  string cesta = $@"C:\Uživatel\{jméno}\Dokumenty";
  ```
- **Escape uvnitø interpolovaných øetìzcù**:  
  ```csharp
  string cesta = $"C:\\Users\\{jméno}"; // Nutné escapovat!
  ```

---

### **13. Ukázka kódu**

```csharp
// Kombinace operátorù
string? input = null;
string output = input?.ToUpper() ?? "NIC"; // "NIC"

// Raw string literal
string html = """
    <div class="container">
        <p>Ahoj!</p>
    </div>
    """;

// Lambda výraz
var èísla = new List<int> { 1, 2, 3 };
var sudá = èísla.Where(x => x % 2 == 0).ToList();
```
