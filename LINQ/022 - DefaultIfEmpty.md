
### **`DefaultIfEmpty`**  

Metoda `DefaultIfEmpty` slouží k **zajištění neprázdného výsledku** pro LINQ dotazy. Pokud je zdrojová kolekce prázdná, vrátí sekvenci obsahující **výchozí hodnotu**, což zabraňuje chybám při práci s neexistujícími daty. Je užitečná zejména při **left outer joins** a zpracování potenciálně prázdných kolekcí.

---

#### **Základní charakteristika**  

- **Účel**:  
  - Zajistit, aby výsledek dotazu vždy obsahoval alespoň jeden prvek.  
  - Nahradit prázdnou kolekci výchozí hodnotou (implicitní nebo zadanou).  
- **Návratový typ**: `IEnumerable<T>` (sekvence s jedním prvkem, pokud je zdroj prázdný).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  
- **Výchozí hodnota**:  
  - Pro **referenční typy**: `default(T)` (obvykle `null`).  
  - Pro **hodnotové typy**: `0`, `false`, nebo výchozí struktura.  
  - Lze zadat vlastní výchozí hodnotu.  

---

### **Syntaxe**  

```csharp
// Implicitní výchozí hodnota (default(T)):
IEnumerable<T> výsledek = kolekce.DefaultIfEmpty();

// Vlastní výchozí hodnota:
IEnumerable<T> výsledek = kolekce.DefaultIfEmpty(výchozíHodnota);
```

---

### **Klíčové vlastnosti**  

1. **Zachování původních dat**:  
   - Pokud zdrojová kolekce **není prázdná**, vrátí její prvky beze změny.  
   - Pokud je **prázdná**, vrátí sekvenci s jedním prvkem (`default(T)` nebo zadanou hodnotu).  

2. **Použití v left outer joins**:  
   - Kombinuje se s `GroupJoin` a `SelectMany` pro zpracování nepovinných relací.  
   ```csharp
   var query = zákazníci
       .GroupJoin(
           objednávky,
           z => z.Id,
           o => o.ZákazníkId,
           (z, o) => new { Zákazník = z, Objednávky = o.DefaultIfEmpty() }
       )
       .SelectMany(x => x.Objednávky, (z, o) => new { z.Zákazník, Objednávka = o });
   ```

3. **Podpora pro EF Core**:  
   - Při použití s `IQueryable` se převádí na SQL `COALESCE` nebo `LEFT JOIN`.  

---

### **Příklady použití**  

#### **1. Základní použití**  

```csharp
List<int> prázdná = new List<int>();
List<int> čísla = new List<int> { 10, 20 };

var výsledekPrázdná = prázdná.DefaultIfEmpty(); // { 0 }
var výsledekČísla = čísla.DefaultIfEmpty();     // { 10, 20 }
```

#### **2. Vlastní výchozí hodnota**  

```csharp
List<string> prázdná = new List<string>();
var výsledek = prázdná.DefaultIfEmpty("N/A"); // { "N/A" }
```

#### **3. Použití v dotazech s objekty**  

```csharp
class Produkt { public string Název { get; set; } public decimal Cena { get; set; } }
List<Produkt> produkty = new List<Produkt>();

var výchozíProdukt = new Produkt { Název = "Žádný produkt", Cena = 0 };
var výsledek = produkty.DefaultIfEmpty(výchozíProdukt); // Jeden prvek: výchozíProdukt
```

---

### **Časté chyby a upozornění**  

1. **Záměna s `FirstOrDefault`**:  
   - `FirstOrDefault` vrací **jednu hodnotu** (nebo `default`), zatímco `DefaultIfEmpty` vrací **sekvenci**.  
   ```csharp
   int první = prázdná.FirstOrDefault(); // 0 (jedna hodnota)
   IEnumerable<int> sekvence = prázdná.DefaultIfEmpty(); // { 0 } (kolekce)
   ```

2. **Neošetřené `null` v referenčních typech**:  
   ```csharp
   List<string> prázdná = new List<string>();
   string hodnota = prázdná.DefaultIfEmpty().First(); // null (může způsobit chybu!).
   ```

3. **Nadbytečné použití**:  
   ```csharp
   // Zbytečné – Where vrátí prázdnou kolekci, DefaultIfEmpty přidá 0:
   var výsledek = čísla.Where(x => x > 100).DefaultIfEmpty();
   ```

---

### **Tipy pro efektivní použití**  

- **Kombinace s `SelectMany` pro left join**:  
  ```csharp
  var leftJoin = from z in zákazníci
                 join o in objednávky on z.Id equals o.ZákazníkId into skupina
                 from o in skupina.DefaultIfEmpty()
                 select new { z.Jméno, Objednávka = o?.Částka ?? 0 };
  ```
- **Vlastní výchozí hodnota pro lepší čitelnost**:  
  ```csharp
  var výsledek = kolekce.DefaultIfEmpty(-1); // Místo implicitního 0.
  ```
- **Ošetření `null` u referenčních typů**:  
  ```csharp
  var výsledek = kolekce.DefaultIfEmpty() ?? new List<T> { new T() };
  ```

---

### **Shrnutí**  

- `DefaultIfEmpty` zajišťuje **neprázdný výsledek** pro LINQ dotazy.  
- Používá se pro **left outer joins**, zpracování prázdných kolekcí a předcházení `InvalidOperationException`.  
- Umožňuje definovat **vlastní výchozí hodnotu** pro větší kontrolu nad výstupem.  

📖 **Zdroj**: [Microsoft Docs – DefaultIfEmpty](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.defaultifempty)
