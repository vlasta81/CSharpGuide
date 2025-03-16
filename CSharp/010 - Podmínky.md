
### **1. Úvod do podmínek**

- **Účel**: Řízení toku programu na základě splnění/nesplnění podmínek.
- **Základní logické operátory**:
  - `&&` (AND), `||` (OR), `!` (NOT).
  - Porovnávací operátory: `==`, `!=`, `>`, `<`, `>=`, `<=`.

---

### **2. Typy podmínek**

#### **a) `if`-`else`**

- **Syntaxe**:
  ```csharp
  if (podmínka1) {
      // Kód při splnění podmínky1
  } 
  else if (podmínka2) {
      // Kód při splnění podmínky2
  } 
  else {
      // Kód, pokud žádná podmínka není splněna
  }
  ```
- **Příklad**:
  ```csharp
  int věk = 20;
  if (věk >= 18) {
      Console.WriteLine("Dospělý");
  } else {
      Console.WriteLine("Nezletilý");
  }
  ```

#### **b) Ternární operátor (`?:`)** 

- **Syntaxe**: `podmínka ? hodnota_pokud_true : hodnota_pokud_false`.
- **Příklad**:
  ```csharp
  string stav = (teplota > 30) ? "Horko" : "Normál";
  ```

#### **c) `switch`**  

- **Vhodné pro více podmínek** (např. výběr z možností).
- **Klasický `switch`**:
  ```csharp
  switch (proměnná) {
      case 1:
          Console.WriteLine("Jedna");
          break;
      case 2:
          Console.WriteLine("Dvě");
          break;
      default:
          Console.WriteLine("Neznámé");
          break;
  }
  ```
- **Switch výraz (C# 8+)**:
  ```csharp
  string den = čísloDne switch {
      1 => "Pondělí",
      2 => "Úterý",
      _ => "Neznámý den" // Výchozí případ
  };
  ```

#### **d) Pattern Matching (C# 7+)**  

- **Kontrola typu a hodnoty** v podmínkách:
  ```csharp
  if (obj is string text && text.Length > 5) {
      Console.WriteLine($"Dlouhý text: {text}");
  }
  ```

---

### **3. Speciální operátory pro podmínky**

- **Null-coalescing (`??`)**: Vrátí výchozí hodnotu pro `null`.
  ```csharp
  string jméno = input ?? "Neznámý";
  ```
- **Null-conditional (`?.`, `?[]`)**: Bezpečný přístup k členům objektu.
  ```csharp
  int? délka = text?.Length; // null, pokud je text null
  ```

---

### **4. Běžné chyby a varování**

- **Záměna `=` a `==`**:
  ```csharp
  if (x = 5) { ... } // Chyba – přiřazení místo porovnání
  ```
- **Zapomenutý `break` v `switch`**:
  ```csharp
  case 1: 
      Console.WriteLine("1");
      // Chybí break → chyba kompilace!
  ```
- **Nepokrytý případ v `switch`**: Vždy přidejte `default` (není povinné, ale doporučené).

---

### **5. Tipy pro efektivní použití**

1. **Vyhněte se vnořeným `if`**: Používejte `else if` nebo refaktorujte kód.
2. **Používejte `switch` pro mnoho možností**: Zlepšuje čitelnost.
3. **Využívejte logické operátory**:
   ```csharp
   if (věk >= 18 && věk <= 65) { ... }
   ```
4. **Kombinujte s výjimkami**:
   ```csharp
   if (!File.Exists(cesta)) {
       throw new FileNotFoundException();
   }
   ```

---

### **6. Ukázky kódu**

#### **Více podmínek s `if`-`else`**

```csharp
int skóre = 85;
if (skóre >= 90) {
    Console.WriteLine("Výborně");
} else if (skóre >= 70) {
    Console.WriteLine("Dobře");
} else {
    Console.WriteLine("Nevyhovující");
}
```

#### **Pattern Matching v `switch`**

```csharp
object obj = "Ahoj";
switch (obj) {
    case int i:
        Console.WriteLine($"Je číslo: {i}");
        break;
    case string s when s.Length > 3:
        Console.WriteLine($"Dlouhý text: {s}");
        break;
    default:
        Console.WriteLine("Neznámý typ");
        break;
}
```

#### **Ternární operátor pro jednoduché podmínky**

```csharp
int a = 10, b = 20;
int max = (a > b) ? a : b; // max = 20
```

---

### **7. Porovnání `if` vs `switch`**

| **Kritérium**       | **`if`-`else`**                | **`switch`**                |
|----------------------|--------------------------------|------------------------------|
| **Počet podmínek**   | Libovolný počet               | Hodnoty z pevné množiny      |
| **Čitelnost**        | Vhodné pro 2–3 podmínky       | Lepší pro mnoho možností     |
| **Výkon**            | Stejný (kompilátor optimalizuje) | Stejný                     |

---

### **8. Reálné scénáře**

- **Validace vstupů**:
  ```csharp
  if (string.IsNullOrEmpty(email)) {
      Console.WriteLine("Email je povinný!");
  }
  ```
- **Stavové stroje**:
  ```csharp
  switch (stav) {
      case Stav.Novy: /* ... */ break;
      case Stav.Schvaleny: /* ... */ break;
  }
  ```
