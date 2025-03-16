
### **Typ `char` v jazyce C#**

Typ `char` (alias pro `System.Char`) reprezentuje **jediný Unicode znak**. Využívá se pro práci s jednotlivými znaky, například při analýze textu, validaci vstupů nebo manipulaci s řetězci.

---

#### **1. Základní vlastnosti**

- **Velikost**: 2 bajty (UTF-16 kódování, podpora Unicode).
- **Rozsah**: Umožňuje uložit libovolný znak z Unicode (např. `'A'`, `'€'`, `'\u0061'` pro `'a'`).
- **Výchozí hodnota**: `'\0'` (prázdný znak, Unicode `\u0000`).
- **Implicitní konverze**: Lze převést na `int` (Unicode číselná hodnota), ale ne na `string` ani jiné typy bez explicitní konverze.

---

#### **2. Deklarace a inicializace**

```csharp
char letter = 'A';          // Použití apostrofů
char unicodeChar = '\u0041'; // 'A' v Unicode
char newLine = '\n';        // Escape sekvence
char heart = '❤';           // Emoji (podporováno od .NET 5+)
```

---

#### **3. Metody třídy `System.Char`**

Statické metody pro práci s znaky:
- **Kontrola typu znaku**:
  ```csharp
  bool isDigit = Char.IsDigit('5');      // true
  bool isLetter = Char.IsLetter('č');    // true
  bool isUpper = Char.IsUpper('X');      // true
  bool isWhiteSpace = Char.IsWhiteSpace(' '); // true
  ```
- **Transformace**:
  ```csharp
  char lower = Char.ToLower('A');     // 'a'
  char upper = Char.ToUpper('m');     // 'M'
  ```

---

#### **4. Escape sekvence**

Speciální znaky začínající zpětným lomítkem `\`:
| Sekvence | Význam          |
|----------|-----------------|
| `\'`     | Apostrof        |
| `\"`     | Uvozovka        |
| `\\`     | Zpětné lomítko |
| `\n`     | Nový řádek      |
| `\t`     | Tabulátor       |
| `\uXXXX` | Unicode znak (hexadecimálně) |

---

#### **5. Konverze mezi `char` a `int`**

- **Znak → Unicode hodnota**:
  ```csharp
  int code = (int)'A'; // 65
  ```
- **Unicode hodnota → znak**:
  ```csharp
  char c = (char)65; // 'A'
  ```

---

#### **6. Nullable `char` (`char?`)**

- Může obsahovat `char` nebo `null` (užitečné pro neznámé hodnoty).
  ```csharp
  char? optionalChar = null;
  if (optionalChar.HasValue)
      Console.WriteLine(optionalChar.Value);
  ```

---

#### **7. Příklady použití**

- **Validace vstupu**:
  ```csharp
  char input = Console.ReadKey().KeyChar;
  if (Char.IsDigit(input))
      Console.WriteLine("Zadali jste číslici.");
  ```
- **Práce s řetězci**:
  ```csharp
  string text = "Hello";
  char firstChar = text[0]; // 'H'
  foreach (char c in text)
      Console.WriteLine(c);
  ```
- **Generování znaků**:
  ```csharp
  for (char ch = 'A'; ch <= 'Z'; ch++)
      Console.Write(ch); // A B C ... Z
  ```

---

#### **8. Časté chyby a tipy**

- **Použití dvojitých uvozovek**: `char ch = "A";` → chyba kompilace (musí být `'A'`).
- **Přetečení při konverzi**:
  ```csharp
  int bigNumber = 1000;
  char ch = (char)bigNumber; // Platné, ale nemusí odpovídat tisknutelnému znaku.
  ```
- **Case-sensitive porovnávání**:
  ```csharp
  bool isEqual = ('a' == 'A'); // false
  bool ignoreCase = (Char.ToLower('A') == Char.ToLower('a')); // true
  ```
- **Nepodporuje implicitní převod na `string`**:
  ```csharp
  string s = 'X'.ToString(); // Správně
  string s2 = 'X';           // Chyba!
  ```

---

#### **9. Integrace s dalšími funkcemi C#**

- **Switch výrazy**:
  ```csharp
  char grade = 'B';
  string result = grade switch {
      'A' => "Výborně",
      'B' => "Dobře",
      _ => "Neznámá známka"
  };
  ```
- **LINQ**:
  ```csharp
  string text = "Abc123";
  var letters = text.Where(Char.IsLetter); // ['A', 'b', 'c']
  ```

---

### **Shrnutí**

- **Základní použití**: Práce s jednotlivými znaky, validace, transformace.
- **Unicode podpora**: Umožňuje pracovat s mezinárodními znaky a emoji.
- **Důležité metody**: `IsDigit`, `IsLetter`, `ToUpper`, `ToLower`.
- **Konverze**: Explicitní převod mezi `char` a `int` pro Unicode hodnoty.
- **Tip**: Pro složitější operace s textem používejte `string`, pro jednotlivé znaky `char`.

📌 **Příklad z praxe – Počet samohlásek v textu**:
```csharp
string text = "Hello, world!";
int vowelCount = text.Count(c => "aeiouAEIOU".Contains(c));
Console.WriteLine(vowelCount); // 3 (e, o, o)
```
