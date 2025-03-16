
### **1. Definice výkazu**

- **Výkaz** je instrukce, která provádí akci (např. přiřazení hodnoty, volání metody, řízení toku programu).  
- Každý výkaz končí středníkem `;` (kromě bloků `{}`).

---

### **2. Kategorie výkazů**

#### **a) Deklarační výkazy**

- Deklarují proměnné, konstanty nebo funkce.  
  ```csharp
  int x = 10;
  const double PI = 3.14;
  ```

#### **b) Výrazové výkazy**

- Vyhodnocení výrazu s vedlejším účinkem (např. přiřazení, volání metody).  
  ```csharp
  x = 5 + 3;
  Console.WriteLine("Ahoj!");
  ```

#### **c) Řízení toku programu**

##### **Podmíněné výkazy**  

- **`if`-`else`**:  
  ```csharp
  if (vek >= 18) {
      Console.WriteLine("Dospělý");
  } else {
      Console.WriteLine("Nezletilý");
  }
  ```
  
- **`switch`**:  
  ```csharp
  switch (den) {
      case DayOfWeek.Monday: 
          Console.WriteLine("Pondělí"); 
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

- **`break`**: Ukončí cyklus nebo `switch`.  
- **`continue`**: Přeskočí aktuální iteraci cyklu.  
- **`return`**: Vrátí hodnotu z metody.  
- **`goto`**: Přeskočí na označený výkaz (používejte výjimečně!).  
  ```csharp
  goto Navesti;
  // ...
  Navesti: 
      Console.WriteLine("Návěští");
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

- Skupina výkazů uzavřená v `{}`.  
  ```csharp
  {
      int a = 5;
      Console.WriteLine(a);
  }
  ```

#### **f) Ostatní výkazy**

- **`using`**: Automaticky uvolní prostředky (pro `IDisposable`).  
  ```csharp
  using (var reader = new StreamReader("soubor.txt")) {
      // Práce se souborem
  }
  ```
  
- **`checked`/`unchecked`**: Kontrola přetečení aritmetických operací.  
  ```csharp
  checked {
      int x = int.MaxValue + 1; // Vyvolá OverflowException
  }
  ```
  
- **`lock`**: Zajišťuje synchronizaci vláken.  
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

- Asynchronní volání (v metodách označených `async`).  
  ```csharp
  async Task NactiData() {
      var data = await HttpClient.GetStringAsync("url");
  }
  ```

---

### **4. Porovnání cyklů**

| **Cyklus**   | **Vhodné použití**                  | **Poznámka**                          |
|--------------|-------------------------------------|----------------------------------------|
| `for`        | Známý počet iterací                 | Ideální pro indexované kolekce         |
| `foreach`    | Práce s kolekcemi                   | Nevyžaduje index                       |
| `while`      | Neznámý počet iterací (podmínka na začátku) | Může být nekonečný                    |
| `do-while`   | Neznámý počet iterací (podmínka na konci) | Provede se minimálně jednou           |

---

### **5. Běžné chyby a tipy**

- **Zapomenutý `break` v `switch`**:  
  ```csharp
  case 1: 
      Console.WriteLine("1");
      break; // Bez break způsobí chybu kompilace!
  ```
  
- **Nekonečné cykly**:  
  ```csharp
  while (true) { 
      // Chybí podmínka pro ukončení
  }
  ```
  
- **Použití `goto`**: Lze použít pro opuštění vnořených cyklů, ale obecně se nedoporučuje.  
- **`using` pro ne-`IDisposable` typy**: Vyvolá chybu kompilace.

---

### **6. Ukázky kódu**

#### **Komplexní použití `try-catch`**

```csharp
try {
    int x = int.Parse("nečíslo");
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

### **7. Doporučené zdroje**

- Oficiální dokumentace: [Statements in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/statements-expressions-operators/statements)
- Kniha: *C# 10 in a Nutshell* (Albahari) – kapitola o řízení toku.
