
### Regulární výrazy v jazyce C#

Regulární výrazy (regex) slouží k **vyhledávání, analýze a manipulaci s textem** na základě specifických vzorů. V C# jsou implementovány prostřednictvím knihovny `System.Text.RegularExpressions`.

---

#### 1. **Základní třídy a struktury**

- **`Regex`**: Třída pro definici a práci s regulárním výrazem.
  - Může být použita jako instance (pro opakované použití) nebo přes statické metody (pro jednorázové operace).
  - Příklad: `Regex regex = new Regex(@"\d+");`
- **`Match`**: Reprezentuje výsledek jednoho porovnání vzoru s textem.
  - Vlastnosti: `Value`, `Index`, `Length`, `Groups`.
- **`MatchCollection`**: Kolekce všech nalezených shod (`Match` objektů).

---

#### 2. **Základní metody**

- **`IsMatch`**: Kontrola, zda text odpovídá vzoru.
  ```csharp
  bool isValid = Regex.IsMatch("123", @"^\d+$"); // true
  ```
- **`Match`**: Vrátí první nalezenou shodu.
  ```csharp
  Match match = Regex.Match("A1B2C3", @"\d");
  Console.WriteLine(match.Value); // "1"
  ```
- **`Matches`**: Vrátí všechny shody jako `MatchCollection`.
  ```csharp
  foreach (Match m in Regex.Matches("A1B2C3", @"\d"))
    Console.WriteLine(m.Value); // 1, 2, 3
  ```
- **`Replace`**: Nahradí shody novým textem.
  ```csharp
  string result = Regex.Replace("Hello 123", @"\d", "X"); // "Hello XXX"
  ```
- **`Split`**: Rozdělí text podle shod.
  ```csharp
  string[] parts = Regex.Split("a1b2c3", @"\d"); // ["a", "b", "c"]
  ```

---

#### 3. **Syntaxe regulárních výrazů**

- **Speciální znaky** (escapování pomocí `\` v C# pomocí `@"..."` nebo `\\`):
  - `\d` – číslice, `\w` – alfanumerický znak, `\s` – bílý znak.
  - `.` – libovolný znak, `^` – začátek řetězce, `$` – konec řetězce.
- **Kvantifikátory**:
  - `*` (0 a více), `+` (1 a více), `?` (0 nebo 1), `{n}` (přesně `n` opakování).
- **Skupiny a alternativy**:
  - `(skupina)` – zachycení skupiny, `(?:skupina)` – nezachycující skupina.
  - `a|b` – alternativa (a nebo b).
- **Pojmenované skupiny**:
  ```csharp
  Match m = Regex.Match("ID: 123", @"ID: (?<id>\d+)");
  Console.WriteLine(m.Groups["id"].Value); // "123"
  ```

---

#### 4. **Možnosti (RegexOptions)**

- Lze kombinovat pomocí operátoru `|`:
  - `IgnoreCase` – ignoruje velikost písmen.
  - `Multiline` – `^` a `$` platí pro začátek/konec každého řádku.
  - `Compiled` – zkompiluje regex pro vyšší výkon (náročnější na paměť).
  - `ExplicitCapture` – ignoruje nezpojmenované skupiny.
  ```csharp
  Regex regex = new Regex(@"aBc", RegexOptions.IgnoreCase);
  ```

---

#### 5. **Příklady použití**

- **Validace e-mailu** (zjednodušená):
  ```csharp
  bool isValidEmail = Regex.IsMatch("test@example.com", 
      @"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$");
  ```
- **Extrakce čísel z textu**:
  ```csharp
  foreach (Match m in Regex.Matches("Cena: 200 Kč", @"\d+"))
      Console.WriteLine(m.Value); // "200"
  ```
- **Formátování dat**:
  ```csharp
  string formatted = Regex.Replace("20231231", 
      @"(\d{4})(\d{2})(\d{2})", "$1-$2-$3"); // "2023-12-31"
  ```

---

#### 6. **Tipy a nejlepší praktiky**

- **Kompilace regulárních výrazů**: Používejte `RegexOptions.Compiled` pro časté použití.
- **Timeout**: Pro dlouhé operace nastavte `Regex.MatchTimeout`, aby nedocházelo k zablokování.
- **Čitelnost**: Komentujte složité vzory pomocí `(?x)`:
  ```csharp
  Regex regex = new Regex(@"(?x)^\d{3}  # Počáteční tři číslice
                              -\d{2}    # Pomlčka a dvě číslice
                              -\d{4}$"); # Konec
  ```

---

#### 7. **Časté chyby**

- **Greedy vs. Lazy kvantifikátory**: 
  - `.*` (greedy) vs. `.*?` (lazy).
- **Nesprávné escapování**: V C# používejte **verbatim řetězce** (`@"..."`), abyste snížili počet `\`.
  - Špatně: `"\\d+"`, správně: `@"\d+"`.

---

### Závěr

Regulární výrazy v C# jsou mocným nástrojem pro práci s textem. Klíčové je pochopení syntaxe regex, správné použití tříd `Regex`, `Match` a optimalizace pomocí `RegexOptions`. Pro složité vzory vždy testujte nástroji jako [Regex101](https://regex101.com/).
