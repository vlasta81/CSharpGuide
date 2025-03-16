
### **`SequenceEqual`**  

Metoda `SequenceEqual` slouží k **porovnání dvou sekvencí** a zjištění, zda jsou **identické v pořadí a hodnotách prvků**. Je užitečná pro ověření shody kolekcí, například po transformaci dat nebo při testování.

---

#### **Základní charakteristika**  

- **Účel**: Kontrola, zda dvě kolekce obsahují **stejné prvky ve stejném pořadí**.  
- **Návratový typ**: `bool` (`true` pro shodu, `false` jinak).  
- **Porovnání prvků**:  
  - Pro hodnotové typy: porovnání podle hodnoty.  
  - Pro referenční typy: výchozí porovnání podle **reference** (pokud není použit vlastní `IEqualityComparer<T>`).  
- **Citlivost na pořadí**: Pokud se liší pořadí prvků, vrací `false`.  

---

### **Syntaxe**  

```csharp
bool jsouShodné = kolekce1.SequenceEqual(kolekce2);

// S vlastním porovnávačem:
bool jsouShodné = kolekce1.SequenceEqual(kolekce2, new MujComparer());
```

---

### **Klíčové vlastnosti**  

1. **Podpora vlastního porovnávače**:  
   ```csharp
   class CaseInsensitiveComparer : IEqualityComparer<string>
   {
       public bool Equals(string x, string y) => 
           string.Equals(x, y, StringComparison.OrdinalIgnoreCase);
       
       public int GetHashCode(string obj) => 
           obj.ToUpper().GetHashCode();
   }

   List<string> words1 = new List<string> { "Ahoj", "Světe" };
   List<string> words2 = new List<string> { "ahoj", "světe" };
   bool shoda = words1.SequenceEqual(words2, new CaseInsensitiveComparer()); // true
   ```

2. **Reakce na délku kolekcí**:  
   - Pokud jsou kolekce **různě dlouhé**, vrátí `false` bez porovnávání prvků.  

3. **Optimalizace pro stejné reference**:  
   - Pokud obě kolekce odkazují na stejnou instanci, vrátí `true`.  

---

### **Příklady použití**  

#### **1. Porovnání jednoduchých kolekcí**  

```csharp
int[] čísla1 = { 1, 2, 3 };
int[] čísla2 = { 1, 2, 3 };
bool shoda = čísla1.SequenceEqual(čísla2); // true

List<string> slova1 = new List<string> { "a", "b" };
List<string> slova2 = new List<string> { "a", "c" };
bool shoda = slova1.SequenceEqual(slova2); // false
```

#### **2. Porovnání objektů s vlastním porovnávačem**  

```csharp
class Person 
{ 
    public int Id { get; set; }
    public string Name { get; set; }
}

class PersonIdComparer : IEqualityComparer<Person>
{
    public bool Equals(Person x, Person y) => x.Id == y.Id;
    public int GetHashCode(Person p) => p.Id.GetHashCode();
}

List<Person> lidé1 = new List<Person> { new Person { Id = 1, Name = "Alice" } };
List<Person> lidé2 = new List<Person> { new Person { Id = 1, Name = "Bob" } };
bool shoda = lidé1.SequenceEqual(lidé2, new PersonIdComparer()); // true (stejné Id)
```

#### **3. Porovnání různých typů kolekcí**  

```csharp
List<int> list = new List<int> { 1, 2, 3 };
int[] pole = { 1, 2, 3 };
bool shoda = list.SequenceEqual(pole); // true (stejné prvky a pořadí)
```

---

### **Časté chyby a upozornění**  

1. **Ignorování pořadí prvků**:  
   ```csharp
   int[] čísla1 = { 1, 2 };
   int[] čísla2 = { 2, 1 };
   bool shoda = čísla1.SequenceEqual(čísla2); // false
   ```

2. **Neošetřené `null` kolekce**:  
   ```csharp
   List<int> nullKolekce = null;
   int[] pole = { 1, 2 };
   bool shoda = nullKolekce.SequenceEqual(pole); // Vyvolá ArgumentNullException!
   ```

3. **Porovnání referenčních typů bez vlastního porovnávače**:  
   ```csharp
   List<Person> lidé1 = new List<Person> { new Person { Id = 1 } };
   List<Person> lidé2 = new List<Person> { new Person { Id = 1 } };
   bool shoda = lidé1.SequenceEqual(lidé2); // false (porovnává reference!)
   ```

---

### **Tipy pro efektivní použití**  

- **Před použitím seřaďte kolekce**, pokud nezáleží na pořadí:  
  ```csharp
  bool shoda = kolekce1.OrderBy(x => x).SequenceEqual(kolekce2.OrderBy(x => x));
  ```
- **Pro složité objekty vždy definujte `IEqualityComparer`**.  
- **Kontrolujte délku kolekcí předem** pro optimalizaci:  
  ```csharp
  if (kolekce1.Count() != kolekce2.Count()) return false;
  return kolekce1.SequenceEqual(kolekce2);
  ```

---

### **Shrnutí**  

- `SequenceEqual` kontroluje **shodu prvků a jejich pořadí** ve dvou kolekcích.  
- Pro referenční typy vyžaduje **vlastní porovnávač** k porovnání podle hodnot.  
- Efektivně funguje s různými typy kolekcí (např. `List<T>` vs. `T[]`).  

📖 **Zdroj**: [Microsoft Docs – SequenceEqual](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.sequenceequal)
