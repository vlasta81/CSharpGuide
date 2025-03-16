
### **1. Definice hodnotových typù**

- Ukládají **data pøímo v pamìti** (na zásobníku *stack* nebo uvnitø objektù na haldì *heap*).
- Kopírováním vzniká **nezávislá kopie dat**.
- Mají **pevnou velikost** a jsou **rychlejší** pro pøístup než referenèní typy.

---

### **2. Kategorie hodnotových typù**

#### **a) Vestavìné primitivní typy**

- **Celá èísla**:
  - `byte` (0–255), `sbyte` (-128–127)
  - `short` (-32 768–32 767), `ushort` (0–65 535)
  - `int` (-2 miliardy–2 miliardy), `uint`
  - `long` (velký rozsah), `ulong`
- **Desetinná èísla**:
  - `float` (~6–9 platných míst), `double` (~15–17 platných míst)
  - `decimal` (28–29 platných míst, pøesné pro finance).
- **Logické hodnoty**:
  - `bool` (`true`/`false`).
- **Znaky**:
  - `char` (Unicode znak, napø. `'A'`).

#### **b) Struktury (`struct`)**

- Uživatelsky definované složené typy.
- **Pøíklad**: `DateTime`, `Point`, vlastní struktury.
- **Vlastnosti**:
  - Mohou obsahovat pole, metody, vlastnosti.
  - Nemohou dìdit (kromì implementace rozhraní).
  - Implicitní konstruktor bez parametrù nelze definovat.
  ```csharp
  public struct Bod {
      public int X;
      public int Y;
      public Bod(int x, int y) => (X, Y) = (x, y);
  }
  ```

#### **c) Výètové typy (`enum`)**

- Definují množinu pojmenovaných konstant.
- **Výchozí typ**: `int` (lze zmìnit na `byte`, `short` apod.).
- **Pøíklad**:
  ```csharp
  public enum DenVTydnu {
      Pondeli, Utery, Streda, Ctvrtek, Patek, Sobota, Nedele
  }
  ```

---

### **3. Klíèové vlastnosti**

- **Implicitní hodnota**: Inicializovány na výchozí hodnotu (napø. `0` pro èísla, `false` pro `bool`).
- **Životnost**: Znièeny pøi opuštìní kontextu (napø. po dokonèení metody).
- **Nullabilita**: Nelze pøiøadit `null` (pouze s `Nullable<T>` nebo `?`).

---

### **4. Nullable hodnotové typy**

- Umožòují pøiøadit `null` pomocí `Nullable<T>` nebo zkratky `?`.
- **Pøíklad**:
  ```csharp
  int? cislo = null;
  if (cislo.HasValue) {
      Console.WriteLine(cislo.Value);
  }
  ```

---

### **5. Rozdíly oproti referenèním typùm**

| **Vlastnost**       | **Hodnotové typy**       | **Referenèní typy**       |
|----------------------|--------------------------|---------------------------|
| Ukládání             | Pøímo na stack/heap      | Reference na heap         |
| Kopírování           | Hluboká kopie            | Kopie reference           |
| Výchozí hodnota      | `0`, `false` apod.       | `null`                    |
| Výkon                | Rychlejší pøístup        | Pomalejší (GC overhead)   |

---

### **6. Boxing a Unboxing**

- **Boxing**: Konverze hodnotového typu na `object` (ukládá se na heap).
- **Unboxing**: Zpìtná konverze na hodnotový typ.
- **Pøíklad**:
  ```csharp
  int cislo = 42;
  object boxed = cislo; // Boxing
  int unboxed = (int)boxed; // Unboxing
  ```

---

### **7. Doporuèené použití**

- **Struktury**: Pro malé, nemìnné datové struktury (napø. souøadnice).
- **Enumy**: Pro omezené množiny hodnot (dny v týdnu, stavy).
- **Nullable**: Pokud hodnota mùže chybìt (napø. v databázových polích).

---

### **8. Pozor na**

- **Zbyteèné kopírování**: Velké struktury mohou zpomalit kód.
- **Boxing overhead**: Èasté boxování snižuje výkon.
- **Imutabilita**: U struktur doporuèeno pro pøedcházení chybám.

---

### **9. Ukázka kódu**

```csharp
// Struktura
public struct Velikost {
    public int Sirka { get; }
    public int Vyska { get; }
    public Velikost(int sirka, int vyska) => (Sirka, Vyska) = (sirka, vyska);
}

// Enum
public enum Stav { Aktivni, Neaktivni, Pozastaveno }

// Použití
Velikost v = new Velikost(1920, 1080);
Stav currentStav = Stav.Aktivni;
int? skore = null;
```
