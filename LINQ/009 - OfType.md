
### **`OfType`**  

Metoda `OfType` slouží k **filtrování prvků kolekce na základě jejich typu**. Je užitečná pro práci s heterogenními kolekcemi (např. `ArrayList`, `object[]`) nebo při extrakci prvků implementujících konkrétní rozhraní. Na rozdíl od `Cast` bezpečně ignoruje prvky nekompatibilní s cílovým typem.

---

#### **Základní charakteristika**  

- **Účel**: Vybrat prvky, které jsou kompatibilní se zadaným typem `TResult` (včetně poděděných tříd).  
- **Návratový typ**: `IEnumerable<TResult>`.  
- **Deferred Execution**: Dotaz se vykoná až při iteraci výsledku.  
- **Bezpečnost**: Nevyvolá výjimku pro nekompatibilní prvky – ty jsou automaticky přeskočeny.  
- **Podpora**: Funguje s `IEnumerable` (včetně ne-generických kolekcí jako `ArrayList`).  

---

### **Syntaxe**  

```csharp
IEnumerable<TResult> result = kolekce.OfType<TResult>();
```

---

### **Klíčové vlastnosti**  

1. **Filtrace podle typu**:  
   - Vrací prvky, které jsou **instancemi `TResult`** nebo **jeho podtříd**.  
   - Pro hodnotové typy (`int`, `double` apod.) filtruje přesně daný typ (např. `OfType<int>` nevrátí `long`).  
   ```csharp
   ArrayList mixedList = new ArrayList { 1, "text", 3.14, DateTime.Now };
   var integers = mixedList.OfType<int>(); // { 1 }
   ```

2. **Práce s rozhraními**:  
   ```csharp
   List<object> objects = new List<object> { new List<int>(), "hello", new Dictionary<int, string>() };
   var collections = objects.OfType<ICollection>(); // List<int> a Dictionary<...>
   ```

3. **Ošetření `null` hodnot**:  
   - Pokud je `TResult` ne-nullable typ, `OfType` přeskočí `null` hodnoty.  
   ```csharp
   object[] items = { null, "abc", 10, null };
   var strings = items.OfType<string>(); // { "abc" }
   ```

---

### **Příklady použití**  

#### **1. Filtrace řetězců z `ArrayList`**  

```csharp
ArrayList list = new ArrayList { 10, "apple", true, "banana", 20 };
IEnumerable<string> strings = list.OfType<string>(); // "apple", "banana"
```

#### **2. Extrakce podtříd z kolekce**  

```csharp
class Animal { }
class Dog : Animal { }
class Cat : Animal { }

List<Animal> animals = new List<Animal> { new Dog(), new Cat(), new Dog() };
IEnumerable<Dog> dogs = animals.OfType<Dog>(); // Všechny instance Dog
```

#### **3. Práce s hodnotovými typy**  

```csharp
object[] values = { 5, 5.5, "6", 7.0m };
var decimals = values.OfType<decimal>(); // 7.0m
```

---

### **Rozdíl mezi `OfType` a `Cast`**  

| Vlastnost               | `OfType`                               | `Cast`                               |
|-------------------------|----------------------------------------|--------------------------------------|
| **Chování při chybě**   | Přeskočí nekompatibilní prvky.         | Vyvolá `InvalidCastException`.       |
| **Prázdná kolekce**     | Vrátí prázdnou kolekci.                | Vrátí prázdnou kolekci.              |
| **Použití**             | Heterogenní kolekce, bezpečná filtrace. | Kolekce s garantovanou kompatibilitou. |

---

### **Časté chyby**  

1. **Záměna s `Cast`**:  
   ```csharp
   ArrayList list = new ArrayList { 1, "text" };
   var risky = list.Cast<int>(); // Vyvolá výjimku u "text"!
   var safe = list.OfType<int>(); // { 1 }
   ```

2. **Ignorování `null` u ne-nullable typů**:  
   ```csharp
   object[] items = { null, "abc", 10 };
   var numbers = items.OfType<int>(); // { 10 } (null je přeskočen)
   ```

3. **Nesprávné použití s generickými kolekcemi**:  
   ```csharp
   List<object> objects = new List<object> { 1, 2, 3 };
   var ints = objects.OfType<int>(); // OK, ale lépe použít List<int> přímo.
   ```

---

### **Tipy pro efektivní použití**  

- **Kombinace s `Where`**:  
  ```csharp
  // Filtruje prvky typu string a délky větší než 3
  var result = kolekce.OfType<string>().Where(s => s.Length > 3);
  ```
- **Optimalizace pro LINQ to Entities**:  
  - Metoda `OfType` se převádí na SQL `OF TYPE` (pokud je podporován, např. u dědičnosti v EF).  

---

### **Shrnutí**  

- `OfType` je **bezpečný nástroj pro filtrování prvků podle typu**.  
- Funguje s generickými i ne-generickými kolekcemi.  
- Vrací prvky kompatibilní s cílovým typem, včetně podtříd a implementovaných rozhraní.  
- Preferujte ji místo `Cast`, pokud není zaručena kompatibilita všech prvků.  

📖 **Zdroj**: [Microsoft Docs – OfType](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.oftype)
