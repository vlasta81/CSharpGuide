
### **Tuple a ValueTuple v jazyce C#**

---

#### **1. Úvod**

**Tuple** a **ValueTuple** slouží k **seskupení více hodnot do jednoho objektu**. Umožňují návrat více hodnot z metody bez nutnosti vytvářet vlastní třídu nebo strukturu. Rozdíl mezi nimi spočívá v implementaci a vlastnostech.

---

### **2. Tuple (System.Tuple)**

- **Typ**: Reference typ (alokován na haldě).
- **Základní vlastnosti**:
  - Prvky jsou **read-only** (nelze je měnit po vytvoření).
  - Prvky jsou pojmenovány `Item1`, `Item2`, atd.
  - Vhodný pro jednorázové použití nebo v starších verzích C# (před C# 7).
- **Syntaxe**:
  ```csharp
  var person = Tuple.Create(1, "Alice", 30);
  Console.WriteLine(person.Item1); // 1
  ```

---

### **3. ValueTuple (System.ValueTuple)**

- **Typ**: Value typ (alokován na zásobníku, vyšší výkon).
- **Základní vlastnosti**:
  - Prvky jsou **mutable** (lze je měnit).
  - Podpora **pojmenovaných prvků** pro lepší čitelnost.
  - Možnost **dekonstrukce** do samostatných proměnných.
  - Dostupné od C# 7.
- **Syntaxe**:
  ```csharp
  // Vytvoření s pojmenovanými prvky
  var person = (Id: 1, Name: "Alice", Age: 30);
  Console.WriteLine(person.Name); // "Alice"

  // Dekonstrukce
  (int id, string name, _) = person; // Ignorování věku pomocí _
  ```

---

### **4. Klíčové rozdíly**

| **Kritérium**         | **Tuple**                     | **ValueTuple**                 |
|-----------------------|-------------------------------|---------------------------------|
| **Typ**               | Reference typ (class)         | Value typ (struct)             |
| **Mutabilita**        | Read-only                     | Mutable                        |
| **Pojmenování prvků** | Pouze `Item1`, `Item2`, ...   | Vlastní názvy (syntaktický cukr) |
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

- Zlepšují čitelnost kódu, ale jsou pouze **syntaktickým cukrem** (v runtime se používají `Item1`, `Item2`).
  ```csharp
  var point = (X: 10, Y: 20);
  Console.WriteLine(point.X); // 10
  ```

---

### **7. Porovnání hodnot**

- **ValueTuple** porovnává **hodnoty prvků**:
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
  - Když potřebujete **read-only** kolekci hodnot.
  - V starším kódu (před C# 7).
- **ValueTuple**:
  - Pro **efektivní práci s dočasnými daty**.
  - Když potřebujete **pojmenované prvky** nebo **dekonstrukci**.
  - V nových projektech (C# 7+).

---

### **9. Omezení a tipy**

- **ValueTuple není vždy optimální**: Pro složité nebo velké struktury preferujte třídy/struktury.
- **Pozor na mutabilitu**: Měnění prvků ValueTuple může vést k nečekanému chování.
- **Instalace**: V některých projektech je třeba přidat NuGet balíček `System.ValueTuple`.

---

### **10. Shrnutí**

- **Tuple** je reference typ s read-only prvky, vhodný pro starší kód.
- **ValueTuple** je value typ s lepším výkonem, mutabilními prvky a moderní syntaxí.
- Používejte ValueTuple pro většinu scénářů v C# 7 a novějších.
