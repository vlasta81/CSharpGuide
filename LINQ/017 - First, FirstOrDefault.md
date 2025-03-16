
### **`First` a `FirstOrDefault`**  

Tyto metody slouží k **výběru prvního prvku kolekce** nebo prvního prvku splňujícího podmínku. Liší se v chování, když žádný prvek neexistuje nebo podmínka není splněna.

---

#### **Základní charakteristika**  

| Vlastnost               | **`First`**                            | **`FirstOrDefault`**                   |
|-------------------------|----------------------------------------|----------------------------------------|
| **Účel**                | Vrátí první prvek/kvalifikující prvek. | Vrátí první prvek/kvalifikující prvek **nebo výchozí hodnotu**. |
| **Výjimka**             | Vyvolá `InvalidOperationException`, pokud neexistuje prvek nebo splňující podmínku. | **Nevyvolá výjimku** – vrátí `default(T)`. |
| **Výchozí hodnota**     | Není podporována.                      | `null` (pro reference), `0` (pro čísla), `default` (pro struct). |

---

### **Syntaxe**  

```csharp
// Bez podmínky:
T prvek = kolekce.First();
T prvek = kolekce.FirstOrDefault();

// S podmínkou:
T prvek = kolekce.First(x => x.Vlastnost == hodnota);
T prvek = kolekce.FirstOrDefault(x => x.Vlastnost == hodnota);
```

---

### **Klíčové vlastnosti**  

1. **Podmíněný výběr**:  
   - Lze kombinovat s libovolným lambda výrazem.  
   ```csharp
   var dospělý = osoby.First(p => p.Věk >= 18);
   ```

2. **Optimalizace pro databáze (EF Core)**:  
   - Při použití s `IQueryable` (např. Entity Framework) se převádí na SQL `TOP 1` s `WHERE`.  
   ```csharp
   var uživatel = dbContext.Users.FirstOrDefault(u => u.Email == "example@email.com");
   ```

3. **Výchozí hodnoty pro různé typy**:  
   - Pro `string`: `null`.  
   - Pro `int`: `0`.  
   - Pro `DateTime`: `01/01/0001 00:00:00`.  

---

### **Příklady použití**  

#### **1. Výběr prvního prvku bez podmínky**  

```csharp
List<int> čísla = new List<int> { 10, 20, 30 };
int první = čísla.First(); // 10
int prvníDefault = new List<int>().FirstOrDefault(); // 0
```

#### **2. Výběr s podmínkou**  

```csharp
List<string> jména = new List<string> { "Alice", "Bob", "Charlie" };
string začínáNaA = jména.FirstOrDefault(j => j.StartsWith("A")); // "Alice"
string začínáNaX = jména.FirstOrDefault(j => j.StartsWith("X")); // null
```

#### **3. Práce s objekty**  

```csharp
class Produkt { public string Název { get; set; } public bool JeSkladem { get; set; } }
List<Produkt> produkty = new List<Produkt> { /* ... */ };

Produkt skladem = produkty.FirstOrDefault(p => p.JeSkladem);
if (skladem != null) 
{
    Console.WriteLine($"První dostupný produkt: {skladem.Název}");
}
```

---

### **Časté chyby**  

1. **Použití `First` bez kontroly existence prvků**:  
   ```csharp
   var prázdnáKolekce = new List<int>();
   int hodnota = prázdnáKolekce.First(); // Vyvolá výjimku!
   ```

2. **Ignorování `default` hodnoty u `FirstOrDefault`**:  
   ```csharp
   string jméno = jména.FirstOrDefault(j => j.Length > 10);
   Console.WriteLine(jméno.ToUpper()); // Chyba, pokud je jméno null!
   ```

3. **Neefektivní dotazy u velkých kolekcí**:  
   - Pro velké kolekce preferujte `Where().FirstOrDefault()` místo `FirstOrDefault(podmínka)`.  

---

### **Tipy pro efektivní použití**  

- **Kontrola existence před `First`**:  
  ```csharp
  if (kolekce.Any(x => x.Vlastnost == hodnota)) 
  {
      var prvek = kolekce.First(x => x.Vlastnost == hodnota);
  }
  ```
- **Použití `FirstOrDefault` s null-conditional operátorem**:  
  ```csharp
  Console.WriteLine(jména.FirstOrDefault()?.ToUpper());
  ```
- **Kombinace s `OrderBy`**:  
  ```csharp
  var nejlevnější = produkty.OrderBy(p => p.Cena).First();
  ```

---

### **Shrnutí** 

- **`First`** – Pro situace, kdy **očekáváte existenci prvku** (jinak vyvolá výjimku).  
- **`FirstOrDefault`** – Pro **bezpečný přístup**, když absence prvku je validní stav.  
- Efektivně pracuje s databázemi díky převodu na optimalizované SQL dotazy.  

📖 **Zdroj**: [Microsoft Docs – First](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.first) | [FirstOrDefault](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.firstordefault)
