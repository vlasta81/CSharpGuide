
### **Hodnota `null` v jazyce C#**

Hodnota `null` reprezentuje **neexistující nebo nedefinovanou referenci** na objekt. V C# se používá k označení, že proměnná neodkazuje na žádná data v paměti. Porozumění `null` je klíčové pro prevenci chyb (např. `NullReferenceException`) a efektivní práci s referencemi a nullable typy.

---

### **1. Základní koncepty**

- **`null` pro reference**: Všechny **referenční typy** (např. `string`, třídy, pole) mohou mít hodnotu `null`.
  ```csharp
  string text = null; // Platné
  List<int> numbers = null; 
  ```
- **Hodnotové typy** (např. `int`, `bool`, `struct`) **nemohou být `null`**, pokud nejsou deklarovány jako **nullable**.
  ```csharp
  int age = null; // Chyba kompilace
  int? nullableAge = null; // Správně (nullable typ)
  ```

---

### **2. Nullable hodnotové typy**

- **Syntaxe `?`**: Přidání `?` k hodnotovému typu umožňuje přiřadit `null`.
  ```csharp
  int? number = null;
  bool? isActive = null;
  ```
- **Podpora přes `Nullable<T>`**: 
  ```csharp
  Nullable<int> number = null; // Ekvivalentní k int?
  ```
- **Metody a vlastnosti**:
  - `HasValue`: Vrátí `true`, pokud proměnná obsahuje hodnotu (ne `null`).
  - `Value`: Vrátí hodnotu (pokud `HasValue` je `false`, vyvolá `InvalidOperationException`).
  - `GetValueOrDefault()`: Vrátí hodnotu nebo výchozí (např. `0` pro `int?`).
  ```csharp
  int? x = null;
  int y = x.GetValueOrDefault(); // y = 0
  ```

---

### **3. Operátory pro práci s `null`**

- **Null-coalescing operátor (`??`)**: 
  - Vrátí levou hodnotu, pokud není `null`, jinak pravou.
  ```csharp
  string name = inputName ?? "Neznámý";
  int? age = null;
  int actualAge = age ?? 18; // 18
  ```
- **Null-coalescing přiřazení (`??=`) (C# 8+)**:
  ```csharp
  List<int> numbers = null;
  numbers ??= new List<int>(); // Inicializuje, pokud je null
  ```
- **Null-conditional operátor (`?.`) (C# 6+)**:
  - Bezpečně přistupuje k členům objektu. Pokud je objekt `null`, vrátí `null`.
  ```csharp
  string? text = null;
  int? length = text?.Length; // length = null (nevyvolá výjimku)
  ```

---

### **4. Nullable reference typy (C# 8+)**

- **Cílem je snížit `NullReferenceException`**: 
  - Kompilátor sleduje, zda mohou reference obsahovat `null`.
  - Aktivuje se direktivou `#nullable enable`.
  ```csharp
  #nullable enable
  string nonNullableText = "Ahoj"; // Nelze přiřadit null
  string? nullableText = null;     // Explicitní nullable
  ```
- **Varování kompilátoru**:
  ```csharp
  string text = null; // Varování: Nelze přiřadit null!
  ```

---

### **5. Práce s `null` v praxi**

- **Kontrola `null`**:
  ```csharp
  if (text != null)
      Console.WriteLine(text.Length);
  
  // Pattern matching (C# 7+):
  if (text is null)
      Console.WriteLine("Prázdný text");
  ```
- **Metody pro kontrolu**:
  - `string.IsNullOrEmpty()`: Kontroluje `null` nebo prázdný řetězec.
  - `string.IsNullOrWhiteSpace()`: Kontroluje `null`, prázdný řetězec nebo samé mezery.
  ```csharp
  if (string.IsNullOrEmpty(text))
      Console.WriteLine("Neplatný text");
  ```

---

### **6. Časté chyby a řešení**

- **`NullReferenceException`**:
  - Příčina: Pokus o přístup k členu objektu, který je `null`.
  ```csharp
  string[]? names = null;
  Console.WriteLine(names.Length); // Vyvolá výjimku
  ```
  - Řešení: Použijte `?.` nebo explicitní kontrolu:
  ```csharp
  Console.WriteLine(names?.Length ?? 0);
  ```
- **Nesprávné použití nullable typů**:
  ```csharp
  int? x = 5;
  int y = x; // Chyba: Nelze implicitně převést int? na int
  int z = x.Value; // Riziko výjimky, pokud x je null
  ```

---

### **7. Null v databázích a API**

- **Entity Framework Core**: Mapuje `null` na `NULL` v databázi.
  ```csharp
  public class User
  {
      public int Id { get; set; }
      public string? Email { get; set; } // Může být null
  }
  ```
- **JSON serializace**: 
  - Vlastnost s `null` může být ignorována nebo serializována.
  ```csharp
  [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
  public string? Phone { get; set; }
  ```

---

### **8. Best Practices**

1. **Používejte nullable reference typy (C# 8+)** pro prevenci chyb.
2. **Inicializujte proměnné**, pokud možno.
3. **Používejte `?.` a `??`** pro bezpečný přístup k hodnotám.
4. **Ošetřujte `null` u vstupů z externích zdrojů** (API, databáze, uživatelé).
5. **Nepoužívejte `null` pro "výchozí" stavy** – preferujte výčtové typy nebo vzory (např. Null Object pattern).

---

### **Shrnutí**

- **`null`** = absence hodnoty.
- **Nullable typy**: `T?` pro hodnotové typy, `#nullable enable` pro reference.
- **Operátory**: `??`, `?.`, `??=` pro bezpečné zpracování.
- **Chyby**: `NullReferenceException` lze předejít kontrolami a správným návrhem kódu.

📌 **Příklad – Bezpečný přístup k vlastnostem**:
```csharp
// Bezpečně získá délku textu nebo vrátí 0
int length = text?.Length ?? 0; 

// Bezpečné volání metody
user?.UpdateProfile(); // Pokud user je null, metoda se nevolá
```
