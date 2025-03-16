
### **1. Escape sekvence v řetězcích**

Speciální znaky pro formátování textu a reprezentaci neviditelných symbolů.  

- `\"` – Uvozovka uvnitř řetězce.  
  ```csharp
  string text = "Řekl: \"Ahoj!\"";
  ```
- `\'` – Apostrof uvnitř char literálu.  
  ```csharp
  char apostrof = '\'';
  ```
- `\\` – Zpětné lomítko (např. pro cesty v Windows).  
  ```csharp
  string cesta = "C:\\Users\\Uživatel";
  ```
- `\n` – Nový řádek.  
- `\t` – Tabulátor.  
- `\r` – Návrat vozíku (často s `\n` jako `\r\n`).  
- `\uXXXX` – Unicode znak (např. `\u00A9` pro ©).  
- `\xXXXX` – Hexadecimální reprezentace znaku.  

---

### **2. Verbatim identifikátor `@`**

- **Ignoruje escape sekvence** v řetězcích a umožňuje víceřádkové texty.  
  ```csharp
  string cesta = @"C:\Users\Uživatel\Dokumenty";
  string html = @"<html>
                  <body></body>
                </html>";
  ```
- **Použití klíčových slov jako identifikátorů**:  
  ```csharp
  int @class = 10; // Proměnná se jménem "class"
  ```

---

### **3. Interpolace řetězců `$`**

- Vkládání výrazů přímo do řetězců.  
  ```csharp
  int věk = 25;
  string zpráva = $"Věk: {věk}"; // "Věk: 25"
  ```
- **Formátování**:  
  ```csharp
  double cena = 99.95;
  string výpis = $"{cena:C2}"; // "99,95 Kč"
  ```

---

### **4. Operátor `nameof`**

- Vrátí název proměnné, metody nebo typu jako řetězec.  
  ```csharp
  string název = nameof(Console.WriteLine); // "WriteLine"
  ```

---

### **5. Null operátory**

- `?.` (**null-conditional**) – Bezpečný přístup k členům objektu.  
  ```csharp
  string? text = null;
  int? délka = text?.Length; // null (nevyvolá výjimku)
  ```
- `??` (**null-coalescing**) – Výchozí hodnota pro `null`.  
  ```csharp
  string jméno = null;
  string výsledek = jméno ?? "Neznámý"; // "Neznámý"
  ```
- `??=` (**null-coalescing assignment**) – Přiřazení, pokud je proměnná `null`.  
  ```csharp
  string? hodnota = null;
  hodnota ??= "Default"; // hodnota = "Default"
  ```

---

### **6. Lambda operátor `=>`**

- Definice lambda výrazů a těla metod.  
  ```csharp
  Func<int, int> druháMocnina = x => x * x;
  ```

---

### **7. Bitové a logické operátory**

- `&` (AND), `|` (OR), `^` (XOR), `~` (doplněk).  
  ```csharp
  int a = 5 & 3; // 1 (0101 & 0011 = 0001)
  ```
- `<<` (posun vlevo), `>>` (posun vpravo).  
  ```csharp
  int b = 8 << 1; // 16 (posun o 1 bit vlevo).
  ```

---

### **8. Operátor `#` (preprocesorové direktivy)**

- Instrukce pro kompilátor před zpracováním kódu.  
  ```csharp
  #define DEBUG
  #if DEBUG
      Console.WriteLine("Debug režim");
  #endif
  ```

---

### **9. Pointerové operátory (unsafe kontext)**

- `*` (dereference ukazatele), `&` (adresa proměnné).  
  ```csharp
  unsafe {
      int x = 10;
      int* ptr = &x;
      Console.WriteLine(*ptr); // 10
  }
  ```

---

### **10. Raw string literals (C# 11+)**

- **Víceřádkové řetězce bez escape sekvencí** pomocí `"""`.  
  ```csharp
  string json = """
  {
    "Jméno": "Karel",
    "Věk": 30
  }
  """;
  ```

---

### **11. Speciální symboly v komentářích**

- `///` – Dokumentační komentáře (pro XML dokumentaci).  
- `/* */` – Víceřádkové komentáře.  
- `//` – Jednořádkový komentář.  

---

### **12. Běžné chyby a tipy**

- **Záměna `""` a `''`**:  
  ```csharp
  char znak = 'A'; // Správně
  string text = "A"; // Správně
  ```
- **Kombinace `@` a `$`**:  
  ```csharp
  string cesta = $@"C:\Uživatel\{jméno}\Dokumenty";
  ```
- **Escape uvnitř interpolovaných řetězců**:  
  ```csharp
  string cesta = $"C:\\Users\\{jméno}"; // Nutné escapovat!
  ```

---

### **13. Ukázka kódu**

```csharp
// Kombinace operátorů
string? input = null;
string output = input?.ToUpper() ?? "NIC"; // "NIC"

// Raw string literal
string html = """
    <div class="container">
        <p>Ahoj!</p>
    </div>
    """;

// Lambda výraz
var čísla = new List<int> { 1, 2, 3 };
var sudá = čísla.Where(x => x % 2 == 0).ToList();
```
