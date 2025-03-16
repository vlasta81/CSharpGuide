
### Celočíselné typy v jazyce C#

C# nabízí řadu **celočíselných datových typů** s různou velikostí, rozsahem a vlastnostmi (se znaménkem/bez znaménka). Ty se používají podle požadavků na paměť, rozsah hodnot a účelu.

---

#### 1. **Přehled typů**

| Typ (C#) | Rozsah                                      | Velikost (bitů) | .NET alias       | Popis                     |
|----------|---------------------------------------------|-----------------|------------------|---------------------------|
| `sbyte`  | -128 až 127                                 | 8               | `System.SByte`   | Se znaménkem              |
| `byte`   | 0 až 255                                    | 8               | `System.Byte`    | Bez znaménka              |
| `short`  | -32 768 až 32 767                           | 16              | `System.Int16`   | Se znaménkem              |
| `ushort` | 0 až 65 535                                 | 16              | `System.UInt16`  | Bez znaménka              |
| `int`    | -2 147 483 648 až 2 147 483 647             | 32              | `System.Int32`   | Se znaménkem (**default** pro celá čísla) |
| `uint`   | 0 až 4 294 967 295                          | 32              | `System.UInt32`  | Bez znaménka              |
| `long`   | -9 223 372 036 854 775 808 až 9 223 372 036 854 775 807 | 64 | `System.Int64` | Se znaménkem (pro velké hodnoty) |
| `ulong`  | 0 až 18 446 744 073 709 551 615             | 64              | `System.UInt64`  | Bez znaménka              |
| `nint`   | Závisí na architektuře (32/64 bitů)          | 32/64           | `System.IntPtr`  | Se znaménkem (pro ukazatele, low-level kód) |
| `nuint`  | Závisí na architektuře (32/64 bitů)          | 32/64           | `System.UIntPtr` | Bez znaménka (pro ukazatele) |

---

#### 2. **Klíčové vlastnosti**

- **Se znaménkem vs. bez znaménka**:
  - Typy s `u` na začátku (`uint`, `ushort` apod.) **neukládají záporná čísla**.
  - Např. `byte` má rozsah 0–255, zatímco `sbyte` –128–127.
- **Velikost a výkon**:
  - Menší typy (např. `byte`) šetří paměť, ale operace s nimi mohou být **pomalejší** kvůli konverzím na `int`.
  - Pro většinu případů je vhodný `int` (optimalizovaný pro 32bitové procesory).
- **Nativní typy (`nint`, `nuint`)**:
  - Od C# 9 (.NET 5+), velikost odpovídá architektuře (32/64 bitů).
  - Používají se pro **interoperabilitu s nativním kódem** (např. při práci s ukazateli).
  - Rozsah: `nint` má stejný rozsah jako `int` nebo `long` podle architektury.

---

#### 3. **Deklarace a inicializace**

```csharp
sbyte a = -10;
byte b = 200;
short c = -32000;
ushort d = 65000;
int e = -2_147_483_648; // Podtržítko pro čitelnost
uint f = 4_294_967_295U; // Sufix "U"
long g = 9_223_372_036_854_775_807L; // Sufix "L"
ulong h = 18_446_744_073_709_551_615UL; // Sufix "UL"
nint i = -100; // Stejné jako IntPtr
nuint j = 100; // Stejné jako UIntPtr
```

---

#### 4. **Konverze a operace**

- **Implicitní konverze**:
  - Platí pro "menší" → "větší" typy (např. `int` → `long`).
  ```csharp
  int number = 100;
  long bigNumber = number; // OK
  ```
- **Explicitní konverze**:
  - Vyžaduje se při převodu "většího" typu na "menší" (může způsobit **ztrátu dat** nebo přetečení).
  ```csharp
  int x = 300;
  byte y = (byte)x; // y = 44 (přetečení)!
  ```
- **Přetečení (overflow)**:
  - Výchozí chování: hodnota se "ořízne" (např. 300 → 44 pro `byte`).
  - Lze kontrolovat pomocí `checked`:
  ```csharp
  checked
  {
      byte z = (byte)300; // Vyvolá výjimku OverflowException
  }
  ```

---

#### 5. **Literály a sufixy**

- **Výchozí typ literálu**: `int` pro celá čísla bez sufixu.
- Sufixy pro explicitní typy:
  - `U` → `uint` nebo `ulong`.
  - `L` → `long`.
  - `UL` → `ulong`.
  ```csharp
  uint value1 = 123U;
  long value2 = 123L;
  ulong value3 = 123UL;
  ```

---

#### 6. **Nullable typy**

- Lze použít s `?` pro reprezentaci `null` (např. `int?`).
  ```csharp
  int? nullableInt = null; // Užitečné v databázích nebo API
  ```

---

#### 7. **Příklady použití**

- **Čítače a indexy**: `int` (defaultní volba).
- **Binární data**: `byte[]` (např. čtení souborů).
- **Výpočty s velkými čísly**: `long` nebo `ulong`.
- **Nízkoúrovňový kód**: `nint`, `nuint` (práce s pamětí, interoperabilita).

---

#### 8. **Časté chyby a tipy**

- **Přetečení (overflow)**: Vždy zvažte použití `checked` pro kritický kód.
- **Záměna signed/unsigned**:
  ```csharp
  uint a = 10;
  int b = -5;
  // if (a > b) ... // Neočekávané chování (b je konvertováno na uint!)
  ```
- **Zbytečné použití malých typů**: `short` nebo `byte` často nevedou k lepšímu výkonu.
- **Nativní typy (`nint`, `nuint`)**: Používejte pouze pro specifické scénáře (interoperabilita, optimalizace).

---

### Shrnutí

- **Defaultní typ**: `int`.
- **Bez znaménka**: `uint`, `ushort`, `byte`, `ulong`, `nuint`.
- **Nativní typy**: `nint` a `nuint` pro práci s ukazateli nebo optimalizaci podle architektury.
- **Pozor na přetečení**: V kritických místech používejte `checked`.

📌 **Tip**: Pro většinu aplikací stačí `int` a `long`. Menší typy použijte, až když je to nezbytné (např. pro práci s binárními daty nebo úsporu paměti v masivních polích).
