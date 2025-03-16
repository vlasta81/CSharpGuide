
### Přetypování v jazyce C#  

Přetypování (type casting) je proces převodu hodnoty jednoho datového typu na jiný datový typ. V C# existují dva základní typy přetypování: **implicitní** (automatické) a **explicitní** (ruční). Zde je přehled klíčových konceptů:

---

#### **1. Implicitní přetypování**  

- Probíhá automaticky, pokud je konverze bezpečná (žádná ztráta dat).  
- Platí pro kompatibilní datové typy, kde cílový typ má větší kapacitu než zdrojový.  
- **Příklady kompatibilních typů**:  
  ```csharp
  int cisloInt = 100;
  long cisloLong = cisloInt; // Implicitní převod int → long

  float cisloFloat = 3.14f;
  double cisloDouble = cisloFloat; // Implicitní převod float → double
  ```

---

#### **2. Explicitní přetypování**  

- Vyžaduje manuální operaci pomocí syntaxe `(typ)`.  
- Nutné, pokud hrozí ztráta dat nebo typy nejsou kompatibilní.  
- **Příklad**:  
  ```csharp
  double cisloDouble = 123.456;
  int cisloInt = (int)cisloDouble; // Výsledek: 123 (ztráta desetinné části)
  ```  
- **Rizika**:  
  - Přetečení (overflow) při konverzi na menší typ (např. `long` → `int`).  
  - Pro kontrolu přetečení lze použít `checked`:  
    ```csharp
    checked
    {
        long velkeCislo = 1_000_000_000;
        int malyTyp = (int)velkeCislo; // Vyvolá OverflowException
    }
    ```

---

#### **3. Metoda `Convert` třídy**  

- Univerzální převody mezi typy (včetně nekompatibilních).  
- **Příklad**:  
  ```csharp
  string text = "123";
  int cislo = Convert.ToInt32(text); // Převod string → int
  ```  
- **Výhody**: Podpora pro `null`, různé formáty (např. hexadecimální).  
- **Nevýhody**: Vyvolá výjimku (`FormatException`), pokud převod selže.

---

#### **4. Parsování řetězců**  

- Převod řetězců na číselné typy pomocí metod `Parse()` a `TryParse()`.  
- **Příklad**:  
  ```csharp
  string vstup = "42";
  int cislo = int.Parse(vstup); // Vyvolá výjimku při chybě

  if (int.TryParse(vstup, out int vysledek))
  {
      // Bezpečný převod (vrací bool)
  }
  ```

---

#### **5. Operátory `as` a `is`**  

- **`as`**: Bezpečný převod pro reference typy. Vrací `null` při neúspěchu.  
  ```csharp
  object obj = "text";
  string text = obj as string; // Úspěšné přetypování
  ```  
- **`is`**: Kontrola kompatibility typů.  
  ```csharp
  if (obj is string)
  {
      // proved akci
  }
  ```

---

#### **6. Boxing a Unboxing**  

- **Boxing**: Konverze hodnotového typu na `object` (implicitní).  
  ```csharp
  int cislo = 42;
  object boxed = cislo; // Boxing
  ```  
- **Unboxing**: Konverze zpět na hodnotový typ (explicitní).  
  ```csharp
  int unboxed = (int)boxed; // Unboxing
  ```

---

#### **7. Uživatelsky definované přetypování**  

- Lze definovat pomocí přetížení operátorů `explicit` a `implicit`.  
- **Příklad**:  
  ```csharp
  public class VlastniTyp
  {
      public int Hodnota { get; set; }

      public static explicit operator VlastniTyp(int x)
      {
          return new VlastniTyp { Hodnota = x };
      }
  }

  VlastniTyp objekt = (VlastniTyp)42; // Explicitní převod
  ```

---

#### **Časté chyby**  

- `InvalidCastException`: Neplatné přetypování (např. `string` → `int`).  
- `OverflowException`: Přetečení hodnoty.  
- `FormatException`: Chyba parsování (např. `int.Parse("abc")`).

---

#### **Doporučení**  

- Pro čtení vstupů používej `TryParse()` místo `Parse()`.  
- U reference typů preferuj `as` a `is` pro bezpečnost.  
- Při explicitním přetypování kontroluj rozsah hodnot.
