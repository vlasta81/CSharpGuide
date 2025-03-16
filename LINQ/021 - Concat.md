
### **`Concat`**  

Metoda `Concat` slouží ke **kombinaci dvou sekvencí** do jedné, přičemž zachovává **všechny prvky v původním pořadí**. Na rozdíl od `Union` neodstraňuje duplicity a pouze připojuje druhou kolekci za první. Je užitečná pro spojování dat z různých zdrojů bez potřeby filtrace.

---

#### **Základní charakteristika**  

- **Účel**: Spojení dvou kolekcí do jedné.  
- **Návratový typ**: `IEnumerable<T>` (nová sekvence obsahující prvky obou kolekcí).  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  
- **Duplicity**: **Neodstraňuje duplicitní prvky** – všechny prvky jsou zachovány.  
- **Požadavek**: Obě kolekce musí být stejného typu `T` (nebo kompatibilní po převodu).  

---

### **Syntaxe**  

```csharp
IEnumerable<T> kombinace = kolekce1.Concat(kolekce2);
```

---

### **Klíčové vlastnosti**  

1. **Zachování pořadí**:  
   - Prvky první kolekce jsou následovány prvky druhé kolekce v původním pořadí.  
   ```csharp
   List<int> čísla1 = new List<int> { 1, 2 };
   List<int> čísla2 = new List<int> { 3, 4 };
   var spojeno = čísla1.Concat(čísla2); // { 1, 2, 3, 4 }
   ```

2. **Práce s různými typy kolekcí**:  
   - Funguje mezi `List<T>`, `T[]`, `IEnumerable<T>` atd.  
   ```csharp
   int[] pole = { 5, 6 };
   var spojeno = čísla1.Concat(pole); // { 1, 2, 5, 6 }
   ```

3. **Kombinace s dalšími operátory**:  
   ```csharp
   var výsledek = kolekce1.Where(x => x > 0)
                         .Concat(kolekce2.OrderBy(x => x));
   ```

---

### **Příklady použití**  

#### **1. Spojení dvou kolekcí čísel**  

```csharp
List<int> list1 = new List<int> { 1, 2, 3 };
List<int> list2 = new List<int> { 3, 4 };
IEnumerable<int> spojeno = list1.Concat(list2); // { 1, 2, 3, 3, 4 }
```

#### **2. Spojení řetězců z různých zdrojů**  

```csharp
string[] jména1 = { "Alice", "Bob" };
List<string> jména2 = new List<string> { "Charlie", "David" };
var všechnaJména = jména1.Concat(jména2); // "Alice", "Bob", "Charlie", "David"
```

#### **3. Spojení objektů s konverzí typu** 

```csharp
List<int> čísla = new List<int> { 1, 2 };
List<string> texty = new List<string> { "A", "B" };

// Převod na společný typ (např. object):
var kombinace = čísla.Cast<object>().Concat(texty.Cast<object>());
```

---

### **Časté chyby a upozornění**  

1. **Neošetřené `null` kolekce**:  
   ```csharp
   List<int> nullKolekce = null;
   var výsledek = kolekce1.Concat(nullKolekce); // ArgumentNullException!
   ```

2. **Nekompatibilní typy**:  
   ```csharp
   List<int> čísla = new List<int> { 1, 2 };
   List<string> texty = new List<string> { "A", "B" };
   var chyba = čísla.Concat(texty); // Chyba kompilace!
   ```

3. **Záměna s `Union`**:  
   ```csharp
   var sDuplicitami = list1.Concat(list2); // { 1, 2, 3, 3, 4 }
   var bezDuplicit = list1.Union(list2);   // { 1, 2, 3, 4 }
   ```

---

### **Rozdíly oproti podobným metodám**  

| Metoda       | Popis                                      | Duplicity | Pořadí           |
|--------------|--------------------------------------------|-----------|------------------|
| **Concat**   | Spojí dvě kolekce.                         | Zachovává | Zachovává        |
| **Union**    | Spojí kolekce a odstraní duplicity.        | Odstraní  | Nezaručeno       |
| **AddRange** | Modifikuje původní kolekci (není LINQ).    | Zachovává | Zachovává        |

---

### **Tipy pro efektivní použití**  

- **Materializace výsledku**:  
  ```csharp
  List<int> výsledek = kolekce1.Concat(kolekce2).ToList(); // Uloží do seznamu.
  ```
- **Kombinace s `Select` pro konverzi typů**:  
  ```csharp
  var kombinace = čísla.Select(n => n.ToString()).Concat(texty);
  ```
- **Ošetření prázdných kolekcí**:  
  ```csharp
  var bezpečné = (kolekce1 ?? Enumerable.Empty<int>()).Concat(kolekce2);
  ```

---

### **Shrnutí**  

- `Concat` slouží k **spojení dvou kolekcí** do jedné s zachováním všech prvků a pořadí.  
- Neodstraňuje duplicity a vyžaduje kompatibilní typy.  
- Vhodné pro **kombinaci dat z různých zdrojů** bez potřeby filtrace.  

📖 **Zdroj**: [Microsoft Docs – Concat](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.concat)
