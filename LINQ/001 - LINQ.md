
### **LINQ (Language Integrated Query)**  

LINQ je technologie integrovaná do jazyka C#, která umožňuje **dotazování nad daty** přímo v syntaxi jazyka. Kombinuje sílu SQL-like dotazů s výhodami silného typového systému C#.

---

#### **Základní charakteristika**  

- **Integrace do jazyka**: LINQ dotazy jsou součástí kódu v C#, nikoli řetězců (např. jako v SQL).  
- **Univerzální použití**: Lze dotazovat různé zdroje dat (kolekce, XML, SQL databáze, Entity Framework, JSON atd.).  
- **Dva přístupy**:  
  1. **Query Syntax** (deklarativní styl podobný SQL).  
  2. **Method Syntax** (metody rozšíření a lambda výrazy).  

---

#### **Klíčové komponenty**  

1. **Extension Methods** (`System.Linq`):  
   - Metody jako `Where()`, `Select()`, `OrderBy()` atd.  
   - Pracují s rozhraním `IEnumerable<T>` nebo `IQueryable<T>`.  
2. **Lambda Expressions**:  
   - Zkrácený zápis pro anonymní funkce (např. `x => x.Age > 18`).  
3. **Anonymous Types**:  
   - Vytváření anonymních objektů pro projekce (`new { x.Name, x.Age }`).  
4. **Delegáti**:  
   - Např. `Func<T, bool>` pro definici podmínek.  

---

#### **Syntaxe LINQ**  

**1. Query Syntax** (SQL-like):  
```csharp
var result = from person in people
             where person.Age > 18
             orderby person.Name
             select new { person.Name, person.City };
```

**2. Method Syntax** (fluent style):  
```csharp
var result = people
    .Where(person => person.Age > 18)
    .OrderBy(person => person.Name)
    .Select(person => new { person.Name, person.City });
```

Oba přístupy jsou ekvivalentní – kompilátor překládá Query Syntax na Method Syntax.

---

#### **Hlavní operátory LINQ**  

| Kategorie         | Příklady                                      | Popis                              |
|--------------------|----------------------------------------------|------------------------------------|
| **Filtrování**     | `Where()`, `OfType<T>()`                     | Výběr prvků podle podmínky.        |
| **Řazení**         | `OrderBy()`, `ThenBy()`, `Reverse()`         | Seřazení výsledků.                 |
| **Seskupení**      | `GroupBy()`, `ToLookup()`                    | Seskupení podle klíče.             |
| **Projekce**       | `Select()`, `SelectMany()`                   | Transformace výsledků.             |
| **Spojení**        | `Join()`, `GroupJoin()`                      | Spojení dvou kolekcí.              |
| **Agregace**       | `Sum()`, `Average()`, `Count()`, `Max()`     | Výpočty nad daty.                  |
| **Stránkování**    | `Skip()`, `Take()`                           | Výběr podmnožiny prvků.            |
| **Elementy**       | `First()`, `Single()`, `ElementAt()`         | Výběr konkrétního prvku.           |
| **Konverze**       | `ToList()`, `ToArray()`, `ToDictionary()`    | Převod na konkrétní kolekci.       |

---

#### **LINQ Providers**  

- **LINQ to Objects**: Pro práci s kolekcemi v paměti (pole, seznamy).  
- **LINQ to SQL/Entity Framework**: Pro dotazování relačních databází.  
- **LINQ to XML**: Pro práci s XML dokumenty (`XDocument`, `XElement`).  
- **LINQ to JSON** (pomocí knihoven jako Newtonsoft.Json).  

---

#### **Deferred Execution (Odložené vykonání)**  

- Dotaz se **nevykoná okamžitě** při deklaraci, ale až při iteraci výsledku (např. ve `foreach` nebo při volání `ToList()`).  
- Umožňuje optimalizaci a kombinování dotazů.  
- **Příklad**:  
  ```csharp
  var query = people.Where(p => p.Age > 18); // Dotaz se ještě nevykonal
  var results = query.ToList(); // Vyvolání dotazu
  ```

