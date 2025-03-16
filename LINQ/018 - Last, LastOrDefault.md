
### **`Last` a `LastOrDefault`**  

Tyto metody slouží k **výběru posledního prvku kolekce** nebo posledního prvku splňujícího podmínku. Chovají se podobně jako `First` a `FirstOrDefault`, ale pracují s koncem sekvence.

---

#### **Základní charakteristika**  

| Vlastnost               | **`Last`**                             | **`LastOrDefault`**                   |
|-------------------------|----------------------------------------|----------------------------------------|
| **Účel**                | Vrátí poslední prvek/kvalifikující prvek. | Vrátí poslední prvek/kvalifikující prvek **nebo výchozí hodnotu**. |
| **Výjimka**             | Vyvolá `InvalidOperationException`, pokud neexistuje prvek nebo splňující podmínku. | **Nevyvolá výjimku** – vrátí `default(T)`. |
| **Výchozí hodnota**     | Není podporována.                      | `null` (pro reference), `0` (pro čísla), `default` (pro struct). |

---

### **Syntaxe**  

```csharp
// Bez podmínky:
T prvek = kolekce.Last();
T prvek = kolekce.LastOrDefault();

// S podmínkou:
T prvek = kolekce.Last(x => x.Vlastnost == hodnota);
T prvek = kolekce.LastOrDefault(x => x.Vlastnost == hodnota);
```

---

### **Klíčové vlastnosti**  

1. **Podmíněný výběr**:  
   - Lze kombinovat s libovolným lambda výrazem.  
   ```csharp
   var posledníSouhláska = písmena.Last(c => !"aeiou".Contains(c));
   ```

2. **Optimalizace pro databáze (EF Core)**:  
   - Při použití s `IQueryable` se převádí na SQL `ORDER BY ... DESC` s `TOP 1` a `WHERE`.  
   ```csharp
   var posledníObjednávka = dbContext.Orders.LastOrDefault(o => o.CustomerId == 100);
   ```

3. **Výkon**:  
   - Pro neindexovatelné kolekce (např. `IEnumerable<T>`) iteruje celou sekvenci – **O(n) složitost**.  
   - Pro `List<T>` nebo `Array` je rychlejší (přístup k poslednímu prvku přes index).

---

### **Příklady použití**  

#### **1. Výběr posledního prvku bez podmínky**  

```csharp
List<int> čísla = new List<int> { 10, 20, 30 };
int poslední = čísla.Last(); // 30
int posledníDefault = new List<int>().LastOrDefault(); // 0
```

#### **2. Výběr s podmínkou**  

```csharp
List<string> slova = new List<string> { "apple", "banana", "cherry", "date" };
string posledníDlouhé = slova.LastOrDefault(s => s.Length > 5); // "banana"
string neexistující = slova.LastOrDefault(s => s.StartsWith("x")); // null
```

#### **3. Práce s objekty**  

```csharp
class Transakce { public decimal Částka { get; set; } public DateTime Datum { get; set; } }
List<Transakce> transakce = new List<Transakce> { /* ... */ };

Transakce posledníVysoká = transakce.LastOrDefault(t => t.Částka > 1000);
```

---

### **Časté chyby**  

1. **Použití `Last` na prázdnou kolekci**:  
   ```csharp
   var prázdná = new List<string>();
   string hodnota = prázdná.Last(); // Vyvolá výjimku!
   ```

2. **Ignorování `default` hodnoty u `LastOrDefault`**:  
   ```csharp
   int posledníLiché = čísla.LastOrDefault(n => n % 2 == 1);
   Console.WriteLine(posledníLiché * 2); // Chyba, pokud je posledníLiché = 0 (default)!
   ```

3. **Neefektivita u velkých kolekcí**:  
   - Pro `IEnumerable<T>` bez indexace může `Last` procházet celou kolekci.  

---

### **Tipy pro efektivní použití**  

- **Kombinace s `OrderBy` pro databázové dotazy**:  
  ```csharp
  var posledníUživatel = dbContext.Users.OrderBy(u => u.Id).LastOrDefault();
  ```
- **Kontrola existence prvku**:  
  ```csharp
  if (kolekce.Any(x => x.Vlastnost == hodnota)) 
  {
      var prvek = kolekce.Last(x => x.Vlastnost == hodnota);
  }
  ```
- **Výchozí hodnota pro ne-nullable typy**:  
  ```csharp
  int poslední = čísla.DefaultIfEmpty(-1).Last();
  ```

---

### **Shrnutí**  

- **`Last`** – Pro **garantovaně neprázdné kolekce** nebo podmínky, které vždy splní alespoň jeden prvek.  
- **`LastOrDefault`** – Pro **bezpečný přístup**, když může kolekce nebo výsledek podmínky být prázdný.  
- Efektivně spolupracuje s EF Core (převod na SQL).  

📖 **Zdroj**: [Microsoft Docs – Last](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.last) | [LastOrDefault](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.lastordefault)
