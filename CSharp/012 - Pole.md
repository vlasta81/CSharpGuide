
### **1. Definice pole**

- **Statická kolekce** prvků stejného typu s **pevnou velikostí**.
- **Výhody**: Rychlý přístup k prvkům (pomocí indexu), jednoduchá syntaxe.
- **Nevýhody**: Nelze dynamicky měnit velikost (alternativa: `List<T>`).

---

### **2. Deklarace a inicializace**

#### **a) Jednorozměrné pole**

```csharp
// Deklarace
int[] cisla;

// Inicializace s velikostí
cisla = new int[3]; // [0, 0, 0]

// Inicializace s hodnotami
int[] cisla = { 1, 2, 3 }; // Velikost 3
```

#### **b) Vícerozměrné pole (rectangular array)**

```csharp
// 2D pole (matice)
int[,] matice = new int[2, 3] { 
    { 1, 2, 3 }, 
    { 4, 5, 6 } 
};

// 3D pole
int[,,] kostka = new int[2, 2, 2];
```

#### **c) Zubaté pole (jagged array)**

- Pole polí (každý vnitřní pole může mít jinou délku).
```csharp
int[][] zubatePole = new int[3][];
zubatePole[0] = new int[] { 1, 2 };
zubatePole[1] = new int[] { 3 };
zubatePole[2] = new int[] { 4, 5, 6 };
```

---

### **3. Přístup k prvkům**

- Pomocí **indexu** (počítáno od 0).
```csharp
int[] cisla = { 10, 20, 30 };
Console.WriteLine(cisla[0]); // 10

// 2D pole
int[,] matice = new int[2, 2] { { 1, 2 }, { 3, 4 } };
Console.WriteLine(matice[1, 0]); // 3

// Zubaté pole
Console.WriteLine(zubatePole[0][1]); // 2
```

---

### **4. Důležité vlastnosti a metody**

- **`Length`**: Celkový počet prvků v poli.
  ```csharp
  int[] cisla = { 1, 2, 3 };
  Console.WriteLine(cisla.Length); // 3
  ```
  
- **`GetLength(dimenze)`**: Počet prvků v konkrétní dimenzi (pro vícerozměrná pole).
  ```csharp
  int[,] matice = new int[2, 3];
  Console.WriteLine(matice.GetLength(0)); // 2 (řádky)
  Console.WriteLine(matice.GetLength(1)); // 3 (sloupce)
  ```

- **`Clone()`**: Vytvoří **mělkou kopii** pole (pro hodnotové typy – kopie dat, pro referenční typy – kopie referencí).
  ```csharp
  int[] kopie = (int[])cisla.Clone();
  ```

- **`Array.Copy()`**: Kopírování prvků mezi poli.
  ```csharp
  int[] zdroj = { 1, 2, 3 };
  int[] cil = new int[3];
  Array.Copy(zdroj, cil, zdroj.Length);
  ```

---

### **5. Běžné operace**

#### **a) Iterace přes pole**

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

#### **b) Třídění a vyhledávání**

- **`Array.Sort()`**: Seřazení pole.
  ```csharp
  int[] cisla = { 5, 3, 8 };
  Array.Sort(cisla); // [3, 5, 8]
  ```
  
- **`Array.BinarySearch()`**: Vyhledání prvku v seřazeném poli.
  ```csharp
  int index = Array.BinarySearch(cisla, 5); // Vrátí 1
  ```

#### **c) Změna velikosti**

- Nelze přímo – je třeba vytvořit nové pole.
  ```csharp
  int[] starePole = { 1, 2, 3 };
  int[] novePole = new int[5];
  Array.Copy(starePole, novePole, starePole.Length);
  ```

---

### **6. Implicitně typovaná pole (`var`)**

```csharp
var cisla = new[] { 1, 2, 3 }; // Typ int[]
var slova = new[] { "Ahoj", "světe" }; // Typ string[]
```

---

### **7. Pole vs. jiné kolekce**

| **Kritérium**       | **Pole**               | **List<T>**            |  
|----------------------|------------------------|------------------------|  
| **Velikost**         | Pevná                  | Dynamická              |  
| **Výkon**            | Rychlejší              | Pomalejší (dynamické operace) |  
| **Metody**           | Základní               | Bohaté (Add, Remove)   |  

---

### **8. Časté chyby**

- **Index mimo rozsah**:
  ```csharp
  int[] cisla = { 1, 2 };
  Console.WriteLine(cisla[2]); // Vyvolá IndexOutOfRangeException
  ```
  
- **Záměna s referenčními typy**:
  ```csharp
  class Uzivatel { public string Jmeno; }
  Uzivatel[] uzivatele = new Uzivatel[2];
  uzivatele[0].Jmeno = "Karel"; // NullReferenceException! (prvky jsou null)
  ```

---

### **9. Ukázky kódu**

#### **Práce se zubatým polem**

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
// Výstup:
// 1 2
// 3
// 4 5 6
```

#### **Kopírování pole s LINQ**

```csharp
using System.Linq;

int[] zdroj = { 1, 2, 3 };
int[] kopie = zdroj.ToArray(); // LINQ metoda
```

---

### **10. Doporučené zdroje**

- **Oficiální dokumentace**: [Arrays (C# Programming Guide)](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/arrays/)
