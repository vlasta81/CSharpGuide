
### **1. Úvod do XML dokumentačních komentářů**

- **Syntaxe**: Začínají `///` a používají XML značky k popisu kódu.
- **Účel**:
  - Generování API dokumentace (např. pomocí nástrojů jako *Sandcastle* nebo *DocFX*).
  - Zobrazení nápovědy v IDE (IntelliSense ve Visual Studiu).
  - Zlepšení čitelnosti a údržby kódu.
- **Výstup**: Kompilátor generuje soubor `.xml` s dokumentací (lze povolit v nastavení projektu).

---

### **2. Základní značky**

#### **`<summary>`**

- **Popis**: Krátký popis člena (třída, metoda, vlastnost).
- **Příklad**:
  ```csharp
  /// <summary>
  /// Třída reprezentující uživatele aplikace.
  /// </summary>
  public class Uzivatel { }
  ```

#### **`<param>`**

- **Popis**: Dokumentace parametru metody/konstruktoru.
- **Atribut**: `name` (název parametru).
- **Příklad**:
  ```csharp
  /// <param name="jmeno">Celé jméno uživatele.</param>
  public void NastavJmeno(string jmeno) { }
  ```

#### **`<returns>`**

- **Popis**: Vysvětluje návratovou hodnotu metody.
- **Příklad**:
  ```csharp
  /// <returns>Věk uživatele v letech.</returns>
  public int ZiskejVek() => 25;
  ```

#### **`<remarks>`**

- **Popis**: Rozšířené informace o členu (doplňuje `<summary>`).
- **Příklad**:
  ```csharp
  /// <remarks>
  /// Tato metoda může vyvolat výjimku při neplatném vstupu.
  /// </remarks>
  ```

---

### **3. Pokročilé značky**

#### **`<exception>`**

- **Popis**: Dokumentuje výjimky, které metoda může vyvolat.
- **Atribut**: `cref` (odkaz na typ výjimky).
- **Příklad**:
  ```csharp
  /// <exception cref="ArgumentNullException">Pokud je jmeno prázdné.</exception>
  public void Validuj(string jmeno) {
      if (string.IsNullOrEmpty(jmeno)) throw new ArgumentNullException();
  }
  ```

#### **`<example>`**

- **Popis**: Ukázka použití kódu (často s `<code>`).
- **Příklad**:
  ```csharp
  /// <example>
  /// <code>
  /// var uzivatel = new Uzivatel();
  /// uzivatel.NastavJmeno("Karel");
  /// </code>
  /// </example>
  ```

#### **`<see>` a `<seealso>`**

- **Popis**: Odkazy na jiné členy nebo typy.
- **Příklad**:
  ```csharp
  /// <see cref="Uzivatel"/> // Odkaz na třídu
  /// <seealso cref="NastavJmeno"/>
  ```

#### **`<value>`**

- **Popis**: Popisuje význam vlastnosti (property).
- **Příklad**:
  ```csharp
  /// <value>Jméno uživatele ve formátu "Příjmení, Jméno".</value>
  public string CeleJmeno { get; set; }
  ```

#### **`<typeparam>`**

- **Popis**: Dokumentuje generický typ parametru.
- **Atribut**: `name` (název parametru).
- **Příklad**:
  ```csharp
  /// <typeparam name="T">Typ prvku v kolekci.</typeparam>
  public class Kolekce<T> { }
  ```

---

### **4. Formátování a další značky**

#### **`<c>`**

- **Popis**: Zvýrazní kód v textu (inline).
  ```csharp
  /// <c>int x = 5;</c>
  ```

#### **`<code>`**

- **Popis**: Blok kódu (v kombinaci s `<example>`).
  ```csharp
  /// <code>
  /// if (cond) { ... }
  /// </code>
  ```

#### **`<para>`**

- **Popis**: Vloží odstavec (např. uvnitř `<remarks>`).
  ```csharp
  /// <remarks>
  /// <para>První odstavec.</para>
  /// <para>Druhý odstavec.</para>
  /// </remarks>
  ```

#### **`<inheritdoc/>`**

- **Popis**: Dědí dokumentaci z nadřazeného členu (ušetří duplikaci).
  ```csharp
  /// <inheritdoc />
  public override void Metoda() { }
  ```

---

### **5. Generování dokumentace**

1. **Povolení v projektu** (`.csproj`):
   ```xml
   <PropertyGroup>
     <GenerateDocumentationFile>true</GenerateDocumentationFile>
   </PropertyGroup>
   ```
2. **Nástroje pro generování**:
   - **DocFX**: Vytváří webovou dokumentaci.
   - **Sandcastle**: Generuje CHM nebo HTML.
   - **VS IntelliSense**: Zobrazuje popisky přímo v editoru.

---

### **6. Doporučené postupy**

- **Dokumentujte veřejné API**: Třídy, metody, vlastnosti.
- **Popisujte výjimky**: Umožňuje vývojářům předcházet chybám.
- **Používejte `<example>`**: Usnadňuje pochopení použití.
- **Vyhněte se duplikacím**: Využívejte `<inheritdoc/>`.

---

### **7. Ukázka komplexního použití**

```csharp
/// <summary>
/// Třída pro správu matematických operací.
/// </summary>
public class Matematika {
    /// <summary>
    /// Sečte dvě čísla.
    /// </summary>
    /// <param name="a">První číslo.</param>
    /// <param name="b">Druhé číslo.</param>
    /// <returns>Součet čísel.</returns>
    /// <exception cref="OverflowException">Pokud výsledek překročí rozsah.</exception>
    /// <example>
    /// <code>
    /// var result = Matematika.Secti(5, 3); // Vrátí 8
    /// </code>
    /// </example>
    public static int Secti(int a, int b) => checked(a + b);
}
```

---

### **8. Výhody XML dokumentace**

- **Profesionální vzhled API**: Automaticky generovaná dokumentace.
- **Lepší týmová spolupráce**: Jasný popis funkcionality.
- **Integrace s nástroji**: Podpora pro CI/CD a verze dokumentace.
