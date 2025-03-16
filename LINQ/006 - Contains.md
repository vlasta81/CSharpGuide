
### **`Contains`** 

`Contains` je metoda rozšíření v LINQ, která **kontroluje, zda kolekce obsahuje konkrétní prvek**. Je užitečná pro rychlou kontrolu existence hodnoty bez nutnosti explicitního cyklu.

---

#### **Základní charakteristika**  

- **Účel**: Zjistit, zda kolekce obsahuje zadaný prvek.  
- **Návratový typ**: `bool`.  
- **Přetížení**:  
  1. `Contains(T hodnota)` – používá výchozí porovnávač rovnosti (`EqualityComparer<T>.Default`).  
  2. `Contains(T hodnota, IEqualityComparer<T> comparer)` – umožňuje definovat vlastní logiku porovnání.  

---

### **Syntaxe**  

```csharp
// S výchozím porovnávačem:
bool result = kolekce.Contains(hodnota);

// S vlastním porovnávačem:
bool result = kolekce.Contains(hodnota, new MujComparer());
```

---

### **Klíčové vlastnosti**  

1. **Porovnání hodnot**:  
   - Pro primitivní typy (`int`, `string` apod.) funguje intuitivně.  
   - Pro objekty **porovnává reference** (pokud není použit vlastní `IEqualityComparer`).  
   - Lze změnit chování přepsáním `Equals()` a `GetHashCode()` v třídě prvku.  

2. **Optimalizace u LINQ to Entities**:  
   - V dotazech Entity Framework se `Contains` převádí na SQL operátor `IN`.  
   ```csharp
   // Generuje SQL: WHERE p.Id IN (1, 2, 3)
   var ids = new List<int> { 1, 2, 3 };
   var products = dbContext.Products.Where(p => ids.Contains(p.Id)).ToList();
   ```

---

### **Příklady použití**  

#### **1. Primitivní typy**  

```csharp
List<int> numbers = new List<int> { 10, 20, 30 };
bool containsTwenty = numbers.Contains(20); // true
```

#### **2. Objekty s výchozím porovnávačem**  

```csharp
class Person { public string Name { get; set; } }

var people = new List<Person> { new Person { Name = "Alice" } };
var alice = new Person { Name = "Alice" };

bool exists = people.Contains(alice); // false (porovnává se reference!)
```

#### **3. Vlastní porovnávač**  

```csharp
class PersonComparer : IEqualityComparer<Person>
{
    public bool Equals(Person x, Person y) => x.Name == y.Name;
    public int GetHashCode(Person p) => p.Name.GetHashCode();
}

bool existsCustom = people.Contains(alice, new PersonComparer()); // true
```

#### **4. Case-insensitive porovnání řetězců**  

```csharp
List<string> fruits = new List<string> { "Apple", "Banana" };
bool hasApple = fruits.Contains("apple", StringComparer.OrdinalIgnoreCase); // true
```

---

### **Rozdíl mezi `Contains` a `Any`**  

- **`Contains`**:  
  - Kontroluje existenci **konkrétní hodnoty**.  
  - Pro objekty vyžaduje definici rovnosti.  
- **`Any`**:  
  - Kontroluje existenci prvku splňujícího **podmínku** (lambda výraz).  
  ```csharp
  // Ekvivalent Contains pomocí Any:
  bool exists = people.Any(p => p.Name == "Alice");
  ```

---

### **Výkonnostní tipy**  

- **Kolekce s rychlým vyhledáváním**:  
  - Pro časté kontroly použijte `HashSet<T>` nebo `Dictionary<TKey, TValue>`, které mají `O(1)` složitost pro `Contains`.  
- **LINQ to Entities**:  
  - `Contains` je efektivní, protože se převádí na SQL `IN` (nevyžaduje načtení všech dat do paměti).  

---

### **Časté chyby**  

1. **Porovnávání objektů bez vlastního porovnávače**:  
   ```csharp
   var list = new List<Person> { new Person("Alice") };
   bool exists = list.Contains(new Person("Alice")); // false (pokud není přepsán Equals)
   ```

2. **Záměna s `string.Contains`**:  
   ```csharp
   List<string> texts = new List<string> { "Hello world" };
   bool containsHello = texts.Any(s => s.Contains("Hello")); // true
   // LINQ Contains by vrátilo false, protože hledá celý řetězec "Hello"!
   ```

---

### **Shrnutí**  

- `Contains` slouží k **rychlému ověření existence prvku** v kolekci.  
- Pro objekty je nutné řešit porovnávání (výchozí vs. vlastní logika).  
- V EF optimalizuje dotazy pomocí SQL operátoru `IN`.  
- Pro složitější podmínky použijte `Any`.  

📖 **Zdroj**: [Microsoft Docs – Contains](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.contains)
