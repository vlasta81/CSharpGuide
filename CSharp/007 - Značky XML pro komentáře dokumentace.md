
### **1. Úvod do XML dokumentaèních komentáøù**

- **Syntaxe**: Zaèínají `///` a používají XML znaèky k popisu kódu.
- **Úèel**:
  - Generování API dokumentace (napø. pomocí nástrojù jako *Sandcastle* nebo *DocFX*).
  - Zobrazení nápovìdy v IDE (IntelliSense ve Visual Studiu).
  - Zlepšení èitelnosti a údržby kódu.
- **Výstup**: Kompilátor generuje soubor `.xml` s dokumentací (lze povolit v nastavení projektu).

---

### **2. Základní znaèky**

#### **`<summary>`**

- **Popis**: Krátký popis èlena (tøída, metoda, vlastnost).
- **Pøíklad**:
  ```csharp
  /// <summary>
  /// Tøída reprezentující uživatele aplikace.
  /// </summary>
  public class Uzivatel { }
  ```

#### **`<param>`**

- **Popis**: Dokumentace parametru metody/konstruktoru.
- **Atribut**: `name` (název parametru).
- **Pøíklad**:
  ```csharp
  /// <param name="jmeno">Celé jméno uživatele.</param>
  public void NastavJmeno(string jmeno) { }
  ```

#### **`<returns>`**

- **Popis**: Vysvìtluje návratovou hodnotu metody.
- **Pøíklad**:
  ```csharp
  /// <returns>Vìk uživatele v letech.</returns>
  public int ZiskejVek() => 25;
  ```

#### **`<remarks>`**

- **Popis**: Rozšíøené informace o èlenu (doplòuje `<summary>`).
- **Pøíklad**:
  ```csharp
  /// <remarks>
  /// Tato metoda mùže vyvolat výjimku pøi neplatném vstupu.
  /// </remarks>
  ```

---

### **3. Pokroèilé znaèky**

#### **`<exception>`**

- **Popis**: Dokumentuje výjimky, které metoda mùže vyvolat.
- **Atribut**: `cref` (odkaz na typ výjimky).
- **Pøíklad**:
  ```csharp
  /// <exception cref="ArgumentNullException">Pokud je jmeno prázdné.</exception>
  public void Validuj(string jmeno) {
      if (string.IsNullOrEmpty(jmeno)) throw new ArgumentNullException();
  }
  ```

#### **`<example>`**

- **Popis**: Ukázka použití kódu (èasto s `<code>`).
- **Pøíklad**:
  ```csharp
  /// <example>
  /// <code>
  /// var uzivatel = new Uzivatel();
  /// uzivatel.NastavJmeno("Karel");
  /// </code>
  /// </example>
  ```

#### **`<see>` a `<seealso>`**

- **Popis**: Odkazy na jiné èleny nebo typy.
- **Pøíklad**:
  ```csharp
  /// <see cref="Uzivatel"/> // Odkaz na tøídu
  /// <seealso cref="NastavJmeno"/>
  ```

#### **`<value>`**

- **Popis**: Popisuje význam vlastnosti (property).
- **Pøíklad**:
  ```csharp
  /// <value>Jméno uživatele ve formátu "Pøíjmení, Jméno".</value>
  public string CeleJmeno { get; set; }
  ```

#### **`<typeparam>`**

- **Popis**: Dokumentuje generický typ parametru.
- **Atribut**: `name` (název parametru).
- **Pøíklad**:
  ```csharp
  /// <typeparam name="T">Typ prvku v kolekci.</typeparam>
  public class Kolekce<T> { }
  ```

---

### **4. Formátování a další znaèky**

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

- **Popis**: Vloží odstavec (napø. uvnitø `<remarks>`).
  ```csharp
  /// <remarks>
  /// <para>První odstavec.</para>
  /// <para>Druhý odstavec.</para>
  /// </remarks>
  ```

#### **`<inheritdoc/>`**

- **Popis**: Dìdí dokumentaci z nadøazeného èlenu (ušetøí duplikaci).
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
   - **DocFX**: Vytváøí webovou dokumentaci.
   - **Sandcastle**: Generuje CHM nebo HTML.
   - **VS IntelliSense**: Zobrazuje popisky pøímo v editoru.

---

### **6. Doporuèené postupy**

- **Dokumentujte veøejné API**: Tøídy, metody, vlastnosti.
- **Popisujte výjimky**: Umožòuje vývojáøùm pøedcházet chybám.
- **Používejte `<example>`**: Usnadòuje pochopení použití.
- **Vyhnìte se duplikacím**: Využívejte `<inheritdoc/>`.

---

### **7. Ukázka komplexního použití**

```csharp
/// <summary>
/// Tøída pro správu matematických operací.
/// </summary>
public class Matematika {
    /// <summary>
    /// Seète dvì èísla.
    /// </summary>
    /// <param name="a">První èíslo.</param>
    /// <param name="b">Druhé èíslo.</param>
    /// <returns>Souèet èísel.</returns>
    /// <exception cref="OverflowException">Pokud výsledek pøekroèí rozsah.</exception>
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
