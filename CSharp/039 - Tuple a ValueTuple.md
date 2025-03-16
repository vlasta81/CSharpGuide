
### **Tuple a ValueTuple v jazyce C#**

---

#### **1. �vod**

**Tuple** a **ValueTuple** slou�� k **seskupen� v�ce hodnot do jednoho objektu**. Umo��uj� n�vrat v�ce hodnot z metody bez nutnosti vytv��et vlastn� t��du nebo strukturu. Rozd�l mezi nimi spo��v� v implementaci a vlastnostech.

---

### **2. Tuple (System.Tuple)**

- **Typ**: Reference typ (alokov�n na hald�).
- **Z�kladn� vlastnosti**:
  - Prvky jsou **read-only** (nelze je m�nit po vytvo�en�).
  - Prvky jsou pojmenov�ny `Item1`, `Item2`, atd.
  - Vhodn� pro jednor�zov� pou�it� nebo v star��ch verz�ch C# (p�ed C# 7).
- **Syntaxe**:
  ```csharp
  var person = Tuple.Create(1, "Alice", 30);
  Console.WriteLine(person.Item1); // 1
  ```

---

### **3. ValueTuple (System.ValueTuple)**

- **Typ**: Value typ (alokov�n na z�sobn�ku, vy��� v�kon).
- **Z�kladn� vlastnosti**:
  - Prvky jsou **mutable** (lze je m�nit).
  - Podpora **pojmenovan�ch prvk�** pro lep�� �itelnost.
  - Mo�nost **dekonstrukce** do samostatn�ch prom�nn�ch.
  - Dostupn� od C# 7.
- **Syntaxe**:
  ```csharp
  // Vytvo�en� s pojmenovan�mi prvky
  var person = (Id: 1, Name: "Alice", Age: 30);
  Console.WriteLine(person.Name); // "Alice"

  // Dekonstrukce
  (int id, string name, _) = person; // Ignorov�n� v�ku pomoc� _
  ```

---

### **4. Kl��ov� rozd�ly**

| **Krit�rium**         | **Tuple**                     | **ValueTuple**                 |
|-----------------------|-------------------------------|---------------------------------|
| **Typ**               | Reference typ (class)         | Value typ (struct)             |
| **Mutabilita**        | Read-only                     | Mutable                        |
| **Pojmenov�n� prvk�** | Pouze `Item1`, `Item2`, ...   | Vlastn� n�zvy (syntaktick� cukr) |
| **V�kon**             | Pomalej�� (halda)             | Rychlej�� (z�sobn�k)           |
| **Dekonstrukce**      | Ne                            | Ano                            |
| **Podpora od verze**  | C# 4.0                        | C# 7.0                         |

---

### **5. Dekonstrukce a pr�ce s metodami**

- **N�vrat ValueTuple z metody**:
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

### **6. Pojmenovan� prvky**

- Zlep�uj� �itelnost k�du, ale jsou pouze **syntaktick�m cukrem** (v runtime se pou��vaj� `Item1`, `Item2`).
  ```csharp
  var point = (X: 10, Y: 20);
  Console.WriteLine(point.X); // 10
  ```

---

### **7. Porovn�n� hodnot**

- **ValueTuple** porovn�v� **hodnoty prvk�**:
  ```csharp
  var a = (1, "A");
  var b = (1, "A");
  Console.WriteLine(a == b); // True (C# 7.3+)
  ```

- **Tuple** porovn�v� **reference** (pou�ijte `Equals` pro porovn�n� hodnot):
  ```csharp
  var a = Tuple.Create(1, "A");
  var b = Tuple.Create(1, "A");
  Console.WriteLine(a.Equals(b)); // True
  ```

---

### **8. Kdy pou��t?**

- **Tuple**:
  - Kdy� pot�ebujete **read-only** kolekci hodnot.
  - V star��m k�du (p�ed C# 7).
- **ValueTuple**:
  - Pro **efektivn� pr�ci s do�asn�mi daty**.
  - Kdy� pot�ebujete **pojmenovan� prvky** nebo **dekonstrukci**.
  - V nov�ch projektech (C# 7+).

---

### **9. Omezen� a tipy**

- **ValueTuple nen� v�dy optim�ln�**: Pro slo�it� nebo velk� struktury preferujte t��dy/struktury.
- **Pozor na mutabilitu**: M�n�n� prvk� ValueTuple m��e v�st k ne�ekan�mu chov�n�.
- **Instalace**: V n�kter�ch projektech je t�eba p�idat NuGet bal��ek `System.ValueTuple`.

---

### **10. Shrnut�**

- **Tuple** je reference typ s read-only prvky, vhodn� pro star�� k�d.
- **ValueTuple** je value typ s lep��m v�konem, mutabiln�mi prvky a modern� syntax�.
- Pou��vejte ValueTuple pro v�t�inu sc�n��� v C# 7 a nov�j��ch.
