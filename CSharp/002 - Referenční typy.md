
### **1. Definice referen�n�ch typ�**

- Ukl�daj� **odkaz (referenci)** na pam�ovou adresu, kde jsou data ulo�ena (na hald� *heap*).
- Kop�rov�n�m vznik� **kopie reference** (ne kopie dat � ob� prom�nn� odkazuj� na stejn� objekt).
- Maj� **dynamickou velikost** a podporuj� d�di�nost a polymorfismus.

---

### **2. Kategorie referen�n�ch typ�**

#### **a) T��dy (`class`)**  

- Z�kladn� referen�n� typ s podporou d�di�nosti, konstruktor�, metod a vlastnost�.  
- **P��klad**:  
  ```csharp
  public class Uzivatel {
      public string Jmeno { get; set; }
      public int Vek { get; set; }
      
      public void PredstavSe() {
          Console.WriteLine($"Jsem {Jmeno}, {Vek} let.");
      }
  }
  ```

#### **b) Rozhran� (`interface`)**  

- Definuje **kontrakt** (metody, vlastnosti, ud�losti), kter� t��dy implementuj�.  
- **P��klad**:  
  ```csharp
  public interface IUlozitelny {
      void UlozDoSouboru(string cesta);
      string NactiZeSouboru(string cesta);
  }
  ```

#### **c) Deleg�ti (`delegate`)**  

- Typ pro ukl�d�n� odkaz� na metody (podobn� ukazatel�m na funkce).  
- **P��klad**:  
  ```csharp
  public delegate void Operace(int x, int y);
  
  // Pou�it�
  Operace soucet = (a, b) => Console.WriteLine(a + b);
  soucet(5, 3); // V�stup: 8
  ```

#### **d) Pole (`Array`)**  

- Kolekce prvk� stejn�ho typu.  
- **P��klad**:  
  ```csharp
  int[] cisla = new int[] { 1, 2, 3 };
  string[] jmena = new string[5];
  ```

#### **e) �et�zce (`string`)**  

- Nem�niteln� (*immutable*) sekvence znak�.  
- **P��klad**:  
  ```csharp
  string text = "Ahoj sv�te!";
  string upravenyText = text.Replace("sv�te", "C#"); // Vytvo�� nov� �et�zec.
  ```

#### **f) Dynamick� typy (`dynamic`)**  

- Typ kontrolovan� za b�hu (*runtime*), ne p�i kompilaci.  
- **P��klad**:  
  ```csharp
  dynamic promenna = 10;
  promenna = "text"; // ��dn� chyba p�i kompilaci.
  ```

---

### **3. Kl��ov� vlastnosti**

- **V�choz� hodnota**: `null` (neodkazuje na ��dn� objekt).  
- **�ivotnost**: Spravov�na garbage collectorem (GC) � uvoln�ny, kdy� nejsou odkazy.  
- **Nullabilita**: Ve v�choz�m stavu mohou b�t `null` (lze omezit pomoc� *nullable reference types* v C# 8.0+).  
- **Identita**: Dv� prom�nn� odkazuj�c� na stejn� objekt sd�l� data.

---

### **4. Nullable reference typy (C# 8.0+)**

- Explicitn� ozna�en�, zda m��e b�t prom�nn� `null`.  
- **P��klad**:  
  ```csharp
  string? muzeBytNull = null;
  string nesmiBytNull = "C#"; // Kompil�tor hl�d� p�i�azen� null.
  ```

---

### **5. Rozd�ly oproti hodnotov�m typ�m**

| **Vlastnost**       | **Referen�n� typy**      | **Hodnotov� typy**       |
|----------------------|--------------------------|--------------------------|
| Ukl�d�n�             | Reference na heap        | P��mo na stack/heap      |
| Kop�rov�n�           | M�lk� kopie (reference)  | Hlubok� kopie            |
| V�choz� hodnota      | `null`                   | `0`, `false` apod.       |
| V�kon                | Pomalej�� (GC overhead)  | Rychlej�� p��stup        |
| D�di�nost            | Podporuje                | Nepodporuje              |

---

### **6. D�le�it� koncepty**

#### **a) Garbage Collector (GC)**  

- Automaticky uvol�uje pam� pro objekty bez referenc�.  
- **Pozor**: Nevhodn� pou�it� m��e zp�sobit �niky pam�ti (nap�. ud�losti s neodhl�en�mi handlery).

#### **b) M�lk� vs. hlubok� kopie**  

- **M�lk� kopie**: Kop�ruje pouze reference (stejn� objekt).  
- **Hlubok� kopie**: Vytvo�� nov� objekt s kopi� v�ech dat.  
  ```csharp
  // M�lk� kopie
  Uzivatel u1 = new Uzivatel { Jmeno = "Jan" };
  Uzivatel u2 = u1; // Oba odkazuj� na stejn� objekt!
  ```

#### **c) Immutabilita**  

- Objekty nelze m�nit po vytvo�en� (nap�. `string`, `Tuple`).  
- V�hoda: Bezpe�nost ve vl�knech a p�edv�dateln� stav.

---

### **7. Doporu�en� pou�it�**

- **T��dy**: Pro komplexn� objekty s chov�n�m (metodami) a stavem.  
- **Rozhran�**: Pro abstrakci a vynucen� kontraktu mezi komponentami.  
- **Deleg�ti**: Pro zp�tn� vol�n� (*callbacks*) nebo ud�losti.  
- **Pole/Listy**: Pro dynamick� kolekce dat.

---

### **8. Pozor na**

- **Nekone�n� reference**: Cyklick� z�vislosti mezi objekty (komplikace pro GC).  
- **Neo�ek�van� zm�ny**: M�lk� kopie mohou zp�sobit vedlej�� efekty.  
- **Memory leaks**: Nap�. ud�losti s dlouho �ij�c�mi objekty.  
- **Boxing**: Zbyte�n� p�evody hodnotov�ch typ� na `object`.

---

### **9. Uk�zka k�du**

```csharp
// T��da s implementac� rozhran�
public class SouborovyManager : IUlozitelny {
    public void UlozDoSouboru(string cesta) {
        File.WriteAllText(cesta, "Data");
    }
    
    public string NactiZeSouboru(string cesta) {
        return File.ReadAllText(cesta);
    }
}

// Deleg�t a ud�lost
public class Timer {
    public event Action? CasVyprsel;
    
    public void Start() {
        // Simulace �ek�n�
        CasVyprsel?.Invoke();
    }
}

// Pou�it�
Timer timer = new Timer();
timer.CasVyprsel += () => Console.WriteLine("Hotovo!");
timer.Start();
```

---

### **10. Tipy pro efektivn� pr�ci**

- Pro nem�nn� datov� struktury pou�ijte `record class` (C# 9.0+).  
- Pro pr�ci s kolekcemi preferujte generick� typy (`List<T>`, `Dictionary<TKey, TValue>`).  
- Vyhn�te se zbyte�n�m alokac�m v kritick�ch ��stech k�du (nap�. ve smy�k�ch).  
- Pou��vejte `StringBuilder` pro �ast� �pravy �et�zc�.
