
### Číselné typy s plovoucí desetinou čárkou v jazyce C#

C# podporuje tři typy pro reprezentaci desetinných čísel: `float`, `double` a `decimal`. Liší se **přesností, rozsahem a použitím**. Zatímco `float` a `double` jsou binární formáty (vhodné pro vědecké výpočty), `decimal` poskytuje přesné desetinné hodnoty (ideální pro finanční operace).

---

#### 1. **Přehled typů**

| Typ (C#) | Rozsah přibližně                     | Přesnost         | Velikost (bitů) | .NET typ          | Popis                     |
|----------|---------------------------------------|------------------|-----------------|-------------------|---------------------------|
| `float`  | ±1,5×10⁻⁴⁵ až ±3,4×10³⁸              | 6–9 míst         | 32              | `System.Single`   | Rychlý, menší přesnost    |
| `double` | ±5,0×10⁻³²⁴ až ±1,7×10³⁰⁸            | 15–17 míst       | 64              | `System.Double`   | **Výchozí** pro desetinná čísla |
| `decimal`| ±1,0×10⁻²⁸ až ±7,9×10²⁸              | 28–29 míst       | 128             | `System.Decimal`  | Přesný, pro peněžní částky |

---

#### 2. **Klíčové vlastnosti**

- **Přesnost**:
  - `float` a `double` mohou mít **zaokrouhlovací chyby** (např. `0,1` nelze přesně reprezentovat v binárním formátu).
  - `decimal` uchovává přesné desetinné hodnoty (desítková aritmetika).
- **Výkon**:
  - `float` a `double` jsou rychlejší (hardwarově akcelerované).
  - `decimal` je pomalejší, ale přesnější.
- **Použití**:
  - `double`: Vědecké výpočty, hry, grafika.
  - `decimal`: Finanční aplikace, účetnictví (vyžaduje přesnost).
  - `float`: Optimalizace paměti (např. velká pole v 3D grafice).

---

#### 3. **Deklarace a literály**

```csharp
float a = 3.14F;         // Sufix "F"
double b = 3.14;         // Výchozí typ (sufix není potřeba)
decimal c = 3.14M;       // Sufix "M"

// Vědecká notace:
double d = 1.5e6;        // 1,5 × 10⁶ = 1 500 000
decimal e = 2.5e-3m;     // 0,0025
```

---

#### 4. **Operace a konverze**

- **Implicitní konverze**:
  - Platí: `float` → `double` → `decimal`.
  ```csharp
  float f = 10.5F;
  double g = f; // OK
  decimal h = (decimal)g; // Vyžaduje explicitní převod
  ```
- **Explicitní konverze**:
  - Při převodu na menší typ může dojít ke **ztrátě přesnosti** nebo přetečení.
  ```csharp
  decimal money = 100.99m;
  int integerPart = (int)money; // 100 (ztráta desetinné části)
  ```
- **Matematické operace**:
  - Pro `decimal` jsou podporovány stejné operátory (`+`, `-`, `*`, `/`), ale s vyšší přesností.
  - Pozor na dělení nulou (vyvolá `DivideByZeroException` pro `decimal`).

---

#### 5. **Přesnost a zaokrouhlování**

- **Problémy s přesností** u `float` a `double`:
  ```csharp
  double x = 0.1 + 0.2;
  Console.WriteLine(x == 0.3); // False! (0,30000000000000004)
  ```
- **Řešení pro `decimal`**:
  ```csharp
  decimal y = 0.1m + 0.2m;
  Console.WriteLine(y == 0.3m); // True
  ```
- **Zaokrouhlování**:
  ```csharp
  decimal value = 10.567m;
  decimal rounded = Math.Round(value, 2); // 10,57
  ```

---

#### 6. **Příklady použití**

- **Finanční aplikace**:
  ```csharp
  decimal salary = 2500.50m;
  decimal tax = salary * 0.15m;
  ```
- **Vědecké simulace**:
  ```csharp
  double velocity = 9.81 * 5.0; // Fyzikální výpočty
  ```
- **Optimalizace paměti**:
  ```csharp
  float[] points3D = new float[1000]; // Uložení souřadnic v paměti
  ```

---

#### 7. **Časté chyby a tipy**

- **Porovnávání `float`/`double`**:
  - Nikdy nepoužívejte `==` pro porovnání! Místo toho kontrolujte rozdíl s tolerancí:
  ```csharp
  bool isEqual = Math.Abs(a - b) < 0.0001;
  ```
- **Záměna sufixů**:
  - Zapomenutí `F` u `float` nebo `M` u `decimal` způsobí chybu kompilace.
- **Nadbytečné použití `decimal`**:
  - Nepoužívejte `decimal` pro výpočty, kde stačí přibližná hodnota (např. fyzikální simulace).
- **Formátování výstupu**:
  ```csharp
  Console.WriteLine(123.456m.ToString("C")); // "123,46 Kč" (formát měny)
  Console.WriteLine(123.4567.ToString("F2")); // "123,46" (2 desetinná místa)
  ```

---

#### 8. **Nullable typy**

- Lze kombinovat s `?` pro reprezentaci `null`:
  ```csharp
  decimal? price = null; // Užitečné v databázích
  if (price.HasValue)
      Console.WriteLine(price.Value);
  ```

---

### Shrnutí

- **Výchozí typ**: `double` (používejte pro většinu výpočtů).
- **Přesnost**: `decimal` pro peníze, `float`/`double` pro aproximace.
- **Sufixy**: `F` pro `float`, `M` pro `decimal`.
- **Porovnávání**: U binárních typů vždy s tolerancí.

📌 **Tip**: Pro práci s penězi vždy používejte `decimal`. Pro grafiku nebo matematiku volte `float`/`double` kvůli výkonu.
