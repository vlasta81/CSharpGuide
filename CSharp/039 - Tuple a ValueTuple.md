
### **Tuple a ValueTuple v jazyce C#**

---

#### **1. Úvod**

**Tuple** a **ValueTuple** slouží k **seskupení více hodnot do jednoho objektu**. Umožòují návrat více hodnot z metody bez nutnosti vytváøet vlastní tøídu nebo strukturu. Rozdíl mezi nimi spoèívá v implementaci a vlastnostech.

---

### **2. Tuple (System.Tuple)**

- **Typ**: Reference typ (alokován na haldì).
- **Základní vlastnosti**:
  - Prvky jsou **read-only** (nelze je mìnit po vytvoøení).
  - Prvky jsou pojmenovány `Item1`, `Item2`, atd.
  - Vhodný pro jednorázové použití nebo v starších verzích C# (pøed C# 7).
- **Syntaxe**:
  ```csharp
  var person = Tuple.Create(1, "Alice", 30);
  Console.WriteLine(person.Item1); // 1
  ```

---

### **3. ValueTuple (System.ValueTuple)**

- **Typ**: Value typ (alokován na zásobníku, vyšší výkon).
- **Základní vlastnosti**:
  - Prvky jsou **mutable** (lze je mìnit).
  - Podpora **pojmenovaných prvkù** pro lepší èitelnost.
  - Možnost **dekonstrukce** do samostatných promìnných.
  - Dostupné od C# 7.
- **Syntaxe**:
  ```csharp
  // Vytvoøení s pojmenovanými prvky
  var person = (Id: 1, Name: "Alice", Age: 30);
  Console.WriteLine(person.Name); // "Alice"

  // Dekonstrukce
  (int id, string name, _) = person; // Ignorování vìku pomocí _
  ```

---

### **4. Klíèové rozdíly**

| **Kritérium**         | **Tuple**                     | **ValueTuple**                 |
|-----------------------|-------------------------------|---------------------------------|
| **Typ**               | Reference typ (class)         | Value typ (struct)             |
| **Mutabilita**        | Read-only                     | Mutable                        |
| **Pojmenování prvkù** | Pouze `Item1`, `Item2`, ...   | Vlastní názvy (syntaktický cukr) |
| **Výkon**             | Pomalejší (halda)             | Rychlejší (zásobník)           |
| **Dekonstrukce**      | Ne                            | Ano                            |
| **Podpora od verze**  | C# 4.0                        | C# 7.0                         |

---

### **5. Dekonstrukce a práce s metodami**

- **Návrat ValueTuple z metody**:
  ```csharp
  public (int Id, string Name) GetPerson() 
  {
      return (1, "Alice");
  }

  var person = GetPerson();
  Console.WriteLine(person.Name); // "Alice"
  ```

- **Dekonstrukce**:
  ```csharp
  var (id, name) = GetPerson();
  Console.WriteLine(id); // 1
  ```

---

### **6. Pojmenované prvky**

- Zlepšují èitelnost kódu, ale jsou pouze **syntaktickým cukrem** (v runtime se používají `Item1`, `Item2`).
  ```csharp
  var point = (X: 10, Y: 20);
  Console.WriteLine(point.X); // 10
  ```

---

### **7. Porovnání hodnot**

- **ValueTuple** porovnává **hodnoty prvkù**:
  ```csharp
  var a = (1, "A");
  var b = (1, "A");
  Console.WriteLine(a == b); // True (C# 7.3+)
  ```

- **Tuple** porovnává **reference** (použijte `Equals` pro porovnání hodnot):
  ```csharp
  var a = Tuple.Create(1, "A");
  var b = Tuple.Create(1, "A");
  Console.WriteLine(a.Equals(b)); // True
  ```

---

### **8. Kdy použít?**

- **Tuple**:
  - Když potøebujete **read-only** kolekci hodnot.
  - V starším kódu (pøed C# 7).
- **ValueTuple**:
  - Pro **efektivní práci s doèasnými daty**.
  - Když potøebujete **pojmenované prvky** nebo **dekonstrukci**.
  - V nových projektech (C# 7+).

---

### **9. Omezení a tipy**

- **ValueTuple není vždy optimální**: Pro složité nebo velké struktury preferujte tøídy/struktury.
- **Pozor na mutabilitu**: Mìnìní prvkù ValueTuple mùže vést k neèekanému chování.
- **Instalace**: V nìkterých projektech je tøeba pøidat NuGet balíèek `System.ValueTuple`.

---

### **10. Shrnutí**

- **Tuple** je reference typ s read-only prvky, vhodný pro starší kód.
- **ValueTuple** je value typ s lepším výkonem, mutabilními prvky a moderní syntaxí.
- Používejte ValueTuple pro vìtšinu scénáøù v C# 7 a novìjších.
