
### **1. Definice v�kazu**

- **V�kaz** je instrukce, kter� prov�d� akci (nap�. p�i�azen� hodnoty, vol�n� metody, ��zen� toku programu).  
- Ka�d� v�kaz kon�� st�edn�kem `;` (krom� blok� `{}`).

---

### **2. Kategorie v�kaz�**

#### **a) Deklara�n� v�kazy**

- Deklaruj� prom�nn�, konstanty nebo funkce.  
  ```csharp
  int x = 10;
  const double PI = 3.14;
  ```

#### **b) V�razov� v�kazy**

- Vyhodnocen� v�razu s vedlej��m ��inkem (nap�. p�i�azen�, vol�n� metody).  
  ```csharp
  x = 5 + 3;
  Console.WriteLine("Ahoj!");
  ```

#### **c) ��zen� toku programu**

##### **Podm�n�n� v�kazy**  

- **`if`-`else`**:  
  ```csharp
  if (vek >= 18) {
      Console.WriteLine("Dosp�l�");
  } else {
      Console.WriteLine("Nezletil�");
  }
  ```
  
- **`switch`**:  
  ```csharp
  switch (den) {
      case DayOfWeek.Monday: 
          Console.WriteLine("Pond�l�"); 
          break;
      default: 
          Console.WriteLine("Jin� den"); 
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
      // K�d
  }
  ```
  
- **`do-while`**:  
  ```csharp
  do {
      // K�d
  } while (podminka);
  ```

##### **Skokov� v�kazy**  

- **`break`**: Ukon�� cyklus nebo `switch`.  
- **`continue`**: P�esko�� aktu�ln� iteraci cyklu.  
- **`return`**: Vr�t� hodnotu z metody.  
- **`goto`**: P�esko�� na ozna�en� v�kaz (pou��vejte v�jime�n�!).  
  ```csharp
  goto Navesti;
  // ...
  Navesti: 
      Console.WriteLine("N�v�t�");
  ```

#### **d) V�jimkov� v�kazy**

- **`try`-`catch`-`finally`**:  
  ```csharp
  try {
      // Rizikov� k�d
  } catch (Exception ex) {
      Console.WriteLine(ex.Message);
  } finally {
      // �klidov� k�d
  }
  ```
  
- **`throw`**: Vyvol� v�jimku.  
  ```csharp
  throw new InvalidOperationException("Chyba!");
  ```

#### **e) Blokov� v�kazy**

- Skupina v�kaz� uzav�en� v `{}`.  
  ```csharp
  {
      int a = 5;
      Console.WriteLine(a);
  }
  ```

#### **f) Ostatn� v�kazy**

- **`using`**: Automaticky uvoln� prost�edky (pro `IDisposable`).  
  ```csharp
  using (var reader = new StreamReader("soubor.txt")) {
      // Pr�ce se souborem
  }
  ```
  
- **`checked`/`unchecked`**: Kontrola p�ete�en� aritmetick�ch operac�.  
  ```csharp
  checked {
      int x = int.MaxValue + 1; // Vyvol� OverflowException
  }
  ```
  
- **`lock`**: Zaji��uje synchronizaci vl�ken.  
  ```csharp
  lock (lockObject) {
      // Kritick� sekce
  }
  ```

---

### **3. Speci�ln� v�kazy**

#### **`yield`**  

- Pou��v� se v iter�torech pro postupn� vracen� hodnot.  
  ```csharp
  public IEnumerable<int> GenerujCisla() {
      for (int i = 0; i < 5; i++) {
          yield return i;
      }
  }
  ```

#### **`await`**  

- Asynchronn� vol�n� (v metod�ch ozna�en�ch `async`).  
  ```csharp
  async Task NactiData() {
      var data = await HttpClient.GetStringAsync("url");
  }
  ```

---

### **4. Porovn�n� cykl�**

| **Cyklus**   | **Vhodn� pou�it�**                  | **Pozn�mka**                          |
|--------------|-------------------------------------|----------------------------------------|
| `for`        | Zn�m� po�et iterac�                 | Ide�ln� pro indexovan� kolekce         |
| `foreach`    | Pr�ce s kolekcemi                   | Nevy�aduje index                       |
| `while`      | Nezn�m� po�et iterac� (podm�nka na za��tku) | M��e b�t nekone�n�                    |
| `do-while`   | Nezn�m� po�et iterac� (podm�nka na konci) | Provede se minim�ln� jednou           |

---

### **5. B�n� chyby a tipy**

- **Zapomenut� `break` v `switch`**:  
  ```csharp
  case 1: 
      Console.WriteLine("1");
      break; // Bez break zp�sob� chybu kompilace!
  ```
  
- **Nekone�n� cykly**:  
  ```csharp
  while (true) { 
      // Chyb� podm�nka pro ukon�en�
  }
  ```
  
- **Pou�it� `goto`**: Lze pou��t pro opu�t�n� vno�en�ch cykl�, ale obecn� se nedoporu�uje.  
- **`using` pro ne-`IDisposable` typy**: Vyvol� chybu kompilace.

---

### **6. Uk�zky k�du**

#### **Komplexn� pou�it� `try-catch`**

```csharp
try {
    int x = int.Parse("ne��slo");
} catch (FormatException) {
    Console.WriteLine("Neplatn� form�t!");
} catch (Exception ex) {
    Console.WriteLine($"Obecn� chyba: {ex.Message}");
} finally {
    Console.WriteLine("Konec zpracov�n�.");
}
```

#### **Iter�tor s `yield`**

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

### **7. Doporu�en� zdroje**

- Ofici�ln� dokumentace: [Statements in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/statements-expressions-operators/statements)
- Kniha: *C# 10 in a Nutshell* (Albahari) � kapitola o ��zen� toku.
