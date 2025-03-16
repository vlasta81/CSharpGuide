
### **`Skip` a `SkipWhile`**  

Tyto metody slouží k **přeskočení prvků** v kolekci na základě **počtu** nebo **podmínky**. Jsou užitečné pro práci s podmnožinami dat, jako je stránkování nebo filtrace.

---

### **1. `Skip`**  

#### **Základní charakteristika**  

- **Účel**: Přeskočit zadaný počet prvků a vrátit zbývající.  
- **Syntaxe**:  
  ```csharp  
  IEnumerable<T> výsledek = kolekce.Skip(počet);  
  ```  
- **Parametr**:  
  - `počet`: Počet prvků k přeskočení (nesmí být záporný).  
- **Chování**:  
  - Pokud je `počet` větší než délka kolekce, vrátí prázdnou kolekci.  
  - Pokud je `počet = 0`, vrátí původní kolekci.  

#### **Příklady**  

```csharp  
List<int> čísla = new List<int> { 1, 2, 3, 4, 5 };  

// Přeskoč první 2 prvky:  
var výsledek = čísla.Skip(2); // {3, 4, 5}  

// Přeskoč více prvků, než kolik jich existuje:  
var prázdné = čísla.Skip(10); // {}  
```  

#### **Použití v praxi**  

- **Stránkování**:  
  ```csharp  
  int stránka = 2;  
  int velikostStránky = 10;  
  var data = kolekce.Skip((stránka - 1) * velikostStránky).Take(velikostStránky);  
  ```  

---

### **2. `SkipWhile`**  

#### **Základní charakteristika**  

- **Účel**: Přeskočit prvky, dokud je splněna podmínka. Jakmile podmínka selže, vrátí **všechny zbývající prvky**.  
- **Syntaxe**:  
  ```csharp  
  // Bez indexu:  
  IEnumerable<T> výsledek = kolekce.SkipWhile(prvek => podmínka);  

  // S indexem:  
  IEnumerable<T> výsledek = kolekce.SkipWhile((prvek, index) => podmínka);  
  ```  
- **Podmínka**: Lambda výraz vracející `bool`.  

#### **Příklady**  

```csharp  
List<int> čísla = new List<int> { 2, 4, 6, 7, 8, 10 };  

// Přeskoč sudá čísla, dokud není liché:  
var výsledek = čísla.SkipWhile(n => n % 2 == 0); // {7, 8, 10}  

// Přeskoč prvky s indexem menším než 3:  
var výsledek = čísla.SkipWhile((n, index) => index < 3); // {7, 8, 10}  
```  

#### **Klíčové vlastnosti**  

- Podmínka se vyhodnocuje **sekvenčně** – jakmile jednou selže, další prvky se nezkoumají.  
- Pokud **všechny prvky splňují podmínku**, vrátí prázdnou kolekci.  

---

### **Srovnání `Skip` a `SkipWhile`**  

| Vlastnost               | **`Skip`**                          | **`SkipWhile`**                      |  
|-------------------------|--------------------------------------|--------------------------------------|  
| **Podmínka**            | Přeskočí prvky podle **pozice**.     | Přeskočí prvky podle **podmínky**.   |  
| **Výstup**              | Všechny prvky po zadaném počtu.     | Všechny prvky po první nesplněné podmínce. |  
| **Použití**             | Stránkování, přesné pozice.         | Dynamické filtrování podle hodnot.   |  

---

### **Časté chyby**  

1. **Záměna `SkipWhile` s `Where`**:  
   - `SkipWhile` přestane filtrovat po první nesplněné podmínce, zatímco `Where` filtruje celou kolekci.  
  ```csharp  
  List<int> data = new List<int> { 1, 3, 5, 2, 4 };  
  var skipWhile = data.SkipWhile(n => n < 4); // {5, 2, 4} (přeskočí 1, 3)  
  var where = data.Where(n => n >= 4);        // {5, 4}  
  ```  

2. **Neplatný `počet` v `Skip`**:  
   - Záporné hodnoty vyvolají `ArgumentOutOfRangeException`.  

---

### **Tipy pro efektivní použití**  

- **Kombinace s `Take` pro přesné výřezy**:  
  ```csharp  
  var střed = kolekce.Skip(10).Take(5); // Prvky 11–15  
  ```  
- **Použití indexu v `SkipWhile`**:  
  ```csharp  
  // Přeskoč prvních 5 prvků:  
  var výsledek = kolekce.SkipWhile((_, index) => index < 5);  
  ```  
- **Optimalizace pro databáze (EF Core)**:  
  - `Skip` a `SkipWhile` se převádí na SQL `OFFSET` nebo podmínky.  

---

### **Shrnutí**  
- **`Skip`** – Přeskočí prvky podle **počtu**.  
- **`SkipWhile`** – Přeskočí prvky podle **podmínky** (až do prvního nesplnění).  
- Obojí podporuje **deferred execution** a integruje se s dalšími LINQ operátory.  

📖 **Zdroj**: [Microsoft Docs – Skip](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.skip) | [SkipWhile](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.skipwhile)
