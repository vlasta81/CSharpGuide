
### **Typ `bool` v jazyce C#**

Typ `bool` (alias pro `System.Boolean`) reprezentuje **logickou hodnotu** `true` (pravda) nebo `false` (nepravda). Používá se pro **kontrolu toku programu** (podmínky, cykly) a rozhodovací logiku.

---

#### **1. Základní vlastnosti**

- **Možné hodnoty**: `true` nebo `false`.
- **Velikost**: 1 bajt (v praxi, i když stačí 1 bit – optimalizace pro hardware).
- **Výchozí hodnota**: `false` (pro proměnné na úrovni třídy; lokální proměnné musí být inicializovány).
- **Žádná implicitní konverze**: Nelze převádět na `int` ani jiné typy (např. `if (1)` je v C# chyba).

---

#### **2. Deklarace a použití**

```csharp
bool isActive = true;
bool isCompleted = false;
```

#### **3. Logické operátory**

| Operátor | Popis                  | Příklad                     |
|----------|------------------------|----------------------------|
| `&&`     | Logické **AND**        | `true && false → false`    |
| `||`     | Logické **OR**         | `true || false → true`     |
| `!`      | Logické **NOT**        | `!true → false`            |
| `^`      | Logické **XOR**        | `true ^ true → false`      |

#### **4. Porovnávací operátory**

Vracejí `bool` na základě porovnání:
```csharp
int a = 5, b = 10;
bool result = (a < b); // true
bool equal = (a == b); // false
```

---

#### **5. Nullable `bool` (`bool?`)**

- Může obsahovat `true`, `false` nebo `null`.
- Užitečné pro neznámé/neinicializované stavy (např. databázové hodnoty).
```csharp
bool? isVerified = null;
if (isVerified.HasValue)
{
    Console.WriteLine(isVerified.Value);
}
```

---

#### **6. Konverze**

- **Řetězec → `bool`**:
  ```csharp
  bool value1 = bool.Parse("true"); // "True" nebo "False" (case-insensitive)
  bool value2;
  bool success = bool.TryParse("Ano", out value2); // success = false (neplatný formát)
  ```
- **Čísla → `bool`**:
  - Nelze implicitně převést. Je třeba explicitní podmínky:
  ```csharp
  int number = 1;
  bool isValid = (number != 0); // true
  ```

---

#### **7. Příklady použití**

- **Podmíněné větvení**:
  ```csharp
  bool isLoggedIn = true;
  if (isLoggedIn)
  {
      Console.WriteLine("Vítejte!");
  }
  ```
- **Cyklus `while`**:
  ```csharp
  bool shouldContinue = true;
  while (shouldContinue)
  {
      // ... 
      shouldContinue = false; // Ukončí cyklus
  }
  ```
- **Vrácení logické hodnoty z metody**:
  ```csharp
  public bool IsEven(int number)
  {
      return (number % 2 == 0);
  }
  ```

---

#### **8. Časté chyby a tipy**

- **Záměna `=` a `==`**:
  ```csharp
  bool isReady = false;
  if (isReady = true) // Chyba: přiřazení místo porovnání → vždy true!
  ```
- **Převod neboolovských hodnot**:
  - Neplatný kód: `bool x = 0;` → chyba kompilace.
- **Použití `Nullable<bool>`**:
  - Vždy kontrolujte `HasValue` před přístupem k `Value` (vyhnutí se `InvalidOperationException`).
- **Optimalizace podmínek**:
  - Operátor `&&` a `||` používají **zkrácené vyhodnocení** (short-circuiting). Např.:
  ```csharp
  if (obj != null && obj.IsValid) // Pokud je obj null, druhá podmínka se nevyhodnotí.
  ```

---

#### **9. Integrace s dalšími funkcemi C#**

- **Vlastnosti (Properties)**:
  ```csharp
  public bool IsEnabled { get; set; } = true;
  ```
- **LINQ dotazy**:
  ```csharp
  var activeUsers = users.Where(u => u.IsActive);
  ```
- **Switch výrazy** (C# 8+):
  ```csharp
  string status = isActive switch { true => "Aktivní", false => "Neaktivní" };
  ```

---

### **Shrnutí**

- **Základní použití**: Řízení větvení, cyklů a logických operací.
- **Klíčové operátory**: `&&`, `||`, `!`, `^`.
- **Nullable `bool?`**: Pro stavy s možností `null`.
- **Pozor na chyby**: Záměna `=` a `==`, implicitní konverze.
- **Tip**: Pro složitou logiku používejte proměnné s popisnými názvy (např. `isUserValid`).

📌 **Příklad z praxe**:
```csharp
public bool ValidateUser(string username, string password)
{
    bool isUsernameValid = !string.IsNullOrEmpty(username);
    bool isPasswordValid = (password.Length >= 8);
    return isUsernameValid && isPasswordValid;
}
```
