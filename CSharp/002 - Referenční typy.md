
### **1. Definice referenèních typù**

- Ukládají **odkaz (referenci)** na pamìovou adresu, kde jsou data uložena (na haldì *heap*).
- Kopírováním vzniká **kopie reference** (ne kopie dat – obì promìnné odkazují na stejný objekt).
- Mají **dynamickou velikost** a podporují dìdiènost a polymorfismus.

---

### **2. Kategorie referenèních typù**

#### **a) Tøídy (`class`)**  

- Základní referenèní typ s podporou dìdiènosti, konstruktorù, metod a vlastností.  
- **Pøíklad**:  
  ```csharp
  public class Uzivatel {
      public string Jmeno { get; set; }
      public int Vek { get; set; }
      
      public void PredstavSe() {
          Console.WriteLine($"Jsem {Jmeno}, {Vek} let.");
      }
  }
  ```

#### **b) Rozhraní (`interface`)**  

- Definuje **kontrakt** (metody, vlastnosti, události), který tøídy implementují.  
- **Pøíklad**:  
  ```csharp
  public interface IUlozitelny {
      void UlozDoSouboru(string cesta);
      string NactiZeSouboru(string cesta);
  }
  ```

#### **c) Delegáti (`delegate`)**  

- Typ pro ukládání odkazù na metody (podobné ukazatelùm na funkce).  
- **Pøíklad**:  
  ```csharp
  public delegate void Operace(int x, int y);
  
  // Použití
  Operace soucet = (a, b) => Console.WriteLine(a + b);
  soucet(5, 3); // Výstup: 8
  ```

#### **d) Pole (`Array`)**  

- Kolekce prvkù stejného typu.  
- **Pøíklad**:  
  ```csharp
  int[] cisla = new int[] { 1, 2, 3 };
  string[] jmena = new string[5];
  ```

#### **e) Øetìzce (`string`)**  

- Nemìnitelné (*immutable*) sekvence znakù.  
- **Pøíklad**:  
  ```csharp
  string text = "Ahoj svìte!";
  string upravenyText = text.Replace("svìte", "C#"); // Vytvoøí nový øetìzec.
  ```

#### **f) Dynamické typy (`dynamic`)**  

- Typ kontrolovaný za bìhu (*runtime*), ne pøi kompilaci.  
- **Pøíklad**:  
  ```csharp
  dynamic promenna = 10;
  promenna = "text"; // Žádná chyba pøi kompilaci.
  ```

---

### **3. Klíèové vlastnosti**

- **Výchozí hodnota**: `null` (neodkazuje na žádný objekt).  
- **Životnost**: Spravována garbage collectorem (GC) – uvolnìny, když nejsou odkazy.  
- **Nullabilita**: Ve výchozím stavu mohou být `null` (lze omezit pomocí *nullable reference types* v C# 8.0+).  
- **Identita**: Dvì promìnné odkazující na stejný objekt sdílí data.

---

### **4. Nullable reference typy (C# 8.0+)**

- Explicitní oznaèení, zda mùže být promìnná `null`.  
- **Pøíklad**:  
  ```csharp
  string? muzeBytNull = null;
  string nesmiBytNull = "C#"; // Kompilátor hlídá pøiøazení null.
  ```

---

### **5. Rozdíly oproti hodnotovým typùm**

| **Vlastnost**       | **Referenèní typy**      | **Hodnotové typy**       |
|----------------------|--------------------------|--------------------------|
| Ukládání             | Reference na heap        | Pøímo na stack/heap      |
| Kopírování           | Mìlká kopie (reference)  | Hluboká kopie            |
| Výchozí hodnota      | `null`                   | `0`, `false` apod.       |
| Výkon                | Pomalejší (GC overhead)  | Rychlejší pøístup        |
| Dìdiènost            | Podporuje                | Nepodporuje              |

---

### **6. Dùležité koncepty**

#### **a) Garbage Collector (GC)**  

- Automaticky uvolòuje pamì pro objekty bez referencí.  
- **Pozor**: Nevhodné použití mùže zpùsobit úniky pamìti (napø. události s neodhlášenými handlery).

#### **b) Mìlká vs. hluboká kopie**  

- **Mìlká kopie**: Kopíruje pouze reference (stejný objekt).  
- **Hluboká kopie**: Vytvoøí nový objekt s kopií všech dat.  
  ```csharp
  // Mìlká kopie
  Uzivatel u1 = new Uzivatel { Jmeno = "Jan" };
  Uzivatel u2 = u1; // Oba odkazují na stejný objekt!
  ```

#### **c) Immutabilita**  

- Objekty nelze mìnit po vytvoøení (napø. `string`, `Tuple`).  
- Výhoda: Bezpeènost ve vláknech a pøedvídatelný stav.

---

### **7. Doporuèené použití**

- **Tøídy**: Pro komplexní objekty s chováním (metodami) a stavem.  
- **Rozhraní**: Pro abstrakci a vynucení kontraktu mezi komponentami.  
- **Delegáti**: Pro zpìtné volání (*callbacks*) nebo události.  
- **Pole/Listy**: Pro dynamické kolekce dat.

---

### **8. Pozor na**

- **Nekoneèné reference**: Cyklické závislosti mezi objekty (komplikace pro GC).  
- **Neoèekávané zmìny**: Mìlké kopie mohou zpùsobit vedlejší efekty.  
- **Memory leaks**: Napø. události s dlouho žijícími objekty.  
- **Boxing**: Zbyteèné pøevody hodnotových typù na `object`.

---

### **9. Ukázka kódu**

```csharp
// Tøída s implementací rozhraní
public class SouborovyManager : IUlozitelny {
    public void UlozDoSouboru(string cesta) {
        File.WriteAllText(cesta, "Data");
    }
    
    public string NactiZeSouboru(string cesta) {
        return File.ReadAllText(cesta);
    }
}

// Delegát a událost
public class Timer {
    public event Action? CasVyprsel;
    
    public void Start() {
        // Simulace èekání
        CasVyprsel?.Invoke();
    }
}

// Použití
Timer timer = new Timer();
timer.CasVyprsel += () => Console.WriteLine("Hotovo!");
timer.Start();
```

---

### **10. Tipy pro efektivní práci**

- Pro nemìnné datové struktury použijte `record class` (C# 9.0+).  
- Pro práci s kolekcemi preferujte generické typy (`List<T>`, `Dictionary<TKey, TValue>`).  
- Vyhnìte se zbyteèným alokacím v kritických èástech kódu (napø. ve smyèkách).  
- Používejte `StringBuilder` pro èasté úpravy øetìzcù.