---

#### **Rozdíl mezi `IEnumerable` a `IQueryable`**  

- **`IEnumerable`**:  
  - Pro práci s lokálními daty (v paměti).  
  - Vykonává se na straně klienta.  
- **`IQueryable`**:  
  - Pro dotazy nad externími zdroji (např. SQL).  
  - Generuje optimalizovaný SQL dotaz (např. pro Entity Framework).  

---

#### **Příklad: LINQ s Entity Framework**  

```csharp
using (var context = new AppDbContext())
{
    var adults = context.People
                        .Where(p => p.Age >= 18)
                        .OrderBy(p => p.Name)
                        .ToList();
}
```

---

#### **Výhody LINQ**  

- Jednotný způsob dotazování různých zdrojů.  
- Čitelnost a udržovatelnost kódu.  
- Kompilátor kontroluje chyby (na rozdíl od SQL v řetězcích).  

#### **Nevýhody**  

- Někdy méně efektivní než nativní SQL (nutnost optimalizace).  
- Složitější debugování.  

---

## **Metody**

### **Základní operace**
- **Select** – `Projekce prvků`  
- **Where** – `Filtrace podle podmínky`  
- **OfType** – `Filtrace podle typu`  

### **Řazení a seskupování**
- **OrderBy** – `Řazené vzestupne`  
- **OrderByDescending** – `Řazení sestupně`  
- **ThenBy** – `Sekundární řazení vzestupně`  
- **ThenByDescending** – `Sekundární řazení sestupně`  
- **GroupBy** – `Seskupení prvků`  
- **ToLookup** – `Vytvoření lookup tabulky`  

### **Spojování kolekcí**
- **Join** – `Relační spojení`  
- **GroupJoin** – `Seskupené spojení`  
- **Concat** – `Spojení kolekcí`  

### **Agregační funkce**
- **Sum** – `Součet hodnot`  
- **Average** – `Průměrná hodnota`  
- **Count** – `Počet prvků`  
- **Max** – `Maximální hodnota`  
- **Aggregate** – `Agregace hodnot`  

### **Práce s prvky**
- **First** – `První prvek`  
- **FirstOrDefault** – `První prvek s výchozí hodnotou`  
- **Last** – `Poslední prvek`  
- **LastOrDefault** – `Poslední prvek s výchozí hodnotou`  
- **Single** – `Jediný prvek`  
- **SingleOrDefault** – `Jediný prvek s výchozí hodnotou`  
- **ElementAt** – `Prvek na pozici`  
- **ElementAtOrDefault** – `Prvek na pozici s výchozí hodnotou`  

### **Podmíněné operace**
- **Any** – `Alespoň jeden splňuje podmínku`  
- **All** – `Všechny prvky splňují podmínku`  
- **Contains** – `Obsahuje prvek`  

### **Porovnání a manipulace**
- **SequenceEqual** – `Porovnání sekvencí`  
- **Distinct** – `Unikátní prvky`  
- **Except** – `Rozdíl kolekcí`  
- **Intersect** – `Průnik kolekcí`  
- **Union** – `Sjednocení kolekcí`  

### **Práce s sekvencemi**
- **Skip** – `Přeskočení prvků`  
- **SkipWhile** – `Přeskočení dokud podmínka platí`  
- **Take** – `Výběr prvků`  
- **TakeWhile** – `Výběr dokud podmínka platí`  

### **Speciální kolekce**
- **Empty** – `Prázdná kolekce`  
- **Range** – `Sekvence čísel`  
- **Repeat** – `Opakující se prvek`  
- **DefaultIfEmpty** – `Výchozí hodnota pro prázdnou kolekci`  

---

### **Doporučené zdroje**  

- Oficiální dokumentace: [Microsoft LINQ Guide](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/concepts/linq/)  
