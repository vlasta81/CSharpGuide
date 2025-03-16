
### **1. Typy porovnávání**

#### **a) Reference Equality**  

- Kontroluje, zda **dvě proměnné odkazují na stejný objekt v paměti**.  
- Používá operátor `==` (pro reference typy) nebo metodu `object.ReferenceEquals()`.  
```csharp
object a = new object();
object b = a;
Console.WriteLine(a == b); // True (stejná reference)
```

#### **b) Value Equality**  

- Kontroluje, zda **objekty mají stejná data** (i když jsou různé instance).  
- Vyžaduje implementaci logiky (např. přepsání `Equals`).  
```csharp
public class Person 
{
    public string Name { get; set; }
    
    public override bool Equals(object obj) 
    {
        return obj is Person p && p.Name == Name;
    }
}

Person p1 = new Person { Name = "Anna" };
Person p2 = new Person { Name = "Anna" };
Console.WriteLine(p1.Equals(p2)); // True (pokud je Equals přepsáno)
```

---

### **2. Metody pro porovnávání**

#### **a) `object.Equals()`**  

- Výchozí chování:  
  - Pro **reference typy** porovnává reference.  
  - Pro **value typy** (struct) porovnává hodnoty všech polí.  
- Lze přepsat pro vlastní logiku.  

#### **b) `object.ReferenceEquals()`**  

- Vždy porovnává **reference** (ignoruje přepsané `Equals`).  

#### **c) `IEquatable<T>`**  

- Efektivní porovnávání bez boxingu (doporučeno pro value typy).  
```csharp
public class Person : IEquatable<Person> 
{
    public bool Equals(Person other) => other?.Name == Name;
}
```

---

### **3. Důležité koncepty**

#### **a) Přepsání `Equals` a `GetHashCode()`**  

- **Pravidlo**: Pokud přepíšete `Equals`, vždy přepište `GetHashCode()`.  
- **HashCode** musí být stejný pro objekty, které jsou si rovny.  
```csharp
public override int GetHashCode() => Name?.GetHashCode() ?? 0;
```

#### **b) Operátory `==` a `!=`**  

- Pro reference typy výchozí `==` porovnává reference.  
- Lze přetížit pro value equality:  
```csharp
public static bool operator ==(Person a, Person b) => a?.Name == b?.Name;
public static bool operator !=(Person a, Person b) => !(a == b);
```

#### **c) `IComparable<T>` a `IComparer<T>`**  

- **`IComparable<T>`**: Definuje přirozené pořadí objektů (pro řazení).  
- **`IComparer<T>`**: Externí logika porovnávání (např. pro různé druhy řazení).  

---

### **4. Porovnávání kolekcí**

#### **a) `EqualityComparer<T>.Default`**  

- Automaticky používá `IEquatable<T>` nebo `Equals`/`GetHashCode`.  
```csharp
var comparer = EqualityComparer<Person>.Default;
Console.WriteLine(comparer.Equals(p1, p2)); // True
```

#### **b) LINQ `SequenceEqual`**  

- Porovnává prvky kolekcí v pořadí.  
```csharp
int[] a = { 1, 2, 3 };
int[] b = { 1, 2, 3 };
Console.WriteLine(a.SequenceEqual(b)); // True
```

---

### **5. Záznamy (Records) – C# 9+**  

- **Automatická value-based rovnost**:  
```csharp
public record Person(string Name, int Age);

Person p1 = new Person("Anna", 30);
Person p2 = new Person("Anna", 30);
Console.WriteLine(p1 == p2); // True (porovnává hodnoty)
```

---

### **6. Běžné chyby a tipy**

- **Nekonzistentní `Equals` a `GetHashCode`**: Může způsobit chyby v hashovacích kolekcích (např. `Dictionary`).  
- **Ignorování `IEquatable<T>`**: Způsobuje boxing u value typů.  
- **Porovnávání null hodnot**: Vždy ověřujte `null` před přístupem k vlastnostem.  

---

### **7. Ukázka kódu**

```csharp
public class Person : IEquatable<Person>
{
    public string Name { get; init; }
    public int Age { get; init; }

    public bool Equals(Person other) 
    {
        if (other is null) return false;
        return Name == other.Name && Age == other.Age;
    }

    public override bool Equals(object obj) => Equals(obj as Person);

    public override int GetHashCode() => HashCode.Combine(Name, Age);

    public static bool operator ==(Person a, Person b) => a?.Equals(b) ?? false;
    public static bool operator !=(Person a, Person b) => !(a == b);
}

// Použití:
Person p1 = new Person { Name = "Anna", Age = 30 };
Person p2 = new Person { Name = "Anna", Age = 30 };
Console.WriteLine(p1 == p2); // True
```

---

### **8. Doporučené zdroje**

- **Oficiální dokumentace**:  
  - [Object Equality](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons)  
  - [IEquatable<T>](https://learn.microsoft.com/cs-cz/dotnet/api/system.iequatable-1)  
