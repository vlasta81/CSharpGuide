
### **1. Typy porovn�v�n�**

#### **a) Reference Equality**  

- Kontroluje, zda **dv� prom�nn� odkazuj� na stejn� objekt v pam�ti**.  
- Pou��v� oper�tor `==` (pro reference typy) nebo metodu `object.ReferenceEquals()`.  
```csharp
object a = new object();
object b = a;
Console.WriteLine(a == b); // True (stejn� reference)
```

#### **b) Value Equality**  

- Kontroluje, zda **objekty maj� stejn� data** (i kdy� jsou r�zn� instance).  
- Vy�aduje implementaci logiky (nap�. p�eps�n� `Equals`).  
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
Console.WriteLine(p1.Equals(p2)); // True (pokud je Equals p�eps�no)
```

---

### **2. Metody pro porovn�v�n�**

#### **a) `object.Equals()`**  

- V�choz� chov�n�:  
  - Pro **reference typy** porovn�v� reference.  
  - Pro **value typy** (struct) porovn�v� hodnoty v�ech pol�.  
- Lze p�epsat pro vlastn� logiku.  

#### **b) `object.ReferenceEquals()`**  

- V�dy porovn�v� **reference** (ignoruje p�epsan� `Equals`).  

#### **c) `IEquatable<T>`**  

- Efektivn� porovn�v�n� bez boxingu (doporu�eno pro value typy).  
```csharp
public class Person : IEquatable<Person> 
{
    public bool Equals(Person other) => other?.Name == Name;
}
```

---

### **3. D�le�it� koncepty**

#### **a) P�eps�n� `Equals` a `GetHashCode()`**  

- **Pravidlo**: Pokud p�ep�ete `Equals`, v�dy p�epi�te `GetHashCode()`.  
- **HashCode** mus� b�t stejn� pro objekty, kter� jsou si rovny.  
```csharp
public override int GetHashCode() => Name?.GetHashCode() ?? 0;
```

#### **b) Oper�tory `==` a `!=`**  

- Pro reference typy v�choz� `==` porovn�v� reference.  
- Lze p�et�it pro value equality:  
```csharp
public static bool operator ==(Person a, Person b) => a?.Name == b?.Name;
public static bool operator !=(Person a, Person b) => !(a == b);
```

#### **c) `IComparable<T>` a `IComparer<T>`**  

- **`IComparable<T>`**: Definuje p�irozen� po�ad� objekt� (pro �azen�).  
- **`IComparer<T>`**: Extern� logika porovn�v�n� (nap�. pro r�zn� druhy �azen�).  

---

### **4. Porovn�v�n� kolekc�**

#### **a) `EqualityComparer<T>.Default`**  

- Automaticky pou��v� `IEquatable<T>` nebo `Equals`/`GetHashCode`.  
```csharp
var comparer = EqualityComparer<Person>.Default;
Console.WriteLine(comparer.Equals(p1, p2)); // True
```

#### **b) LINQ `SequenceEqual`**  

- Porovn�v� prvky kolekc� v po�ad�.  
```csharp
int[] a = { 1, 2, 3 };
int[] b = { 1, 2, 3 };
Console.WriteLine(a.SequenceEqual(b)); // True
```

---

### **5. Z�znamy (Records) � C# 9+**  

- **Automatick� value-based rovnost**:  
```csharp
public record Person(string Name, int Age);

Person p1 = new Person("Anna", 30);
Person p2 = new Person("Anna", 30);
Console.WriteLine(p1 == p2); // True (porovn�v� hodnoty)
```

---

### **6. B�n� chyby a tipy**

- **Nekonzistentn� `Equals` a `GetHashCode`**: M��e zp�sobit chyby v hashovac�ch kolekc�ch (nap�. `Dictionary`).  
- **Ignorov�n� `IEquatable<T>`**: Zp�sobuje boxing u value typ�.  
- **Porovn�v�n� null hodnot**: V�dy ov��ujte `null` p�ed p��stupem k vlastnostem.  

---

### **7. Uk�zka k�du**

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

// Pou�it�:
Person p1 = new Person { Name = "Anna", Age = 30 };
Person p2 = new Person { Name = "Anna", Age = 30 };
Console.WriteLine(p1 == p2); // True
```

---

### **8. Doporu�en� zdroje**

- **Ofici�ln� dokumentace**:  
  - [Object Equality](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons)  
  - [IEquatable<T>](https://learn.microsoft.com/cs-cz/dotnet/api/system.iequatable-1)  
