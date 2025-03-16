
### Regulární vırazy v jazyce C#

Regulární vırazy (regex) slouí k **vyhledávání, analıze a manipulaci s textem** na základì specifickıch vzorù. V C# jsou implementovány prostøednictvím knihovny `System.Text.RegularExpressions`.

---

#### 1. **Základní tøídy a struktury**

- **`Regex`**: Tøída pro definici a práci s regulárním vırazem.
  - Mùe bıt pouita jako instance (pro opakované pouití) nebo pøes statické metody (pro jednorázové operace).
  - Pøíklad: `Regex regex = new Regex(@"\d+");`
- **`Match`**: Reprezentuje vısledek jednoho porovnání vzoru s textem.
  - Vlastnosti: `Value`, `Index`, `Length`, `Groups`.
- **`MatchCollection`**: Kolekce všech nalezenıch shod (`Match` objektù).

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
- **`Replace`**: Nahradí shody novım textem.
  ```csharp
  string result = Regex.Replace("Hello 123", @"\d", "X"); // "Hello XXX"
  ```
- **`Split`**: Rozdìlí text podle shod.
  ```csharp
  string[] parts = Regex.Split("a1b2c3", @"\d"); // ["a", "b", "c"]
  ```

---

#### 3. **Syntaxe regulárních vırazù**

- **Speciální znaky** (escapování pomocí `\` v C# pomocí `@"..."` nebo `\\`):
  - `\d` – èíslice, `\w` – alfanumerickı znak, `\s` – bílı znak.
  - `.` – libovolnı znak, `^` – zaèátek øetìzce, `$` – konec øetìzce.
- **Kvantifikátory**:
  - `*` (0 a více), `+` (1 a více), `?` (0 nebo 1), `{n}` (pøesnì `n` opakování).
- **Skupiny a alternativy**:
  - `(skupina)` – zachycení skupiny, `(?:skupina)` – nezachycující skupina.
  - `a|b` – alternativa (a nebo b).
- **Pojmenované skupiny**:
  ```csharp
  Match m = Regex.Match("ID: 123", @"ID: (?<id>\d+)");
  Console.WriteLine(m.Groups["id"].Value); // "123"
  ```

---

#### 4. **Monosti (RegexOptions)**

- Lze kombinovat pomocí operátoru `|`:
  - `IgnoreCase` – ignoruje velikost písmen.
  - `Multiline` – `^` a `$` platí pro zaèátek/konec kadého øádku.
  - `Compiled` – zkompiluje regex pro vyšší vıkon (nároènìjší na pamì).
  - `ExplicitCapture` – ignoruje nezpojmenované skupiny.
  ```csharp
  Regex regex = new Regex(@"aBc", RegexOptions.IgnoreCase);
  ```

---

#### 5. **Pøíklady pouití**

- **Validace e-mailu** (zjednodušená):
  ```csharp
  bool isValidEmail = Regex.IsMatch("test@example.com", 
      @"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$");
  ```
- **Extrakce èísel z textu**:
  ```csharp
  foreach (Match m in Regex.Matches("Cena: 200 Kè", @"\d+"))
      Console.WriteLine(m.Value); // "200"
  ```
- **Formátování dat**:
  ```csharp
  string formatted = Regex.Replace("20231231", 
      @"(\d{4})(\d{2})(\d{2})", "$1-$2-$3"); // "2023-12-31"
  ```

---

#### 6. **Tipy a nejlepší praktiky**

- **Kompilace regulárních vırazù**: Pouívejte `RegexOptions.Compiled` pro èasté pouití.
- **Timeout**: Pro dlouhé operace nastavte `Regex.MatchTimeout`, aby nedocházelo k zablokování.
- **Èitelnost**: Komentujte sloité vzory pomocí `(?x)`:
  ```csharp
  Regex regex = new Regex(@"(?x)^\d{3}  # Poèáteèní tøi èíslice
                              -\d{2}    # Pomlèka a dvì èíslice
                              -\d{4}$"); # Konec
  ```

---

#### 7. **Èasté chyby**

- **Greedy vs. Lazy kvantifikátory**: 
  - `.*` (greedy) vs. `.*?` (lazy).
- **Nesprávné escapování**: V C# pouívejte **verbatim øetìzce** (`@"..."`), abyste sníili poèet `\`.
  - Špatnì: `"\\d+"`, správnì: `@"\d+"`.

---

### Závìr

Regulární vırazy v C# jsou mocnım nástrojem pro práci s textem. Klíèové je pochopení syntaxe regex, správné pouití tøíd `Regex`, `Match` a optimalizace pomocí `RegexOptions`. Pro sloité vzory vdy testujte nástroji jako [Regex101](https://regex101.com/).
