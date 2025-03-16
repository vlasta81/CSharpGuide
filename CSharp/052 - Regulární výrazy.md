
### Regul�rn� v�razy v jazyce C#

Regul�rn� v�razy (regex) slou�� k **vyhled�v�n�, anal�ze a manipulaci s textem** na z�klad� specifick�ch vzor�. V C# jsou implementov�ny prost�ednictv�m knihovny `System.Text.RegularExpressions`.

---

#### 1. **Z�kladn� t��dy a struktury**

- **`Regex`**: T��da pro definici a pr�ci s regul�rn�m v�razem.
  - M��e b�t pou�ita jako instance (pro opakovan� pou�it�) nebo p�es statick� metody (pro jednor�zov� operace).
  - P��klad: `Regex regex = new Regex(@"\d+");`
- **`Match`**: Reprezentuje v�sledek jednoho porovn�n� vzoru s textem.
  - Vlastnosti: `Value`, `Index`, `Length`, `Groups`.
- **`MatchCollection`**: Kolekce v�ech nalezen�ch shod (`Match` objekt�).

---

#### 2. **Z�kladn� metody**

- **`IsMatch`**: Kontrola, zda text odpov�d� vzoru.
  ```csharp
  bool isValid = Regex.IsMatch("123", @"^\d+$"); // true
  ```
- **`Match`**: Vr�t� prvn� nalezenou shodu.
  ```csharp
  Match match = Regex.Match("A1B2C3", @"\d");
  Console.WriteLine(match.Value); // "1"
  ```
- **`Matches`**: Vr�t� v�echny shody jako `MatchCollection`.
  ```csharp
  foreach (Match m in Regex.Matches("A1B2C3", @"\d"))
    Console.WriteLine(m.Value); // 1, 2, 3
  ```
- **`Replace`**: Nahrad� shody nov�m textem.
  ```csharp
  string result = Regex.Replace("Hello 123", @"\d", "X"); // "Hello XXX"
  ```
- **`Split`**: Rozd�l� text podle shod.
  ```csharp
  string[] parts = Regex.Split("a1b2c3", @"\d"); // ["a", "b", "c"]
  ```

---

#### 3. **Syntaxe regul�rn�ch v�raz�**

- **Speci�ln� znaky** (escapov�n� pomoc� `\` v C# pomoc� `@"..."` nebo `\\`):
  - `\d` � ��slice, `\w` � alfanumerick� znak, `\s` � b�l� znak.
  - `.` � libovoln� znak, `^` � za��tek �et�zce, `$` � konec �et�zce.
- **Kvantifik�tory**:
  - `*` (0 a v�ce), `+` (1 a v�ce), `?` (0 nebo 1), `{n}` (p�esn� `n` opakov�n�).
- **Skupiny a alternativy**:
  - `(skupina)` � zachycen� skupiny, `(?:skupina)` � nezachycuj�c� skupina.
  - `a|b` � alternativa (a nebo b).
- **Pojmenovan� skupiny**:
  ```csharp
  Match m = Regex.Match("ID: 123", @"ID: (?<id>\d+)");
  Console.WriteLine(m.Groups["id"].Value); // "123"
  ```

---

#### 4. **Mo�nosti (RegexOptions)**

- Lze kombinovat pomoc� oper�toru `|`:
  - `IgnoreCase` � ignoruje velikost p�smen.
  - `Multiline` � `^` a `$` plat� pro za��tek/konec ka�d�ho ��dku.
  - `Compiled` � zkompiluje regex pro vy��� v�kon (n�ro�n�j�� na pam�).
  - `ExplicitCapture` � ignoruje nezpojmenovan� skupiny.
  ```csharp
  Regex regex = new Regex(@"aBc", RegexOptions.IgnoreCase);
  ```

---

#### 5. **P��klady pou�it�**

- **Validace e-mailu** (zjednodu�en�):
  ```csharp
  bool isValidEmail = Regex.IsMatch("test@example.com", 
      @"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$");
  ```
- **Extrakce ��sel z textu**:
  ```csharp
  foreach (Match m in Regex.Matches("Cena: 200 K�", @"\d+"))
      Console.WriteLine(m.Value); // "200"
  ```
- **Form�tov�n� dat**:
  ```csharp
  string formatted = Regex.Replace("20231231", 
      @"(\d{4})(\d{2})(\d{2})", "$1-$2-$3"); // "2023-12-31"
  ```

---

#### 6. **Tipy a nejlep�� praktiky**

- **Kompilace regul�rn�ch v�raz�**: Pou��vejte `RegexOptions.Compiled` pro �ast� pou�it�.
- **Timeout**: Pro dlouh� operace nastavte `Regex.MatchTimeout`, aby nedoch�zelo k zablokov�n�.
- **�itelnost**: Komentujte slo�it� vzory pomoc� `(?x)`:
  ```csharp
  Regex regex = new Regex(@"(?x)^\d{3}  # Po��te�n� t�i ��slice
                              -\d{2}    # Poml�ka a dv� ��slice
                              -\d{4}$"); # Konec
  ```

---

#### 7. **�ast� chyby**

- **Greedy vs. Lazy kvantifik�tory**: 
  - `.*` (greedy) vs. `.*?` (lazy).
- **Nespr�vn� escapov�n�**: V C# pou��vejte **verbatim �et�zce** (`@"..."`), abyste sn�ili po�et `\`.
  - �patn�: `"\\d+"`, spr�vn�: `@"\d+"`.

---

### Z�v�r

Regul�rn� v�razy v C# jsou mocn�m n�strojem pro pr�ci s textem. Kl��ov� je pochopen� syntaxe regex, spr�vn� pou�it� t��d `Regex`, `Match` a optimalizace pomoc� `RegexOptions`. Pro slo�it� vzory v�dy testujte n�stroji jako [Regex101](https://regex101.com/).
