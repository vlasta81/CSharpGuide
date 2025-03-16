
### **`Count`**  

Metoda `Count` slouží k **zjištění počtu prvků v kolekci** nebo počtu prvků splňujících určitou podmínku. Je jednou ze základních agregací v LINQ a používá se pro analýzu datových sad.

---

#### **Základní charakteristika**  

- **Účel**: Spočítat prvky v kolekci nebo prvky splňující podmínku.  
- **Návratový typ**: `int` (nebo `long` pro `LongCount`).  
- **Přetížení**:  
  1. `Count()` – vrátí celkový počet prvků.  
  2. `Count(Func<TSource, bool> predicate)` – vrátí počet prvků splňujících podmínku.  
- **Prázdná kolekce**: Vrací `0`.  

---

### **Syntaxe**  

```csharp
// Celkový počet prvků:
int total = kolekce.Count();

// Počet prvků splňujících podmínku:
int filteredCount = kolekce.Count(x => x.Vlastnost > 10);
```

---

### **Klíčové vlastnosti**  

1. **Okamžité vyhodnocení**: Metoda `Count` **nevyužívá deferred execution** – dotaz se vykoná ihned.  
2. **Optimalizace v LINQ to Entities**:  
   - V dotazech Entity Framework se `Count` převádí na SQL příkaz `COUNT`.  
   ```csharp
   var count = dbContext.Products.Count(p => p.Price > 100); // Generuje SQL COUNT
   ```
3. **Rozdíl oproti `Count` vlastnosti**:  
   - `Count()` je metoda LINQ pracující s `IEnumerable<T>`.  
   - `Count` vlastnost (např. `List<T>.Count`) je přímo součástí kolekce a je optimalizovaná pro rychlý přístup.  

---

### **Příklady použití**  

#### **1. Základní použití**  

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5 };
int total = numbers.Count(); // 5
```

#### **2. Počet prvků s podmínkou**  

```csharp
List<string> words = new List<string> { "apple", "banana", "cherry" };
int countA = words.Count(w => w.StartsWith("a")); // 1
```

#### **3. Práce s objekty**  

```csharp
class Person { public int Age { get; set; } }
List<Person> people = new List<Person> { new Person { Age = 25 }, new Person { Age = 30 } };
int adults = people.Count(p => p.Age >= 18); // 2
```

---

### **Časté chyby a upozornění**  

1. **Záměna s `Any()`**:  
   - `Count() > 0` pro kontrolu existence prvků je **méně efektivní** než `Any()`.  
   ```csharp
   // Špatně: Projde celou kolekci
   if (kolekce.Count() > 0) { ... }

   // Správně: Zastaví se u prvního prvku
   if (kolekce.Any()) { ... }
   ```

2. **Neplatné použití pro nekonečné sekvence**:  
   ```csharp
   // Vyvolá výjimku OverflowException u velmi dlouhých kolekcí:
   var infiniteNumbers = Enumerable.Range(1, int.MaxValue).Concat(Enumerable.Repeat(0, int.MaxValue));
   long total = infiniteNumbers.LongCount(); // Bezpečnější pro velká čísla
   ```

3. **`Null` reference**:  
   ```csharp
   List<Person> people = null;
   int count = people.Count(); // Vyvolá ArgumentNullException
   ```

---

### **Tipy pro efektivní použití**  

- Pro kontrolu existence prvků **vždy preferujte `Any()`** před `Count() > 0`.  
- U kolekcí s vlastností `Count` (např. `List<T>`) použijte přímo tuto vlastnost (je rychlejší).  
- Pro velmi velké kolekce použijte `LongCount()`, pokud hrozí překročení `int.MaxValue`.  

---

### **Shrnutí**  

- `Count` slouží k **spočítání prvků** nebo **prvků splňujících podmínku**.  
- Vrací `0` pro prázdné kolekce a okamžitě vyhodnocuje dotaz.  
- Efektivně spolupracuje s databázemi přes LINQ to Entities.  
- Rozlišujte mezi `Count()` (LINQ) a `Count` vlastností (optimalizovaná pro konkrétní kolekce).  

📖 **Zdroj**: [Microsoft Docs – Count](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.count)
