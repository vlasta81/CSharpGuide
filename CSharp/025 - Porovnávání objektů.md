
### **1. Typy porovnávání**

#### **a) Reference Equality**  

- Kontroluje, zda **dvì promìnné odkazují na stejný objekt v pamìti**.  
- Používá operátor `==` (pro reference typy) nebo metodu `object.ReferenceEquals()`.  
```csharp
object a = new object();
object b = a;
Console.WriteLine(a == b); // True (stejná reference)
```

#### **b) Value Equality**  

- Kontroluje, zda **objekty mají stejná data** (i když jsou rùzné instance).  
- Vyžaduje implementaci logiky (napø. pøepsání `Equals`).  
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
Console.WriteLine(p1.Equals(p2)); // True (pokud je Equals pøepsáno)
```

---

### **2. Metody pro porovnávání**

#### **a) `object.Equals()`**  

- Výchozí chování:  
  - Pro **reference typy** porovnává reference.  
  - Pro **value typy** (struct) porovnává hodnoty všech polí.  
- Lze pøepsat pro vlastní logiku.  

#### **b) `object.ReferenceEquals()`**  

- Vždy porovnává **reference** (ignoruje pøepsané `Equals`).  

#### **c) `IEquatable<T>`**  

- Efektivní porovnávání bez boxingu (doporuèeno pro value typy).  
```csharp
public class Person : IEquatable<Person> 
{
    public bool Equals(Person other) => other?.Name == Name;
}
```

---

### **3. Dùležité koncepty**

#### **a) Pøepsání `Equals` a `GetHashCode()`**  

- **Pravidlo**: Pokud pøepíšete `Equals`, vždy pøepište `GetHashCode()`.  
- **HashCode** musí být stejný pro objekty, které jsou si rovny.  
```csharp
public override int GetHashCode() => Name?.GetHashCode() ?? 0;
```

#### **b) Operátory `==` a `!=`**  

- Pro reference typy výchozí `==` porovnává reference.  
- Lze pøetížit pro value equality:  
```csharp
public static bool operator ==(Person a, Person b) => a?.Name == b?.Name;
public static bool operator !=(Person a, Person b) => !(a == b);
```

#### **c) `IComparable<T>` a `IComparer<T>`**  

- **`IComparable<T>`**: Definuje pøirozené poøadí objektù (pro øazení).  
- **`IComparer<T>`**: Externí logika porovnávání (napø. pro rùzné druhy øazení).  

---

### **4. Porovnávání kolekcí**

#### **a) `EqualityComparer<T>.Default`**  

- Automaticky používá `IEquatable<T>` nebo `Equals`/`GetHashCode`.  
```csharp
var comparer = EqualityComparer<Person>.Default;
Console.WriteLine(comparer.Equals(p1, p2)); // True
```

#### **b) LINQ `SequenceEqual`**  

- Porovnává prvky kolekcí v poøadí.  
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

### **6. Bìžné chyby a tipy**

- **Nekonzistentní `Equals` a `GetHashCode`**: Mùže zpùsobit chyby v hashovacích kolekcích (napø. `Dictionary`).  
- **Ignorování `IEquatable<T>`**: Zpùsobuje boxing u value typù.  
- **Porovnávání null hodnot**: Vždy ovìøujte `null` pøed pøístupem k vlastnostem.  

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

### **8. Doporuèené zdroje**

- **Oficiální dokumentace**:  
  - [Object Equality](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons)  
  - [IEquatable<T>](https://learn.microsoft.com/cs-cz/dotnet/api/system.iequatable-1)  
