
### **StringBuilder v jazyce C#**

**StringBuilder** (třída `System.Text.StringBuilder`) je optimalizovaný nástroj pro **efektivní manipulaci s řetězci** v situacích, kdy je potřeba provádět časté změny (přidávání, mazání, úpravy). Narozdíl od klasického `string` je **mutable** (lze měnit bez vytváření nových instancí), což šetří paměť a zvyšuje výkon.

---

#### **1. Kdy použít StringBuilder?**

- Při **opakovaných operacích s řetězci** (např. v cyklech).
- Pro sestavování dlouhých textů z mnoha částí (např. generování HTML, XML, SQL dotazů).
- Když klasické řetězcové operace (`+`, `Replace`, `Substring`) vedou k **nadměrné alokaci paměti**.

---

#### **2. Základní vlastnosti**

| Vlastnost/Metoda | Popis |
|------------------|-------|
| **`Length`** | Aktuální délka řetězce. |
| **`Capacity`** | Celková alokovaná paměť (lze nastavit pro optimalizaci). |
| **`Append()`** | Přidá text na konec. |
| **`Insert()`** | Vloží text na určitou pozici. |
| **`Remove()`** | Odstraní část řetězce. |
| **`Replace()`** | Nahradí výskyty podřetězce. |
| **`ToString()`** | Převede obsah na `string`. |
| **`Clear()`** | Smaže celý obsah (od C# 10+). |

---

#### **3. Deklarace a základní použití**

```csharp
using System.Text;

// Vytvoření s výchozí kapacitou (16 znaků)
StringBuilder sb = new StringBuilder();

// Přidání textu
sb.Append("Hello");
sb.AppendLine(" World!"); // Přidá řádek s \n
sb.AppendFormat("Dnes je {0:dd.MM}", DateTime.Now);

// Vložení textu na pozici
sb.Insert(5, " C#"); // "Hello C# World!..."

// Nahrazení části
sb.Replace("World", "Universe");

// Odstranění části
sb.Remove(0, 6); // "C# World!..."

// Převedení na string
string result = sb.ToString(); 
```

---

#### **4. Kapacita a optimalizace**

- **Kapacita** je výchozí velikost bufferu. Pokud je překročena, automaticky se zdvojnásobí, což může být nákladné.
- **Nastavení kapacity** pro snížení realokací:
  ```csharp
  // Vytvoření s počáteční kapacitou 100 znaků
  StringBuilder sb = new StringBuilder(100);
  
  // Nastavení kapacity ručně
  sb.Capacity = 200;
  ```

---

#### **5. Výhody oproti klasickým řetězcům**

- **Menší alokace paměti**: Klasický `string` vytváří novou instanci při každé změně.
- **Rychlejší operace**: Např. 1000× `Append()` je až **100× rychlejší** než 1000× `+=` s `string`.

**Příklad neefektivního kódu:**
```csharp
string text = "";
for (int i = 0; i < 1000; i++) 
    text += i; // Každé += vytvoří nový string!
```

**Efektivní řešení se `StringBuilder`:**
```csharp
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) 
    sb.Append(i);
string result = sb.ToString();
```

---

#### **6. Časté chyby a tipy**

- **Zapomenutí `ToString()`**: `StringBuilder` sám o sobě není `string` – výsledek vždy převeďte.
- **Nadbytečné použití**: Pro jednorázové operace (např. `s = "A" + "B"`) stačí `string`.
- **Použití `AppendJoin` pro spojování s oddělovačem**:
  ```csharp
  sb.AppendJoin(", ", new[] { "a", "b", "c" }); // "a, b, c"
  ```
- **Nepoužívat pro malé řetězce**: Náročnost inicializace `StringBuilder` se nevyplatí.

---

#### **7. Pokročilé metody**

- **`AppendFormat()`**: Formátované přidání hodnot.
  ```csharp
  sb.AppendFormat("{0:C}", 100); // "100 Kč"
  ```
- **`EnsureCapacity()`**: Zajistí minimální kapacitu.
  ```csharp
  sb.EnsureCapacity(500); // Zvětší kapacitu na 500, pokud je menší.
  ```

---

#### **8. Příklady z praxe**

- **Generování HTML tabulky**:
  ```csharp
  StringBuilder html = new StringBuilder();
  html.AppendLine("<table>");
  foreach (var row in data)
      html.AppendFormat("<tr><td>{0}</td></tr>", row);
  html.AppendLine("</table>");
  ```
- **Dynamický SQL dotaz**:
  ```csharp
  StringBuilder sql = new StringBuilder("SELECT * FROM Users WHERE ");
  sql.Append("Age > 18 ");
  sql.Append("AND Country = 'CZ'");
  ```

---

### **Shrnutí**

- **Použití**: Efektivní práce s řetězci při častých změnách.
- **Výhody**: Mutable design, menší režie paměti, vyšší výkon.
- **Metody**: `Append`, `Insert`, `Replace`, `Remove`, `ToString`.
- **Kapacita**: Nastavte ji pro minimalizaci realokací.
- **Tip**: Vždy preferujte `StringBuilder` nad `string` v cyklech nebo při sestavování složených textů.

📌 **Příklad výkonu**:  
Operace s 10 000 iteracemi:
- `string`: ~300 ms  
- `StringBuilder`: ~0,5 ms
