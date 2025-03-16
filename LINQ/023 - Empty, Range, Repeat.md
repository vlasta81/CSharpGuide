
### **`Empty`, `Range` a `Repeat`**  

Tyto statické metody třídy `Enumerable` slouží k **generování sekvencí** s předdefinovaným obsahem. Jsou užitečné pro inicializaci kolekcí, testování nebo simulaci dat.

---

### **1. `Empty<TResult>`**  

#### **Základní charakteristika**  

- **Účel**: Vytvořit **prázdnou kolekci** specifikovaného typu `TResult`.  
- **Použití**: Když potřebujete vrátit prázdnou kolekci místo `null` (např. v metodách vracejících `IEnumerable<T>`).  
- **Výhoda**: Zabraňuje `NullReferenceException`.  

#### **Syntaxe**  

```csharp  
IEnumerable<TResult> prázdnáKolekce = Enumerable.Empty<TResult>();  
```  

#### **Příklady**  

```csharp  
var prázdnéŘetězce = Enumerable.Empty<string>(); // {}
var prázdnáČísla = Enumerable.Empty<int>();     // {}  
```  

---

### **2. `Range`**  

#### **Základní charakteristika**  

- **Účel**: Vygenerovat **sekvenci po sobě jdoucích celých čísel**.  
- **Parametry**:  
  - `start`: Počáteční hodnota (včetně).  
  - `count`: Počet generovaných čísel.  
- **Podmínka**: `count` musí být nezáporné číslo.  

#### **Syntaxe**  

```csharp  
IEnumerable<int> čísla = Enumerable.Range(start, count);  
```  

#### **Příklady**  

```csharp  
var čísla1Až10 = Enumerable.Range(1, 10);       // {1, 2, ..., 10}  
var zápornáČísla = Enumerable.Range(-5, 5);    // {-5, -4, -3, -2, -1}  
```  

#### **Využití**  

- Generování indexů pro cykly.  
- Vytváření testovacích dat:  
  ```csharp  
  var data = Enumerable.Range(1, 5).Select(i => new Product { Id = i });  
  ```  

---

### **3. `Repeat`**  

#### **Základní charakteristika**  

- **Účel**: Vytvořit kolekci s **opakujícím se prvkem** daný počet krát.  
- **Parametry**:  
  - `element`: Hodnota nebo objekt, který se bude opakovat.  
  - `count`: Počet opakování (musí být ≥ 0).  

#### **Syntaxe**  

```csharp  
IEnumerable<T> opakování = Enumerable.Repeat(prvek, počet);  
```  

#### **Příklady**  

```csharp  
var pětHvězd = Enumerable.Repeat("★", 5);       // {"★", "★", ..., "★"}  
var nuly = Enumerable.Repeat(0, 3);            // {0, 0, 0}  
```  

#### **Využití**  

- Inicializace kolekcí s výchozími hodnotami.  
- Simulace opakujících se událostí.  

---

### **Srovnání metod**  

| Metoda          | Výstup                                  | Typický Use Case                     |  
|-----------------|-----------------------------------------|--------------------------------------|  
| **`Empty`**     | Prázdná kolekce.                        | Návrat prázdné kolekce místo `null`. |  
| **`Range`**     | Sekvence čísel.                         | Generování testovacích indexů.       |  
| **`Repeat`**    | Kolekce s opakujícím se prvkem.         | Výchozí hodnoty nebo dummy data.     |  

---

### **Časté chyby**  

- **`Range` s `count = 0`**: Vrátí prázdnou kolekci (bez výjimky).  
- **`Repeat` s `count = 0`**: Vrátí prázdnou kolekci.  
- **Záporný `count`**: Vyvolá `ArgumentOutOfRangeException`.  

---

### **Tipy**  

- **Kombinace s `Select` pro komplexní data**:  
  ```csharp  
  var data = Enumerable.Range(1, 5)  
                       .Select(i => $"Uživatel {i}");  
  // {"Uživatel 1", ..., "Uživatel 5"}  
  ```  
- **`Repeat` s objekty**:  
  ```csharp  
  var osoby = Enumerable.Repeat(new Person { Jméno = "Jan" }, 3);  
  ```  
  Pozor: Všechny prvky odkazují na **stejný objekt** v paměti!  

📖 **Zdroj**: [Microsoft Docs – Empty](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.empty) | [Range](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.range) | [Repeat](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.repeat)
