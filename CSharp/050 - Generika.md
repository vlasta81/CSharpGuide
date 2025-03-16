
### Generika v jazyce C#  

Generika umo��uj� vytv��et **univerz�ln� t��dy, metody, rozhran� nebo deleg�ty** s **typov�mi parametry**, kter� se specifikuj� a� p�i pou�it�. Zvy�uj� bezpe�nost, p�ehlednost a znovupou�itelnost k�du. Zde je p�ehled kl��ov�ch koncept�:

---

#### **1. Z�kladn� koncepty**  

- **Typov� parametr**: Z�stupn� symbol (nap�. `T`), kter� ur�uje konkr�tn� typ a� p�i instanciaci.  
- **V�hody**:  
  - **Typov� bezpe�nost**: Zabr�n� chyb�m s nekompatibiln�mi typy (nap�. `List<int>` nep�ijme `string`).  
  - **Eliminace boxingu**: Pr�ce s hodnotov�mi typy bez p�evodu na `object`.  
  - **Znovupou�itelnost k�du**: Jedna generick� t��da pro v�echny typy.  

---

#### **2. Generick� t��da**  

- Definice s typov�m parametrem:  
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
- **Pou�it�**:  
  ```csharp
  Box<int> boxInt = new Box<int>(42);
  Box<string> boxStr = new Box<string>("Ahoj");
  ```

---

#### **3. Generick� metoda**  

- Metoda s vlastn�m typov�m parametrem:  
  ```csharp
  public static T Vetsi<T>(T a, T b) where T : IComparable<T>
  {
      return a.CompareTo(b) > 0 ? a : b;
  }
  ```  
- **Vol�n�**:  
  ```csharp
  int maximum = Vetsi(10, 20); // Typov� inference (T = int)
  ```

---

#### **4. Omezen� typov�ch parametr� (Constraints)**  

- Ur�uj�, jak� typy lze pou��t pro `T`:  
  - **`where T : struct`**: `T` mus� b�t hodnotov� typ.  
  - **`where T : class`**: `T` mus� b�t referen�n� typ.  
  - **`where T : new()`**: `T` mus� m�t bezparametrick� konstruktor.  
  - **`where T : Interface`**: `T` mus� implementovat dan� rozhran�.  
  - **`where T : BaseClass`**: `T` mus� b�t potomek dan� t��dy.  

**P��klad**:  
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

#### **5. V�choz� hodnoty**  

- Pro generick� typy lze pou��t `default(T)`:  
  ```csharp
  public void NastavVychozi()
  {
      T hodnota = default(T); // 0 pro int, null pro string, ...
  }
  ```

---

#### **6. Generick� rozhran� a deleg�ti**  

- **Generick� rozhran�**:  
  ```csharp
  public interface IRepository<T>
  {
      void Uloz(T entita);
      T Nacti(int id);
  }
  ```  
- **Generick� deleg�t**:  
  ```csharp
  public delegate T Operace<T>(T a, T b);
  Operace<int> scitani = (x, y) => x + y;
  ```

---

#### **7. Kolekce a vestav�n� generick� t��dy**  

- **`List<T>`**: Dynamick� pole.  
- **`Dictionary<TKey, TValue>`**: Slovn�k kl��-hodnota.  
- **`Queue<T>`** a **`Stack<T>`**: Fronta a z�sobn�k.  
- **Porovn�n� s negenerick�mi kolekcemi**:  
  - `List<T>` vs `ArrayList`: Bezpe�nost typ� a v�kon.  
  - `Dictionary<TKey, TValue>` vs `Hashtable`: Eliminace boxingu.  

---

#### **8. Kovariance a kontravariance**  

- **Kovariance (`out T`)**: Umo��uje pou��t podtyp (nap�. `IEnumerable<Derived>` jako `IEnumerable<Base>`).  
- **Kontravariance (`in T`)**: Umo��uje pou��t nadtyp (nap�. `Action<Base>` jako `Action<Derived>`).  

**P��klad kovariance**:  
```csharp
IEnumerable<string> texts = new List<string>();
IEnumerable<object> objects = texts; // Platn� d�ky 'out T' v IEnumerable<T>
```

---

#### **9. �ast� chyby**  

- **Pou�it� nekompatibiln�ch omezen�**: Nap�. `where T : struct` s `new()` u typ� bez bezparametrick�ho konstruktoru.  
- **P��li� komplexn� generika**: Zbyte�n� komplikov�n� k�du.  
- **Ignorov�n� typov� bezpe�nosti**: Pou�it� `object` m�sto generik.

---

#### **10. Doporu�en� postupy**  

- Pou��vejte popisn� n�zvy typov�ch parametr� (nap�. `TKey`, `TEntity`).  
- Preferujte generick� kolekce p�ed negenerick�mi.  
- Omezte pou�it� `object` v generick�ch t��d�ch (ztr�ta v�hod generik).  

---

#### **11. Pokro�il� techniky**  

- **Reflexe s generiky**:  
  ```csharp
  Type genericListType = typeof(List<>);
  Type specificListType = genericListType.MakeGenericType(typeof(int));
  var list = Activator.CreateInstance(specificListType);
  ```  
- **Generick� factory metody**:  
  ```csharp
  public static T VytvorInstanci<T>() where T : new() => new T();
  ```  
