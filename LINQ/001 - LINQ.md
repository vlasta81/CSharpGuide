
### **LINQ (Language Integrated Query)**  

LINQ je technologie integrovaná do jazyka C#, která umožòuje **dotazování nad daty** pøímo v syntaxi jazyka. Kombinuje sílu SQL-like dotazù s výhodami silného typového systému C#.

---

#### **Základní charakteristika**  

- **Integrace do jazyka**: LINQ dotazy jsou souèástí kódu v C#, nikoli øetìzcù (napø. jako v SQL).  
- **Univerzální použití**: Lze dotazovat rùzné zdroje dat (kolekce, XML, SQL databáze, Entity Framework, JSON atd.).  
- **Dva pøístupy**:  
  1. **Query Syntax** (deklarativní styl podobný SQL).  
  2. **Method Syntax** (metody rozšíøení a lambda výrazy).  

---

#### **Klíèové komponenty**  

1. **Extension Methods** (`System.Linq`):  
   - Metody jako `Where()`, `Select()`, `OrderBy()` atd.  
   - Pracují s rozhraním `IEnumerable<T>` nebo `IQueryable<T>`.  
2. **Lambda Expressions**:  
   - Zkrácený zápis pro anonymní funkce (napø. `x => x.Age > 18`).  
3. **Anonymous Types**:  
   - Vytváøení anonymních objektù pro projekce (`new { x.Name, x.Age }`).  
4. **Delegáti**:  
   - Napø. `Func<T, bool>` pro definici podmínek.  

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

Oba pøístupy jsou ekvivalentní – kompilátor pøekládá Query Syntax na Method Syntax.

---

#### **Hlavní operátory LINQ**  

| Kategorie         | Pøíklady                                      | Popis                              |
|--------------------|----------------------------------------------|------------------------------------|
| **Filtrování**     | `Where()`, `OfType<T>()`                     | Výbìr prvkù podle podmínky.        |
| **Øazení**         | `OrderBy()`, `ThenBy()`, `Reverse()`         | Seøazení výsledkù.                 |
| **Seskupení**      | `GroupBy()`, `ToLookup()`                    | Seskupení podle klíèe.             |
| **Projekce**       | `Select()`, `SelectMany()`                   | Transformace výsledkù.             |
| **Spojení**        | `Join()`, `GroupJoin()`                      | Spojení dvou kolekcí.              |
| **Agregace**       | `Sum()`, `Average()`, `Count()`, `Max()`     | Výpoèty nad daty.                  |
| **Stránkování**    | `Skip()`, `Take()`                           | Výbìr podmnožiny prvkù.            |
| **Elementy**       | `First()`, `Single()`, `ElementAt()`         | Výbìr konkrétního prvku.           |
| **Konverze**       | `ToList()`, `ToArray()`, `ToDictionary()`    | Pøevod na konkrétní kolekci.       |

---

#### **LINQ Providers**  

- **LINQ to Objects**: Pro práci s kolekcemi v pamìti (pole, seznamy).  
- **LINQ to SQL/Entity Framework**: Pro dotazování relaèních databází.  
- **LINQ to XML**: Pro práci s XML dokumenty (`XDocument`, `XElement`).  
- **LINQ to JSON** (pomocí knihoven jako Newtonsoft.Json).  

---

#### **Deferred Execution (Odložené vykonání)**  

- Dotaz se **nevykoná okamžitì** pøi deklaraci, ale až pøi iteraci výsledku (napø. ve `foreach` nebo pøi volání `ToList()`).  
- Umožòuje optimalizaci a kombinování dotazù.  
- **Pøíklad**:  
  ```csharp
  var query = people.Where(p => p.Age > 18); // Dotaz se ještì nevykonal
  var results = query.ToList(); // Vyvolání dotazu
  ```

---

#### **Rozdíl mezi `IEnumerable` a `IQueryable`**  

- **`IEnumerable`**:  
  - Pro práci s lokálními daty (v pamìti).  
  - Vykonává se na stranì klienta.  
- **`IQueryable`**:  
  - Pro dotazy nad externími zdroji (napø. SQL).  
  - Generuje optimalizovaný SQL dotaz (napø. pro Entity Framework).  

---

#### **Pøíklad: LINQ s Entity Framework**  

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

- Jednotný zpùsob dotazování rùzných zdrojù.  
- Èitelnost a udržovatelnost kódu.  
- Kompilátor kontroluje chyby (na rozdíl od SQL v øetìzcích).  

#### **Nevýhody**  

- Nìkdy ménì efektivní než nativní SQL (nutnost optimalizace).  
- Složitìjší debugování.  

---

## **Metody**

### **Základní operace**
- **Select** – `Projekce prvkù`  
- **Where** – `Filtrace podle podmínky`  
- **OfType** – `Filtrace podle typu`  

### **Øazení a seskupování**
- **OrderBy** – `Øazené vzestupne`  
- **OrderByDescending** – `Øazení sestupnì`  
- **ThenBy** – `Sekundární øazení vzestupnì`  
- **ThenByDescending** – `Sekundární øazení sestupnì`  
- **GroupBy** – `Seskupení prvkù`  
- **ToLookup** – `Vytvoøení lookup tabulky`  

### **Spojování kolekcí**
- **Join** – `Relaèní spojení`  
- **GroupJoin** – `Seskupené spojení`  
- **Concat** – `Spojení kolekcí`  

### **Agregaèní funkce**
- **Sum** – `Souèet hodnot`  
- **Average** – `Prùmìrná hodnota`  
- **Count** – `Poèet prvkù`  
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

### **Podmínìné operace**
- **Any** – `Alespoò jeden splòuje podmínku`  
- **All** – `Všechny prvky splòují podmínku`  
- **Contains** – `Obsahuje prvek`  

### **Porovnání a manipulace**
- **SequenceEqual** – `Porovnání sekvencí`  
- **Distinct** – `Unikátní prvky`  
- **Except** – `Rozdíl kolekcí`  
- **Intersect** – `Prùnik kolekcí`  
- **Union** – `Sjednocení kolekcí`  

### **Práce s sekvencemi**
- **Skip** – `Pøeskoèení prvkù`  
- **SkipWhile** – `Pøeskoèení dokud podmínka platí`  
- **Take** – `Výbìr prvkù`  
- **TakeWhile** – `Výbìr dokud podmínka platí`  

### **Speciální kolekce**
- **Empty** – `Prázdná kolekce`  
- **Range** – `Sekvence èísel`  
- **Repeat** – `Opakující se prvek`  
- **DefaultIfEmpty** – `Výchozí hodnota pro prázdnou kolekci`  

---

### **Doporuèené zdroje**  

- Oficiální dokumentace: [Microsoft LINQ Guide](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/concepts/linq/)  
