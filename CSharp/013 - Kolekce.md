
### **1. Úvod do kolekcí**

- **Kolekce** slouží k ukládání a správě skupin objektů.
- **Výhody oproti polím**: Dynamická velikost, pokročilé operace (řazení, filtrace), specializované struktury.
- **Namespace**: `System.Collections`, `System.Collections.Generic`, `System.Collections.Concurrent`.

---

### **2. Typy kolekcí**

#### **a) Generické kolekce (`System.Collections.Generic`)**  

- **Typově bezpečné** (používají generické parametry).
- **Hlavní typy**:
  - **`List<T>`**: Dynamické pole (náhodný přístup, přidávání/odebírání prvků).
    ```csharp
    List<int> cisla = new List<int> { 1, 2, 3 };
    cisla.Add(4); // [1, 2, 3, 4]
    ```
  
  - **`Dictionary<TKey, TValue>`**: Slovník (klíč-hodnota).
    ```csharp
    Dictionary<string, int> slovnik = new Dictionary<string, int>();
    slovnik.Add("jedna", 1);
    int hodnota = slovnik["jedna"]; // 1
    ```
  
  - **`Queue<T>`**: Fronta (FIFO – první dovnitř, první ven).
    ```csharp
    Queue<string> fronta = new Queue<string>();
    fronta.Enqueue("prvni");
    string prvek = fronta.Dequeue(); // "prvni"
    ```
  
  - **`Stack<T>`**: Zásobník (LIFO – poslední dovnitř, první ven).
    ```csharp
    Stack<int> zasobnik = new Stack<int>();
    zasobnik.Push(1);
    int vrchol = zasobnik.Pop(); // 1
    ```
  
  - **`HashSet<T>`**: Množina unikátních prvků (rychlá kontrola existence).
    ```csharp
    HashSet<int> mnozina = new HashSet<int> { 1, 2, 3 };
    bool existuje = mnozina.Contains(2); // true
    ```

#### **b) Negenerické kolekce (`System.Collections`)**  

- **Typově nebezpečné** (ukládají `object`). **Používejte jen pro starší kód**.
- **Hlavní typy**:
  - **`ArrayList`**: Obdoba `List<T>` pro libovolné typy.
  - **`Hashtable`**: Obdoba `Dictionary<TKey, TValue>`.
  - **`Queue`/`Stack`**: Fronta/zásobník pro `object`.

#### **c) Konkurentní kolekce (`System.Collections.Concurrent`)**  

- **Thread-safe** (bezpečné pro paralelní přístup z více vláken).
- **Hlavní typy**:
  - **`ConcurrentBag<T>`**: Neseřazená kolekce.
  - **`ConcurrentDictionary<TKey, TValue>`**: Thread-safe slovník.
  - **`BlockingCollection<T>`**: Kolekce pro producent-konzument scénáře.

#### **d) Immutable kolekce (`System.Collections.Immutable`)**  

- **Neměnné** (každá operace vrátí novou kolekci). Vhodné pro funkcionální přístup.
- **Hlavní typy**:
  - `ImmutableList<T>`, `ImmutableDictionary<TKey, TValue>`, atd.

---

### **3. Běžné operace a metody**

- **Přidávání/odebírání**:
  - `Add()`, `Remove()`, `Clear()`.
- **Vyhledávání**:
  - `Contains()`, `IndexOf()`.
- **LINQ metody**:
  ```csharp
  var sudaCisla = cisla.Where(x => x % 2 == 0).ToList();
  var prvni = cisla.FirstOrDefault();
  ```

---

### **4. Porovnání kolekcí**

| **Kolekce**         | **Použití**                          | **Výhody**                      | **Nevýhody**               |
|----------------------|--------------------------------------|----------------------------------|----------------------------|
| `List<T>`            | Dynamické pole                      | Rychlý náhodný přístup          | Pomalé vkládání uprostřed  |
| `Dictionary<TKey,T>` | Vyhledávání podle klíče             | Rychlý přístup (O(1))           | Neuchovává pořadí          |
| `HashSet<T>`         | Unikátní prvky                      | Rychlé kontroly existence       | Neuspořádané               |
| `Queue<T>`           | Fronta (FIFO)                       | Efektivní pro frontové zpracování | Omezené operace           |
| `LinkedList<T>`      | Časté vkládání/odstraňování uprostřed | Rychlé operace uzlů            | Pomalý náhodný přístup     |

---

### **5. Časté chyby a tipy**

- **Negenerické kolekce**: Vyhýbejte se jim (boxing/unboxing, chyby typu).
- **Thread-safety**: Kromě `Concurrent` kolekcí nejsou standardní kolekce thread-safe.
- **Kapacita kolekcí**: Inicializujte s odhadnutou kapacitou pro optimalizaci (např. `new List<T>(100)`).
- **Volba správné kolekce**:
  - Potřebujete klíče? → `Dictionary<TKey, TValue>`.
  - Unikátní prvky? → `HashSet<T>`.
  - Paralelní přístup? → `Concurrent kolekce`.

---

### **6. Ukázky kódu**

#### **Práce se slovníkem**

```csharp
var lidi = new Dictionary<int, string>();
lidi.Add(1, "Anna");
lidi[2] = "Karel"; // Alternativní zápis

foreach (var pair in lidi) {
    Console.WriteLine($"{pair.Key}: {pair.Value}");
}
```

#### **Použití LINQ s `List<T>`**

```csharp
List<int> cisla = new List<int> { 5, 2, 8, 1 };
var serazena = cisla.OrderBy(x => x).ToList(); // [1, 2, 5, 8]
```

#### **Thread-safe přístup s `ConcurrentDictionary`**

```csharp
var concurrentDict = new ConcurrentDictionary<string, int>();
concurrentDict.TryAdd("pokus", 10);
int hodnota = concurrentDict["pokus"]; // 10
```

---

### **7. Doporučené zdroje**

- **Oficiální dokumentace**: [Collections in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/concepts/collections)
