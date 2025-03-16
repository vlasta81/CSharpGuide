
### **`Take` a `TakeWhile`**  

Tyto metody slouží k **výběru podmnožiny prvků** z kolekce na základě **počtu** nebo **podmínky**. Jsou užitečné pro práci s omezenými datovými sadami, jako je stránkování nebo dynamický výběr.

---

### **1. `Take`**  

#### **Základní charakteristika**  

- **Účel**: Vybrat prvních `N` prvků z kolekce.  
- **Syntaxe**:  
  ```csharp  
  IEnumerable<T> výsledek = kolekce.Take(počet);  
  ```  
- **Parametr**:  
  - `počet`: Maximální počet prvků k vrácení (nesmí být záporný).  
- **Chování**:  
  - Pokud je `počet` větší než délka kolekce, vrátí všechny prvky.  
  - Pokud je `počet = 0`, vrátí prázdnou kolekci.  

#### **Příklady**  

```csharp  
List<int> čísla = new List<int> { 1, 2, 3, 4, 5 };  

// Vyber první 3 prvky:  
var výsledek = čísla.Take(3); // {1, 2, 3}  

// Vyber více prvků, než kolik jich existuje:  
var všechny = čísla.Take(10); // {1, 2, 3, 4, 5}  
```  

#### **Použití v praxi**  

- **Stránkování**:  
  ```csharp  
  int stránka = 1;  
  int velikostStránky = 5;  
  var data = kolekce.Skip((stránka - 1) * velikostStránky).Take(velikostStránky);  
  ```  

---

### **2. `TakeWhile`**  

#### **Základní charakteristika**  

- **Účel**: Vybrat prvky, **dokud je splněna podmínka**. Jakmile podmínka selže, výběr se zastaví.  
- **Syntaxe**:  
  ```csharp  
  // Bez indexu:  
  IEnumerable<T> výsledek = kolekce.TakeWhile(prvek => podmínka);  

  // S indexem:  
  IEnumerable<T> výsledek = kolekce.TakeWhile((prvek, index) => podmínka);  
  ```  
- **Podmínka**: Lambda výraz vracející `bool`.  

#### **Příklady**  

```csharp  
List<int> čísla = new List<int> { 2, 4, 6, 7, 8, 10 };  

// Vyber sudá čísla, dokud nenarazíš na liché:  
var výsledek = čísla.TakeWhile(n => n % 2 == 0); // {2, 4, 6}  

// Vyber prvky s indexem menším než 3:  
var výsledek = čísla.TakeWhile((n, index) => index < 3); // {2, 4, 6}  
```  

#### **Klíčové vlastnosti**  

- Podmínka se vyhodnocuje **sekvenčně** – jakmile jednou selže, další prvky se nezkoumají.  
- Pokud **všechny prvky splňují podmínku**, vrátí celou kolekci.  

---

### **Srovnání `Take` a `TakeWhile`**  

| Vlastnost               | **`Take`**                          | **`TakeWhile`**                      |  
|-------------------------|--------------------------------------|--------------------------------------|  
| **Podmínka**            | Vybere prvky podle **počtu**.        | Vybere prvky podle **podmínky**.     |  
| **Výstup**              | Prvních `N` prvků.                  | Prvky až do první nesplněné podmínky. |  
| **Použití**             | Stránkování, omezení výsledků.       | Dynamický výběr podle hodnot.        |  

---

### **Časté chyby**  

1. **Záměna `TakeWhile` s `Where`**:  
   - `TakeWhile` přestane vybírat po první nesplněné podmínce, zatímco `Where` filtruje celou kolekci.  
  ```csharp  
  List<int> data = new List<int> { 1, 3, 5, 2, 4 };  
  var takeWhile = data.TakeWhile(n => n < 4); // {1, 3}  
  var where = data.Where(n => n < 4);         // {1, 3, 2}  
  ```  

2. **Neplatný `počet` v `Take`**:  
   - Záporné hodnoty vyvolají `ArgumentOutOfRangeException`.  

---

### **Tipy pro efektivní použití**  
- **Kombinace s `Skip` pro stránkování**:  
  ```csharp  
  var stránka = kolekce.Skip(20).Take(10); // Prvky 21–30  
  ```  
- **Použití indexu v `TakeWhile`**:  
  ```csharp  
  // Vyber prvních 5 prvků:  
  var výsledek = kolekce.TakeWhile((_, index) => index < 5);  
  ```  
- **Optimalizace pro EF Core**:  
  - `Take` se převádí na SQL `TOP` nebo `LIMIT`.  

---

### **Shrnutí**  

- **`Take`** – Vybere prvky podle **počtu**.  
- **`TakeWhile`** – Vybere prvky podle **podmínky** (až do prvního nesplnění).  
- Obojí podporuje **deferred execution** a integruje se s dalšími LINQ operátory.  

📖 **Zdroj**: [Microsoft Docs – Take](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.take) | [TakeWhile](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.takewhile)
