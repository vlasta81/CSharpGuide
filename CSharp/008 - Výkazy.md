
### **1. Definice výkazu**

- **Výkaz** je instrukce, která provádí akci (napø. pøiøazení hodnoty, volání metody, øízení toku programu).  
- Každý výkaz konèí støedníkem `;` (kromì blokù `{}`).

---

### **2. Kategorie výkazù**

#### **a) Deklaraèní výkazy**

- Deklarují promìnné, konstanty nebo funkce.  
  ```csharp
  int x = 10;
  const double PI = 3.14;
  ```

#### **b) Výrazové výkazy**

- Vyhodnocení výrazu s vedlejším úèinkem (napø. pøiøazení, volání metody).  
  ```csharp
  x = 5 + 3;
  Console.WriteLine("Ahoj!");
  ```

#### **c) Øízení toku programu**

##### **Podmínìné výkazy**  

- **`if`-`else`**:  
  ```csharp
  if (vek >= 18) {
      Console.WriteLine("Dospìlý");
  } else {
      Console.WriteLine("Nezletilý");
  }
  ```
  
- **`switch`**:  
  ```csharp
  switch (den) {
      case DayOfWeek.Monday: 
          Console.WriteLine("Pondìlí"); 
          break;
      default: 
          Console.WriteLine("Jiný den"); 
          break;
  }
  ```

##### **Cyklus (iterace)**  

- **`for`**:  
  ```csharp
  for (int i = 0; i < 10; i++) {
      Console.WriteLine(i);
  }
  ```
  
- **`foreach`**:  
  ```csharp
  foreach (var item in kolekce) {
      Console.WriteLine(item);
  }
  ```
  
- **`while`**:  
  ```csharp
  while (podminka) {
      // Kód
  }
  ```
  
- **`do-while`**:  
  ```csharp
  do {
      // Kód
  } while (podminka);
  ```

##### **Skokové výkazy**  

- **`break`**: Ukonèí cyklus nebo `switch`.  
- **`continue`**: Pøeskoèí aktuální iteraci cyklu.  
- **`return`**: Vrátí hodnotu z metody.  
- **`goto`**: Pøeskoèí na oznaèený výkaz (používejte výjimeènì!).  
  ```csharp
  goto Navesti;
  // ...
  Navesti: 
      Console.WriteLine("Návìští");
  ```

#### **d) Výjimkové výkazy**

- **`try`-`catch`-`finally`**:  
  ```csharp
  try {
      // Rizikový kód
  } catch (Exception ex) {
      Console.WriteLine(ex.Message);
  } finally {
      // Úklidový kód
  }
  ```
  
- **`throw`**: Vyvolá výjimku.  
  ```csharp
  throw new InvalidOperationException("Chyba!");
  ```

#### **e) Blokové výkazy**

- Skupina výkazù uzavøená v `{}`.  
  ```csharp
  {
      int a = 5;
      Console.WriteLine(a);
  }
  ```

#### **f) Ostatní výkazy**

- **`using`**: Automaticky uvolní prostøedky (pro `IDisposable`).  
  ```csharp
  using (var reader = new StreamReader("soubor.txt")) {
      // Práce se souborem
  }
  ```
  
- **`checked`/`unchecked`**: Kontrola pøeteèení aritmetických operací.  
  ```csharp
  checked {
      int x = int.MaxValue + 1; // Vyvolá OverflowException
  }
  ```
  
- **`lock`**: Zajišuje synchronizaci vláken.  
  ```csharp
  lock (lockObject) {
      // Kritická sekce
  }
  ```

---

### **3. Speciální výkazy**

#### **`yield`**  

- Používá se v iterátorech pro postupné vracení hodnot.  
  ```csharp
  public IEnumerable<int> GenerujCisla() {
      for (int i = 0; i < 5; i++) {
          yield return i;
      }
  }
  ```

#### **`await`**  

- Asynchronní volání (v metodách oznaèených `async`).  
  ```csharp
  async Task NactiData() {
      var data = await HttpClient.GetStringAsync("url");
  }
  ```

---

### **4. Porovnání cyklù**

| **Cyklus**   | **Vhodné použití**                  | **Poznámka**                          |
|--------------|-------------------------------------|----------------------------------------|
| `for`        | Známý poèet iterací                 | Ideální pro indexované kolekce         |
| `foreach`    | Práce s kolekcemi                   | Nevyžaduje index                       |
| `while`      | Neznámý poèet iterací (podmínka na zaèátku) | Mùže být nekoneèný                    |
| `do-while`   | Neznámý poèet iterací (podmínka na konci) | Provede se minimálnì jednou           |

---

### **5. Bìžné chyby a tipy**

- **Zapomenutý `break` v `switch`**:  
  ```csharp
  case 1: 
      Console.WriteLine("1");
      break; // Bez break zpùsobí chybu kompilace!
  ```
  
- **Nekoneèné cykly**:  
  ```csharp
  while (true) { 
      // Chybí podmínka pro ukonèení
  }
  ```
  
- **Použití `goto`**: Lze použít pro opuštìní vnoøených cyklù, ale obecnì se nedoporuèuje.  
- **`using` pro ne-`IDisposable` typy**: Vyvolá chybu kompilace.

---

### **6. Ukázky kódu**

#### **Komplexní použití `try-catch`**

```csharp
try {
    int x = int.Parse("neèíslo");
} catch (FormatException) {
    Console.WriteLine("Neplatný formát!");
} catch (Exception ex) {
    Console.WriteLine($"Obecná chyba: {ex.Message}");
} finally {
    Console.WriteLine("Konec zpracování.");
}
```

#### **Iterátor s `yield`**

```csharp
public IEnumerable<string> Filtruj(IEnumerable<string> data) {
    foreach (var polozka in data) {
        if (polozka.StartsWith("A")) {
            yield return polozka;
        }
    }
}
```

---

### **7. Doporuèené zdroje**

- Oficiální dokumentace: [Statements in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/statements-expressions-operators/statements)
- Kniha: *C# 10 in a Nutshell* (Albahari) – kapitola o øízení toku.
