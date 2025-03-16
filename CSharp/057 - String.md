
### **Typ `string` v jazyce C#**

Typ `string` (alias pro `System.String`) reprezentuje **neměnnou (immutable) sekvenci Unicode znaků**. Používá se pro práci s textem, formátování, manipulaci a ukládání řetězcových dat.

---

#### **1. Základní vlastnosti**

- **Imutabilita**: Po vytvoření nelze změnit – každá operace (např. `Replace`, `ToUpper`) vytvoří **nový řetězec**.
- **Reference typ**: Ukládá se na haldě (heap), ale chová se podobně jako hodnotový typ díky přetěžování operátorů (např. `==` porovnává obsah, ne reference).
- **UTF-16 kódování**: Každý znak je uložen jako 2 bajty (včetně speciálních znaků a emoji).
- **Null a prázdný řetězec**: 
  - `string s = null;` – neodkazuje na žádná data.
  - `string s = "";` nebo `string s = String.Empty;` – prázdný řetězec.

---

#### **2. Deklarace a inicializace**

```csharp
string text1 = "Ahoj světe!";           // Klasický zápis
string text2 = @"C:\Program Files\";    // Verbatim řetězec (ignoruje escape sekvence)
string text3 = $"Dnes je {DateTime.Now:dd.MM}"; // Interpolovaný řetězec
string text4 = new string('*', 10);     // "**********"
```

---

#### **3. Důležité metody a operace**

- **Základní operace**:
  ```csharp
  string s = "Hello";
  int length = s.Length;               // 5
  char firstChar = s[0];               // 'H'
  string upper = s.ToUpper();          // "HELLO"
  string substring = s.Substring(1, 3); // "ell"
  ```
- **Spojování řetězců**:
  ```csharp
  string combined = String.Concat("A", "B", "C"); // "ABC"
  string joined = String.Join("-", "a", "b", "c"); // "a-b-c"
  ```
- **Porovnávání**:
  ```csharp
  bool isEqual = s.Equals("HELLO", StringComparison.OrdinalIgnoreCase); // true (case-insensitive)
  int comparison = String.Compare("A", "B"); // -1 (A < B)
  ```
- **Formátování**:
  ```csharp
  string formatted = String.Format("{0} + {1} = {2}", 2, 3, 5); // "2 + 3 = 5"
  ```
- **Kontrola obsahu**:
  ```csharp
  bool contains = s.Contains("ell");     // true
  bool startsWith = s.StartsWith("He");  // true
  bool endsWith = s.EndsWith("lo");      // true
  ```

---

#### **4. Interpolované řetězce (C# 6+)**

```csharp
string name = "Karel";
int age = 30;
string message = $"{name} má {age} let."; // "Karel má 30 let."
```

---

#### **5. Verbatim řetězce**

Ignorují escape sekvence a zachovávají formátování:
```csharp
string path = @"C:\Users\Public\Documents";
string multiLine = @"První řádek
Druhý řádek";
```

---

#### **6. Práce s řetězci – pokročilé**

- **Rozdělení řetězce**:
  ```csharp
  string[] parts = "a,b,c".Split(','); // ["a", "b", "c"]
  ```
- **Nahrazení části**:
  ```csharp
  string replaced = "Hello".Replace("l", "L"); // "HeLLo"
  ```
- **Oříznutí mezer**:
  ```csharp
  string trimmed = "   text   ".Trim(); // "text"
  ```
- **Kontrola prázdnoty**:
  ```csharp
  bool isEmpty = String.IsNullOrEmpty("");    // true
  bool isWhitespace = String.IsNullOrWhiteSpace("   "); // true
  ```

---

#### **7. `StringBuilder` – efektivní manipulace**

Pro časté změny řetězce (např. v cyklech) použijte **mutable** `StringBuilder`:
```csharp
using System.Text;
StringBuilder sb = new StringBuilder();
sb.Append("Hello");
sb.AppendLine(" World!");
sb.Replace("World", "C#");
string result = sb.ToString(); // "Hello C#!\n"
```

---

#### **8. Časté chyby a tipy**

- **Imutabilita**: Opakované použití `+=` v cyklech je neefektivní – místo toho použijte `StringBuilder`.
  ```csharp
  // Špatně:
  string s = "";
  for (int i = 0; i < 1000; i++) s += i;
  
  // Správně:
  StringBuilder sb = new StringBuilder();
  for (int i = 0; i < 1000; i++) sb.Append(i);
  ```
- **Case-sensitive porovnávání**: Vždy specifikujte `StringComparison` pro kontrolu kultury:
  ```csharp
  bool equal = s.Equals("hello", StringComparison.OrdinalIgnoreCase);
  ```
- **Formátování čísel**:
  ```csharp
  double value = 123.456;
  string formatted = value.ToString("C2"); // "123,46 Kč" (měna)
  ```
- **Interning**: CLR automaticky ukládá identické literály do sdílené paměti:
  ```csharp
  string a = "test";
  string b = "test";
  bool sameReference = Object.ReferenceEquals(a, b); // true
  ```

---

#### **9. Příklady použití**

- **Validace vstupu**:
  ```csharp
  Console.Write("Zadejte e-mail: ");
  string email = Console.ReadLine();
  bool isValid = email.Contains("@") && email.Length >= 5;
  ```
- **Extrakce dat**:
  ```csharp
  string data = "Jméno: Jan; Příjmení: Novák";
  string[] pairs = data.Split(';');
  foreach (string pair in pairs)
  {
      string[] keyValue = pair.Split(':');
      Console.WriteLine($"{keyValue[0].Trim()} = {keyValue[1].Trim()}");
  }
  ```
- **Maskování hesla**:
  ```csharp
  string password = "secret";
  string masked = new string('*', password.Length); // "******"
  ```

---

### **Shrnutí**

- **Imutabilita**: Každá operace vytváří nový řetězec.
- **Formátování**: Interpolace (`$"..."`), `String.Format`, vlastní formátovací specifikátory.
- **Efektivita**: Pro časté změny použijte `StringBuilder`.
- **Kontrola prázdnoty**: `String.IsNullOrEmpty` a `String.IsNullOrWhiteSpace`.
- **Porovnávání**: Vždy zvažte kulturu a velikost písmen.

📌 **Tip**: Pro práci s velkými texty nebo častými operacemi vždy preferujte `StringBuilder` před klasickými řetězci.
