
### **`Single` a `SingleOrDefault`**  

Tyto metody slouží k **výběru jediného prvku** z kolekce, který splňuje podmínku. Jsou užitečné pro situace, kdy očekáváme **právě jeden výsledek**, a chtěli bychom detekovat neočekávané duplicity nebo chybějící data.

---

#### **Základní charakteristika**  

| Vlastnost               | **`Single`**                           | **`SingleOrDefault`**                  |
|-------------------------|----------------------------------------|----------------------------------------|
| **Účel**                | Vrátí **jediný prvek** splňující podmínku. | Vrátí **jediný prvek** splňující podmínku **nebo výchozí hodnotu**, pokud není nalezen. |
| **Výjimka**             | Vyvolá `InvalidOperationException`, pokud: <br> - Kolekce je prázdná, <br> - Více prvků splňuje podmínku. | Vyvolá `InvalidOperationException`, pokud **více prvků splňuje podmínku**.<br>Vrátí `default(T)`, pokud není nalezen žádný prvek. |
| **Výchozí hodnota**     | Není podporována.                      | `null` (pro reference), `0` (pro čísla), `default` (pro struct). |

---

### **Syntaxe**  

```csharp
// Bez podmínky (pouze pro kolekce s jedním prvkem):
T prvek = kolekce.Single();
T prvek = kolekce.SingleOrDefault();

// S podmínkou:
T prvek = kolekce.Single(x => x.Vlastnost == hodnota);
T prvek = kolekce.SingleOrDefault(x => x.Vlastnost == hodnota);
```

---

### **Klíčové vlastnosti**  

1. **Přísná kontrola jednoznačnosti**:  
   - Metody **zkontrolují, že existuje maximálně jeden prvek** splňující podmínku.  
   - Pokud je nalezeno více prvků, vždy vyvolají výjimku.  

2. **Optimalizace pro databáze (EF Core)**:  
   - Při použití s `IQueryable` se převádí na SQL `TOP 2` k ověření jednoznačnosti.  
   ```csharp
   var uživatel = dbContext.Users.Single(u => u.Id == 100); // Generuje SQL s TOP 2
   ```

3. **Výkon**:  
   - Pro neindexovatelné kolekce (`IEnumerable<T>`) prochází celou sekvenci, aby ověřily jednoznačnost – **O(n) složitost**.  

---

### **Příklady použití**  

#### **1. Bez podmínky (pouze pro kolekce s jedním prvkem)**  

```csharp
List<int> jednoČíslo = new List<int> { 42 };
int hodnota = jednoČíslo.Single(); // 42

List<string> prázdná = new List<string>();
string výchozí = prázdná.SingleOrDefault(); // null
```

#### **2. S podmínkou**  

```csharp
List<string> jména = new List<string> { "Alice", "Bob", "Alice" };

// Vyvolá výjimku, protože "Alice" se vyskytuje vícekrát:
string alice = jména.Single(j => j == "Alice");

// Vyvolá výjimku (i když není prvek):
string neexistující = jména.SingleOrDefault(j => j == "Eve"); // null
```

#### **3. Práce s objekty**  

```csharp
class Uživatel { public int Id { get; set; } public string Email { get; set; } }
List<Uživatel> uživatelé = new List<Uživatel> { /* ... */ };

// Očekáváme, že email je unikátní:
Uživatel admin = uživatelé.Single(u => u.Email == "admin@example.com");
```

---

### **Časté chyby**  

1. **Použití na kolekce s více prvky bez podmínky**:  
   ```csharp
   List<int> čísla = new List<int> { 1, 2 };
   int hodnota = čísla.Single(); // Vyvolá výjimku!
   ```

2. **Ignorování výjimky u `SingleOrDefault` při více prvcích**:  
   ```csharp
   List<int> duplikáty = new List<int> { 5, 5 };
   int hodnota = duplikáty.SingleOrDefault(); // Vyvolá výjimku!
   ```

3. **Záměna s `First`/`FirstOrDefault`**:  
   - `Single` kontroluje jednoznačnost, zatímco `First` vrátí první prvek bez ohledu na počet.  

---

### **Tipy pro efektivní použití**  

- **Používejte `Single` pouze pro unikátní klíče** (např. primární klíče v DB).  
- **Kontrolujte existenci pomocí `Any` před voláním `Single`**:  
  ```csharp
  if (uživatelé.Any(u => u.Email == "test@example.com")) 
  {
      var uživatel = uživatelé.Single(u => u.Email == "test@example.com");
  }
  ```
- **Pro neunikátní data preferujte `First`/`FirstOrDefault`**.  

---

### **Shrnutí**  

- **`Single`** – Pro **garantovaně unikátní výsledky** (vyžaduje přesně jeden prvek).  
- **`SingleOrDefault`** – Pro **unikátní výsledky nebo `default`**, pokud prvek neexistuje.  
- **Vždy vyvolá výjimku při více prvcích** – zajišťuje integritu dat.  

📖 **Zdroj**: [Microsoft Docs – Single](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.single) | [SingleOrDefault](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.singleordefault)
