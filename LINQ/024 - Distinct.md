
### **`Distinct`**  

Metoda `Distinct` slouží k **odstranění duplicitních prvků** z kolekce. Vrací sekvenci obsahující pouze **unikátní prvky** podle definovaného kritéria porovnání. Je užitečná pro čištění dat, agregace nebo práci s unikátními hodnotami.

---

#### **Základní charakteristika**  

- **Účel**: Filtrovat duplicity a vrátit pouze jedinečné prvky.  
- **Návratový typ**: `IEnumerable<T>` nebo `IQueryable<T>` (pro externí zdroje dat).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  
- **Porovnávání**:  
  - **Výchozí chování**: Používá `EqualityComparer<T>.Default` (pro hodnotové typy porovnává hodnoty, pro referenční typy reference).  
  - **Vlastní porovnávač**: Lze definovat pomocí `IEqualityComparer<T>`.  

---

### **Syntaxe**  

```csharp
// S výchozím porovnávačem:
IEnumerable<T> unikátníPrvky = kolekce.Distinct();

// S vlastním porovnávačem:
IEnumerable<T> unikátníPrvky = kolekce.Distinct(new MujPorovnávač());
```

---

### **Klíčové vlastnosti**  

1. **Výchozí porovnávání**:  
   - Pro objekty (referenční typy) je nutné přepsat `Equals` a `GetHashCode`, nebo použít vlastní porovnávač.  
   ```csharp
   class Person { public string Name { get; set; } }

   List<Person> people = new List<Person> { new Person { Name = "Alice" }, new Person { Name = "Alice" } };
   var distinctPeople = people.Distinct(); // Vrátí 2 prvky (pokud není přepsán Equals)!
   ```

2. **Vlastní porovnávač**:  
   ```csharp
   class NameComparer : IEqualityComparer<Person>
   {
       public bool Equals(Person x, Person y) => x.Name == y.Name;
       public int GetHashCode(Person p) => p.Name.GetHashCode();
   }

   var distinctNames = people.Distinct(new NameComparer()); // 1 prvek ("Alice")
   ```

3. **DistinctBy (C# 9+ – LINQ v .NET 6+)**:  
   - Filtruje duplicity podle konkrétní vlastnosti bez nutnosti porovnávače.  
   ```csharp
   var distinctPeople = people.DistinctBy(p => p.Name);
   ```

---

### **Příklady použití**  

#### **1. Odstranění duplicitních čísel**  

```csharp
List<int> čísla = new List<int> { 1, 2, 2, 3, 3, 3 };
var unikátní = čísla.Distinct(); // {1, 2, 3}
```

#### **2. Unikátní řetězce (case-insensitive)**  

```csharp
List<string> slova = new List<string> { "Ahoj", "ahoj", "Světe" };
var unikátní = slova.Distinct(StringComparer.OrdinalIgnoreCase); // {"Ahoj", "Světe"}
```

#### **3. Unikátní objekty podle vlastnosti**  

```csharp
// Bez DistinctBy (C# 9+):
var distinctPeople = people
    .GroupBy(p => p.Name)
    .Select(g => g.First());
```

---

### **Časté chyby a upozornění**  

1. **Ignorování vlastního porovnávače u referenčních typů**:  
   ```csharp
   // Vrátí duplicity, pokud Person nepřepisuje Equals:
   var result = people.Distinct();
   ```

2. **Nejednoznačné porovnávání u anonymních typů**:  
   ```csharp
   // Funguje, protože anonymní typy přepisují Equals:
   var data = people.Select(p => new { p.Name }).Distinct();
   ```

3. **Duplicitní `null` hodnoty**:  
   - `Distinct` zachovává pouze jeden `null` (pokud je v kolekci).  

---

### **Tipy pro efektivní použití**  

- **Pro EF Core**:  
  - Metoda `Distinct` se převádí na SQL `DISTINCT` (optimalizace na straně databáze).  
  ```csharp
  var uniqueNames = dbContext.People.Select(p => p.Name).Distinct().ToList();
  ```
- **Kombinace s `Where`**:  
  ```csharp
  var unikátníAktivní = people.Where(p => p.IsActive).Distinct();
  ```
- **Využití `HashSet<T>` pro okamžité odstranění duplicit**:  
  ```csharp
  var hashSet = new HashSet<Person>(people, new NameComparer()); // Okamžitá deduplikace
  ```

---

### **Shrnutí**  

- `Distinct` odstraňuje duplicity na základě **porovnávání hodnot nebo referencí**.  
- Pro referenční typy je nutné řešit porovnávání (výchozí vs. vlastní logika).  
- Efektivně spolupracuje s **EF Core** (převod na SQL `DISTINCT`).  
- V novějších verzích C# lze použít `DistinctBy` pro zjednodušení práce s vlastnostmi.  

📖 **Zdroj**: [Microsoft Docs – Distinct](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.distinct)
