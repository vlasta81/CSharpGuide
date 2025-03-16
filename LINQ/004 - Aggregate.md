
### **`Aggregate`**  

Metoda `Aggregate` slouží k **postupné aplikaci vlastní logiky na prvky kolekce** za účelem vytvoření jediného výsledku. Je univerzální náhradou za specializované metody jako `Sum()` nebo `Average()`, ale s plnou kontrolou nad procesem agregace.

---

#### **Základní charakteristika**  

- **Účel**: Kombinace prvků kolekce do jedné hodnoty pomocí uživatelské akumulační funkce.  
- **Návratový typ**: Závisí na typu seed a akumulační funkci (může být libovolný typ).  
- **Přetížení**:  
  1. **Bez seedu**: `TResult Aggregate<TSource>(Func<TSource, TSource, TSource> func)`  
     - Používá první prvek kolekce jako počáteční hodnotu.  
     - Vyvolá výjimku, pokud je kolekce prázdná.  
  2. **S seedem**: `TResult Aggregate<TAccumulate, TSource>(TAccumulate seed, Func<TAccumulate, TSource, TAccumulate> func)`  
     - Bezpečnější pro prázdné kolekce.  
  3. **S seedem a transformací výsledku**: `TResult Aggregate<TAccumulate, TResult>(TAccumulate seed, Func<TAccumulate, TSource, TAccumulate> func, Func<TAccumulate, TResult> resultSelector)`  
     - Umožňuje upravit finální agregovanou hodnotu.  

---

### **Syntaxe**  

```csharp
// Bez seedu:
var result = kolekce.Aggregate((acc, prvek) => operace);

// S seedem:
var result = kolekce.Aggregate(seed, (acc, prvek) => operace);

// S seedem a transformací:
var result = kolekce.Aggregate(seed, (acc, prvek) => operace, finalniTransformace);
```

---

### **Příklady použití**  

#### **1. Součet čísel (bez seedu)**  

```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4 };
int sum = numbers.Aggregate((acc, num) => acc + num); // 1 + 2 + 3 + 4 = 10
```

#### **2. Výpočet faktoriálu**  

```csharp
int n = 5;
var factorial = Enumerable.Range(1, n).Aggregate(1, (acc, x) => acc * x); // 120
```

#### **3. Spojení řetězců**  

```csharp
List<string> words = new List<string> { "Ahoj", "světe", "!" };
string sentence = words.Aggregate((acc, word) => acc + " " + word); // "Ahoj světe !"
```

#### **4. Složitější agregace s seedem (počítání sumy a počtu)**  

```csharp
List<int> data = new List<int> { 10, 20, 30 };
var stats = data.Aggregate(
    seed: new { Sum = 0, Count = 0 }, // anonymní typ jako seed
    func: (acc, num) => new { Sum = acc.Sum + num, Count = acc.Count + 1 }
);
Console.WriteLine($"Průměr: {stats.Sum / stats.Count}"); // 20
```

---

### **Klíčové vlastnosti** 

- **Akumulační funkce**:  
  - První parametr (`acc`) je aktuální mezivýsledek.  
  - Druhý parametr (`prvek`) je aktuální prvek kolekce.  
- **Seed**:  
  - Určuje počáteční hodnotu agregace (např. `0` pro sumu, `""` pro řetězce).  
  - Umožňuje práci s jiným typem než mají prvky kolekce (např. agregace čísel do řetězce).  
- **Prázdné kolekce**:  
  - Při použití seedu vrátí `Aggregate` hodnotu seedu.  
  - Bez seedu vyvolá `InvalidOperationException`.  

---

### **Srovnání s podobnými metodami**  

| Metoda       | Popis                                      | Příklad                      |
|--------------|--------------------------------------------|-----------------------------|
| **Aggregate**| Vlastní logika agregace.                   | `numbers.Aggregate((a, b) => a * b)` |
| **Sum**      | Specializovaná pro sčítání.                | `numbers.Sum()`             |
| **Average**  | Specializovaná pro průměr.                 | `numbers.Average()`         |

---

### **Časté chyby**  

1. **Prázdná kolekce bez seedu**:  
   ```csharp
   List<int> empty = new List<int>();
   int result = empty.Aggregate((a, b) => a + b); // Výjimka!
   ```

2. **Nesprávná inicializace seedu**:  
   ```csharp
   // Špatně: Seed musí být kompatibilní s typem výsledku.
   var invalid = numbers.Aggregate("", (acc, num) => acc + num); // Chyba: nelze převést int na string
   ```

3. **Záměna pořadí parametrů**:  
   ```csharp
   // Špatně: (prvek, acc) místo (acc, prvek)
   var wrongSum = numbers.Aggregate((num, acc) => num + acc); // Nesprávný výsledek!
   ```

---

### **Tipy pro efektivní použití**  

- Pro složité agregace (např. výpočet více statistik najednou) použijte **anonymní typy** nebo **třídy** jako seed.  
- Vyhněte se mutování externích proměnných uvnitř `Aggregate` – používejte čisté funkce.  
- Preferujte **seed** pro bezpečnou práci s prázdnými kolekcemi.  

---

### **Shrnutí**  

- `Aggregate` je **univerzální nástroj** pro vlastní agregaci dat.  
- Umožňuje definovat **počáteční hodnotu**, **akumulační logiku** a **transformaci výsledku**.  
- Vhodné pro scénáře, kde neexistuje specializovaná metoda (např. násobení, spojování řetězců, složité výpočty).  

📖 **Zdroj**: [Microsoft Docs – Aggregate](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.aggregate)
