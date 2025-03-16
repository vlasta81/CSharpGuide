
### **`ElementAt` a `ElementAtOrDefault`**  
Tyto metody slouží k **přístupu k prvkům kolekce na základě jejich pozice**. Jsou užitečné pro přímý přístup k prvkům, kdy známe konkrétní index, ale liší se v chování při neplatném indexu.

---

#### **Základní charakteristika**  

| Vlastnost               | **`ElementAt`**                        | **`ElementAtOrDefault`**               |
|-------------------------|----------------------------------------|----------------------------------------|
| **Účel**                | Vrátí prvek na zadaném indexu.         | Vrátí prvek na zadaném indexu nebo **výchozí hodnotu**, pokud je index neplatný. |
| **Výjimka**             | Vyvolá `ArgumentOutOfRangeException`, pokud je index mimo rozsah. | **Nevyvolá výjimku** – vrátí `default(T)`. |
| **Vhodné pro**          | Když je index **garantovaně platný**.  | Když není jistota, zda index existuje. |

---

### **Syntaxe**  

```csharp
// ElementAt:
T item = kolekce.ElementAt(index);

// ElementAtOrDefault:
T item = kolekce.ElementAtOrDefault(index);
```

---

### **Klíčové vlastnosti**  

1. **Podporované kolekce**:  
   - Funguje pro všechny `IEnumerable<T>` (pole, seznamy, LINQ to Entities atd.).  
   - Pro neindexovatelné sekvence (např. `yield return`) iteruje postupně až k požadovanému indexu – **O(n) složitost**.  

2. **Výchozí hodnoty**:  
   - Pro **referenční typy** (např. `string`, třídy): `default` je `null`.  
   - Pro **hodnotové typy** (např. `int`, `DateTime`): `default` je `0`, `false`, nebo `struct` s vynulovanými poli.  
   - Pro **nullable typy** (např. `int?`): `default` je `null`.  

3. **EF Core a SQL**:  
   - V dotazech na databázi se `ElementAt`/`ElementAtOrDefault` převádí na `OFFSET ... FETCH` (pouze pokud kolekce podporuje indexaci).  

---

### **Příklady použití**  

#### **1. Platný index**  

```csharp
List<string> fruits = new List<string> { "Apple", "Banana", "Cherry" };

string fruit1 = fruits.ElementAt(1);        // "Banana"
string fruit1Safe = fruits.ElementAtOrDefault(1); // "Banana"
```

#### **2. Neplatný index**  

```csharp
string invalid = fruits.ElementAt(5);       // Vyvolá výjimku!
string invalidSafe = fruits.ElementAtOrDefault(5); // null (pro List<string>)
```

#### **3. Práce s hodnotovými typy**  

```csharp
int[] numbers = { 10, 20, 30 };

int num = numbers.ElementAtOrDefault(3);    // 0 (výchozí hodnota pro int)
DateTime? date = dates.ElementAtOrDefault(10); // null (pro DateTime?)
```

---

### **Časté chyby a upozornění**  

1. **Záměna s indexérem kolekce**:  
   ```csharp
   // Pro List<T> je efektivnější použít přímo indexér:
   string item = fruits[1]; // O(1) pro List<T>
   string itemLinq = fruits.ElementAt(1); // O(n) pro obecné IEnumerable<T>!
   ```

2. **Neefektivita u sekvencí bez přímého indexování**:  
   - Pro `IEnumerable<T>` (např. výsledek LINQ dotazu) může `ElementAt` iterovat postupně.  

3. **Neočekávané chování u prázdných kolekcí**:  
   ```csharp
   var empty = Enumerable.Empty<int>();
   int value = empty.ElementAtOrDefault(0); // 0
   ```

---

### **Tipy pro efektivní použití**  

- **Kontrola existence indexu**:  
  ```csharp
  if (index >= 0 && index < kolekce.Count()) 
  {
      var item = kolekce.ElementAt(index);
  }
  ```
- **Použití s `Skip` a `Take` pro stránkování**:  
  ```csharp
  var page = kolekce.Skip(pageIndex * pageSize).Take(pageSize).ToList();
  ```
- **Optimalizace pro databáze**:  
  ```csharp
  // EF Core převede na SQL OFFSET/FETCH:
  var user = dbContext.Users.OrderBy(u => u.Id).ElementAt(100);
  ```

---

### **Shrnutí**  

- **`ElementAt`**: Pro přístup k prvkům, kdy je index **garantovaně platný** (jinak vyvolá výjimku).  
- **`ElementAtOrDefault`**: Pro **bezpečný přístup** s výchozí hodnotou při neplatném indexu.  
- Efektivita závisí na typu kolekce – preferujte indexéry u `List<T>` nebo `Array`.  

📖 **Zdroj**: [Microsoft Docs – ElementAt](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.elementat) | [ElementAtOrDefault](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.elementatordefault)
