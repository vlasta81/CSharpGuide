
### **1. Definice pole**

- **Statick� kolekce** prvk� stejn�ho typu s **pevnou velikost�**.
- **V�hody**: Rychl� p��stup k prvk�m (pomoc� indexu), jednoduch� syntaxe.
- **Nev�hody**: Nelze dynamicky m�nit velikost (alternativa: `List<T>`).

---

### **2. Deklarace a inicializace**

#### **a) Jednorozm�rn� pole**

```csharp
// Deklarace
int[] cisla;

// Inicializace s velikost�
cisla = new int[3]; // [0, 0, 0]

// Inicializace s hodnotami
int[] cisla = { 1, 2, 3 }; // Velikost 3
```

#### **b) V�cerozm�rn� pole (rectangular array)**

```csharp
// 2D pole (matice)
int[,] matice = new int[2, 3] { 
    { 1, 2, 3 }, 
    { 4, 5, 6 } 
};

// 3D pole
int[,,] kostka = new int[2, 2, 2];
```

#### **c) Zubat� pole (jagged array)**

- Pole pol� (ka�d� vnit�n� pole m��e m�t jinou d�lku).
```csharp
int[][] zubatePole = new int[3][];
zubatePole[0] = new int[] { 1, 2 };
zubatePole[1] = new int[] { 3 };
zubatePole[2] = new int[] { 4, 5, 6 };
```

---

### **3. P��stup k prvk�m**

- Pomoc� **indexu** (po��t�no od 0).
```csharp
int[] cisla = { 10, 20, 30 };
Console.WriteLine(cisla[0]); // 10

// 2D pole
int[,] matice = new int[2, 2] { { 1, 2 }, { 3, 4 } };
Console.WriteLine(matice[1, 0]); // 3

// Zubat� pole
Console.WriteLine(zubatePole[0][1]); // 2
```

---

### **4. D�le�it� vlastnosti a metody**

- **`Length`**: Celkov� po�et prvk� v poli.
  ```csharp
  int[] cisla = { 1, 2, 3 };
  Console.WriteLine(cisla.Length); // 3
  ```
  
- **`GetLength(dimenze)`**: Po�et prvk� v konkr�tn� dimenzi (pro v�cerozm�rn� pole).
  ```csharp
  int[,] matice = new int[2, 3];
  Console.WriteLine(matice.GetLength(0)); // 2 (��dky)
  Console.WriteLine(matice.GetLength(1)); // 3 (sloupce)
  ```

- **`Clone()`**: Vytvo�� **m�lkou kopii** pole (pro hodnotov� typy � kopie dat, pro referen�n� typy � kopie referenc�).
  ```csharp
  int[] kopie = (int[])cisla.Clone();
  ```

- **`Array.Copy()`**: Kop�rov�n� prvk� mezi poli.
  ```csharp
  int[] zdroj = { 1, 2, 3 };
  int[] cil = new int[3];
  Array.Copy(zdroj, cil, zdroj.Length);
  ```

---

### **5. B�n� operace**

#### **a) Iterace p�es pole**

```csharp
// For cyklus
for (int i = 0; i < cisla.Length; i++) {
    Console.WriteLine(cisla[i]);
}

// Foreach cyklus
foreach (int cislo in cisla) {
    Console.WriteLine(cislo);
}

// 2D pole
for (int i = 0; i < matice.GetLength(0); i++) {
    for (int j = 0; j < matice.GetLength(1); j++) {
        Console.Write(matice[i, j] + " ");
    }
    Console.WriteLine();
}
```

#### **b) T��d�n� a vyhled�v�n�**

- **`Array.Sort()`**: Se�azen� pole.
  ```csharp
  int[] cisla = { 5, 3, 8 };
  Array.Sort(cisla); // [3, 5, 8]
  ```
  
- **`Array.BinarySearch()`**: Vyhled�n� prvku v se�azen�m poli.
  ```csharp
  int index = Array.BinarySearch(cisla, 5); // Vr�t� 1
  ```

#### **c) Zm�na velikosti**

- Nelze p��mo � je t�eba vytvo�it nov� pole.
  ```csharp
  int[] starePole = { 1, 2, 3 };
  int[] novePole = new int[5];
  Array.Copy(starePole, novePole, starePole.Length);
  ```

---

### **6. Implicitn� typovan� pole (`var`)**

```csharp
var cisla = new[] { 1, 2, 3 }; // Typ int[]
var slova = new[] { "Ahoj", "sv�te" }; // Typ string[]
```

---

### **7. Pole vs. jin� kolekce**

| **Krit�rium**       | **Pole**               | **List<T>**            |  
|----------------------|------------------------|------------------------|  
| **Velikost**         | Pevn�                  | Dynamick�              |  
| **V�kon**            | Rychlej��              | Pomalej�� (dynamick� operace) |  
| **Metody**           | Z�kladn�               | Bohat� (Add, Remove)   |  

---

### **8. �ast� chyby**

- **Index mimo rozsah**:
  ```csharp
  int[] cisla = { 1, 2 };
  Console.WriteLine(cisla[2]); // Vyvol� IndexOutOfRangeException
  ```
  
- **Z�m�na s referen�n�mi typy**:
  ```csharp
  class Uzivatel { public string Jmeno; }
  Uzivatel[] uzivatele = new Uzivatel[2];
  uzivatele[0].Jmeno = "Karel"; // NullReferenceException! (prvky jsou null)
  ```

---

### **9. Uk�zky k�du**

#### **Pr�ce se zubat�m polem**

```csharp
int[][] jagged = new int[3][];
jagged[0] = new int[] { 1, 2 };
jagged[1] = new int[] { 3 };
jagged[2] = new int[] { 4, 5, 6 };

foreach (int[] vnitrniPole in jagged) {
    foreach (int cislo in vnitrniPole) {
        Console.Write(cislo + " ");
    }
    Console.WriteLine();
}
// V�stup:
// 1 2
// 3
// 4 5 6
```

#### **Kop�rov�n� pole s LINQ**

```csharp
using System.Linq;

int[] zdroj = { 1, 2, 3 };
int[] kopie = zdroj.ToArray(); // LINQ metoda
```

---

### **10. Doporu�en� zdroje**

- **Ofici�ln� dokumentace**: [Arrays (C# Programming Guide)](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/arrays/)
