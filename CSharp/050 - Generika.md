
### Generika v jazyce C#  

Generika umožňují vytvářet **univerzální třídy, metody, rozhraní nebo delegáty** s **typovými parametry**, které se specifikují až při použití. Zvyšují bezpečnost, přehlednost a znovupoužitelnost kódu. Zde je přehled klíčových konceptů:

---

#### **1. Základní koncepty**  

- **Typový parametr**: Zástupný symbol (např. `T`), který určuje konkrétní typ až při instanciaci.  
- **Výhody**:  
  - **Typová bezpečnost**: Zabrání chybám s nekompatibilními typy (např. `List<int>` nepřijme `string`).  
  - **Eliminace boxingu**: Práce s hodnotovými typy bez převodu na `object`.  
  - **Znovupoužitelnost kódu**: Jedna generická třída pro všechny typy.  

---

#### **2. Generická třída**  

- Definice s typovým parametrem:  
  ```csharp
  public class Box<T>
  {
      public T Obsah { get; set; }
      
      public Box(T obsah)
      {
          Obsah = obsah;
      }
  }
  ```  
- **Použití**:  
  ```csharp
  Box<int> boxInt = new Box<int>(42);
  Box<string> boxStr = new Box<string>("Ahoj");
  ```

---

#### **3. Generická metoda**  

- Metoda s vlastním typovým parametrem:  
  ```csharp
  public static T Vetsi<T>(T a, T b) where T : IComparable<T>
  {
      return a.CompareTo(b) > 0 ? a : b;
  }
  ```  
- **Volání**:  
  ```csharp
  int maximum = Vetsi(10, 20); // Typová inference (T = int)
  ```

---

#### **4. Omezení typových parametrů (Constraints)**  

- Určují, jaké typy lze použít pro `T`:  
  - **`where T : struct`**: `T` musí být hodnotový typ.  
  - **`where T : class`**: `T` musí být referenční typ.  
  - **`where T : new()`**: `T` musí mít bezparametrický konstruktor.  
  - **`where T : Interface`**: `T` musí implementovat dané rozhraní.  
  - **`where T : BaseClass`**: `T` musí být potomek dané třídy.  

**Příklad**:  
```csharp
public class Uloziste<T> where T : IIdentifiable, new()
{
    public T VytvorNovy()
    {
        return new T() { Id = Guid.NewGuid() };
    }
}
```

---

#### **5. Výchozí hodnoty**  

- Pro generické typy lze použít `default(T)`:  
  ```csharp
  public void NastavVychozi()
  {
      T hodnota = default(T); // 0 pro int, null pro string, ...
  }
  ```

---

#### **6. Generická rozhraní a delegáti**  

- **Generické rozhraní**:  
  ```csharp
  public interface IRepository<T>
  {
      void Uloz(T entita);
      T Nacti(int id);
  }
  ```  
- **Generický delegát**:  
  ```csharp
  public delegate T Operace<T>(T a, T b);
  Operace<int> scitani = (x, y) => x + y;
  ```

---

#### **7. Kolekce a vestavěné generické třídy**  

- **`List<T>`**: Dynamické pole.  
- **`Dictionary<TKey, TValue>`**: Slovník klíč-hodnota.  
- **`Queue<T>`** a **`Stack<T>`**: Fronta a zásobník.  
- **Porovnání s negenerickými kolekcemi**:  
  - `List<T>` vs `ArrayList`: Bezpečnost typů a výkon.  
  - `Dictionary<TKey, TValue>` vs `Hashtable`: Eliminace boxingu.  

---

#### **8. Kovariance a kontravariance**  

- **Kovariance (`out T`)**: Umožňuje použít podtyp (např. `IEnumerable<Derived>` jako `IEnumerable<Base>`).  
- **Kontravariance (`in T`)**: Umožňuje použít nadtyp (např. `Action<Base>` jako `Action<Derived>`).  

**Příklad kovariance**:  
```csharp
IEnumerable<string> texts = new List<string>();
IEnumerable<object> objects = texts; // Platné díky 'out T' v IEnumerable<T>
```

---

#### **9. Časté chyby**  

- **Použití nekompatibilních omezení**: Např. `where T : struct` s `new()` u typů bez bezparametrického konstruktoru.  
- **Příliš komplexní generika**: Zbytečné komplikování kódu.  
- **Ignorování typové bezpečnosti**: Použití `object` místo generik.

---

#### **10. Doporučené postupy**  

- Používejte popisné názvy typových parametrů (např. `TKey`, `TEntity`).  
- Preferujte generické kolekce před negenerickými.  
- Omezte použití `object` v generických třídách (ztráta výhod generik).  

---

#### **11. Pokročilé techniky**  

- **Reflexe s generiky**:  
  ```csharp
  Type genericListType = typeof(List<>);
  Type specificListType = genericListType.MakeGenericType(typeof(int));
  var list = Activator.CreateInstance(specificListType);
  ```  
- **Generické factory metody**:  
  ```csharp
  public static T VytvorInstanci<T>() where T : new() => new T();
  ```  
