
### Generika v jazyce C#  

Generika umožòují vytváøet **univerzální tøídy, metody, rozhraní nebo delegáty** s **typovými parametry**, které se specifikují až pøi použití. Zvyšují bezpeènost, pøehlednost a znovupoužitelnost kódu. Zde je pøehled klíèových konceptù:

---

#### **1. Základní koncepty**  

- **Typový parametr**: Zástupný symbol (napø. `T`), který urèuje konkrétní typ až pøi instanciaci.  
- **Výhody**:  
  - **Typová bezpeènost**: Zabrání chybám s nekompatibilními typy (napø. `List<int>` nepøijme `string`).  
  - **Eliminace boxingu**: Práce s hodnotovými typy bez pøevodu na `object`.  
  - **Znovupoužitelnost kódu**: Jedna generická tøída pro všechny typy.  

---

#### **2. Generická tøída**  

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

#### **4. Omezení typových parametrù (Constraints)**  

- Urèují, jaké typy lze použít pro `T`:  
  - **`where T : struct`**: `T` musí být hodnotový typ.  
  - **`where T : class`**: `T` musí být referenèní typ.  
  - **`where T : new()`**: `T` musí mít bezparametrický konstruktor.  
  - **`where T : Interface`**: `T` musí implementovat dané rozhraní.  
  - **`where T : BaseClass`**: `T` musí být potomek dané tøídy.  

**Pøíklad**:  
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

#### **7. Kolekce a vestavìné generické tøídy**  

- **`List<T>`**: Dynamické pole.  
- **`Dictionary<TKey, TValue>`**: Slovník klíè-hodnota.  
- **`Queue<T>`** a **`Stack<T>`**: Fronta a zásobník.  
- **Porovnání s negenerickými kolekcemi**:  
  - `List<T>` vs `ArrayList`: Bezpeènost typù a výkon.  
  - `Dictionary<TKey, TValue>` vs `Hashtable`: Eliminace boxingu.  

---

#### **8. Kovariance a kontravariance**  

- **Kovariance (`out T`)**: Umožòuje použít podtyp (napø. `IEnumerable<Derived>` jako `IEnumerable<Base>`).  
- **Kontravariance (`in T`)**: Umožòuje použít nadtyp (napø. `Action<Base>` jako `Action<Derived>`).  

**Pøíklad kovariance**:  
```csharp
IEnumerable<string> texts = new List<string>();
IEnumerable<object> objects = texts; // Platné díky 'out T' v IEnumerable<T>
```

---

#### **9. Èasté chyby**  

- **Použití nekompatibilních omezení**: Napø. `where T : struct` s `new()` u typù bez bezparametrického konstruktoru.  
- **Pøíliš komplexní generika**: Zbyteèné komplikování kódu.  
- **Ignorování typové bezpeènosti**: Použití `object` místo generik.

---

#### **10. Doporuèené postupy**  

- Používejte popisné názvy typových parametrù (napø. `TKey`, `TEntity`).  
- Preferujte generické kolekce pøed negenerickými.  
- Omezte použití `object` v generických tøídách (ztráta výhod generik).  

---

#### **11. Pokroèilé techniky**  

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
