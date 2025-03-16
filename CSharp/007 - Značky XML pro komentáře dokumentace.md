
### **1. �vod do XML dokumenta�n�ch koment���**

- **Syntaxe**: Za��naj� `///` a pou��vaj� XML zna�ky k popisu k�du.
- **��el**:
  - Generov�n� API dokumentace (nap�. pomoc� n�stroj� jako *Sandcastle* nebo *DocFX*).
  - Zobrazen� n�pov�dy v IDE (IntelliSense ve Visual Studiu).
  - Zlep�en� �itelnosti a �dr�by k�du.
- **V�stup**: Kompil�tor generuje soubor `.xml` s dokumentac� (lze povolit v nastaven� projektu).

---

### **2. Z�kladn� zna�ky**

#### **`<summary>`**

- **Popis**: Kr�tk� popis �lena (t��da, metoda, vlastnost).
- **P��klad**:
  ```csharp
  /// <summary>
  /// T��da reprezentuj�c� u�ivatele aplikace.
  /// </summary>
  public class Uzivatel { }
  ```

#### **`<param>`**

- **Popis**: Dokumentace parametru metody/konstruktoru.
- **Atribut**: `name` (n�zev parametru).
- **P��klad**:
  ```csharp
  /// <param name="jmeno">Cel� jm�no u�ivatele.</param>
  public void NastavJmeno(string jmeno) { }
  ```

#### **`<returns>`**

- **Popis**: Vysv�tluje n�vratovou hodnotu metody.
- **P��klad**:
  ```csharp
  /// <returns>V�k u�ivatele v letech.</returns>
  public int ZiskejVek() => 25;
  ```

#### **`<remarks>`**

- **Popis**: Roz���en� informace o �lenu (dopl�uje `<summary>`).
- **P��klad**:
  ```csharp
  /// <remarks>
  /// Tato metoda m��e vyvolat v�jimku p�i neplatn�m vstupu.
  /// </remarks>
  ```

---

### **3. Pokro�il� zna�ky**

#### **`<exception>`**

- **Popis**: Dokumentuje v�jimky, kter� metoda m��e vyvolat.
- **Atribut**: `cref` (odkaz na typ v�jimky).
- **P��klad**:
  ```csharp
  /// <exception cref="ArgumentNullException">Pokud je jmeno pr�zdn�.</exception>
  public void Validuj(string jmeno) {
      if (string.IsNullOrEmpty(jmeno)) throw new ArgumentNullException();
  }
  ```

#### **`<example>`**

- **Popis**: Uk�zka pou�it� k�du (�asto s `<code>`).
- **P��klad**:
  ```csharp
  /// <example>
  /// <code>
  /// var uzivatel = new Uzivatel();
  /// uzivatel.NastavJmeno("Karel");
  /// </code>
  /// </example>
  ```

#### **`<see>` a `<seealso>`**

- **Popis**: Odkazy na jin� �leny nebo typy.
- **P��klad**:
  ```csharp
  /// <see cref="Uzivatel"/> // Odkaz na t��du
  /// <seealso cref="NastavJmeno"/>
  ```

#### **`<value>`**

- **Popis**: Popisuje v�znam vlastnosti (property).
- **P��klad**:
  ```csharp
  /// <value>Jm�no u�ivatele ve form�tu "P��jmen�, Jm�no".</value>
  public string CeleJmeno { get; set; }
  ```

#### **`<typeparam>`**

- **Popis**: Dokumentuje generick� typ parametru.
- **Atribut**: `name` (n�zev parametru).
- **P��klad**:
  ```csharp
  /// <typeparam name="T">Typ prvku v kolekci.</typeparam>
  public class Kolekce<T> { }
  ```

---

### **4. Form�tov�n� a dal�� zna�ky**

#### **`<c>`**

- **Popis**: Zv�razn� k�d v textu (inline).
  ```csharp
  /// <c>int x = 5;</c>
  ```

#### **`<code>`**

- **Popis**: Blok k�du (v kombinaci s `<example>`).
  ```csharp
  /// <code>
  /// if (cond) { ... }
  /// </code>
  ```

#### **`<para>`**

- **Popis**: Vlo�� odstavec (nap�. uvnit� `<remarks>`).
  ```csharp
  /// <remarks>
  /// <para>Prvn� odstavec.</para>
  /// <para>Druh� odstavec.</para>
  /// </remarks>
  ```

#### **`<inheritdoc/>`**

- **Popis**: D�d� dokumentaci z nad�azen�ho �lenu (u�et�� duplikaci).
  ```csharp
  /// <inheritdoc />
  public override void Metoda() { }
  ```

---

### **5. Generov�n� dokumentace**

1. **Povolen� v projektu** (`.csproj`):
   ```xml
   <PropertyGroup>
     <GenerateDocumentationFile>true</GenerateDocumentationFile>
   </PropertyGroup>
   ```
2. **N�stroje pro generov�n�**:
   - **DocFX**: Vytv��� webovou dokumentaci.
   - **Sandcastle**: Generuje CHM nebo HTML.
   - **VS IntelliSense**: Zobrazuje popisky p��mo v editoru.

---

### **6. Doporu�en� postupy**

- **Dokumentujte ve�ejn� API**: T��dy, metody, vlastnosti.
- **Popisujte v�jimky**: Umo��uje v�voj���m p�edch�zet chyb�m.
- **Pou��vejte `<example>`**: Usnad�uje pochopen� pou�it�.
- **Vyhn�te se duplikac�m**: Vyu��vejte `<inheritdoc/>`.

---

### **7. Uk�zka komplexn�ho pou�it�**

```csharp
/// <summary>
/// T��da pro spr�vu matematick�ch operac�.
/// </summary>
public class Matematika {
    /// <summary>
    /// Se�te dv� ��sla.
    /// </summary>
    /// <param name="a">Prvn� ��slo.</param>
    /// <param name="b">Druh� ��slo.</param>
    /// <returns>Sou�et ��sel.</returns>
    /// <exception cref="OverflowException">Pokud v�sledek p�ekro�� rozsah.</exception>
    /// <example>
    /// <code>
    /// var result = Matematika.Secti(5, 3); // Vr�t� 8
    /// </code>
    /// </example>
    public static int Secti(int a, int b) => checked(a + b);
}
```

---

### **8. V�hody XML dokumentace**

- **Profesion�ln� vzhled API**: Automaticky generovan� dokumentace.
- **Lep�� t�mov� spolupr�ce**: Jasn� popis funkcionality.
- **Integrace s n�stroji**: Podpora pro CI/CD a verze dokumentace.
