
### **`All` a `Any`**  
`All` a `Any` jsou **metody rozšíření** v LINQ sloužící k **kontrolě podmínek** nad prvky kolekce. Jsou užitečné pro validaci dat, filtraci nebo logické operace.

---

#### **Základní přehled**  

| Metoda | Popis                                                                 | Vrácená hodnota pro prázdnou kolekci |
|--------|-----------------------------------------------------------------------|--------------------------------------|
| `All`  | Kontroluje, zda **všechny prvky** splňují podmínku.                   | `true`                               |
| `Any`  | Kontroluje, zda **alespoň jeden prvek** splňuje podmínku (nebo zda je kolekce **neprázdná**). | `false` (s podmínkou) / `false` (bez podmínky) |

---

### **Metoda `All`**  

#### **Syntaxe**  

```csharp
bool result = kolekce.All(predikát);
```

#### **Příklady**  

1. **Kontrola všech prvků**:  
   ```csharp
   List<int> numbers = new List<int> { 2, 4, 6, 8 };
   bool areAllEven = numbers.All(n => n % 2 == 0); // true
   ```

2. **Prázdná kolekce**:  
   ```csharp
   List<string> emptyList = new List<string>();
   bool result = emptyList.All(s => s.Length > 0); // true
   ```

#### **Upozornění**  

- Pokud je kolekce prázdná, `All` vrací `true` (i když podmínka není splněna!).  
- Vhodné pro **validace** (např. „jsou všechny hodnoty kladné?“).

---

### **Metoda `Any`**  

#### **Syntaxe**  

```csharp
// S podmínkou:
bool result = kolekce.Any(predikát);

// Bez podmínky (kontrola, zda je kolekce neprázdná):
bool hasElements = kolekce.Any();
```

#### **Příklady**  

1. **Kontrola existence prvku**:  
   ```csharp
   List<int> numbers = new List<int> { 1, 3, 5, 7 };
   bool hasEvenNumber = numbers.Any(n => n % 2 == 0); // false
   ```

2. **Kontrola neprázdnosti kolekce**:  
   ```csharp
   List<string> names = new List<string> { "Alice", "Bob" };
   bool hasData = names.Any(); // true
   ```

#### **Upozornění**  

- Pokud je kolekce prázdná, `Any` vrací `false` (bez ohledu na podmínku).  
- Pro kontrolu existence prvků **vždy preferujte `Any()` před `Count() > 0`** (efektivnější u velkých kolekcí).

---

### **Klíčové rozdíly**  

| Vlastnost               | `All`                               | `Any`                               |
|-------------------------|-------------------------------------|-------------------------------------|
| **Podmínka**            | Musí splnit **všechny prvky**.      | Stačí **alespoň jeden prvek**.      |
| **Prázdná kolekce**     | Vrací `true`.                       | Vrací `false` (s i bez podmínky).   |
| **Použití**            | Validace dat.                       | Kontrola existence nebo neprázdnosti. |

---

### **Časté scénáře použití**  

1. **Validace formulářových dat**:  
   ```csharp
   bool isFormValid = formFields.All(field => !string.IsNullOrEmpty(field.Value));
   ```

2. **Optimalizace dotazů**:  
   ```csharp
   // Pokud neexistují uživatelé nad 18 let, přeskoči další logiku
   if (users.Any(u => u.Age > 18)) 
   {
       // ...
   }
   ```

3. **Kombinace s `Where`**:  
   ```csharp
   var activeOrders = orders.Where(o => o.IsActive);
   bool hasHighPriority = activeOrders.Any(o => o.Priority == Priority.High);
   ```

---

### **Tipy a varování**  

- **`Any()` vs `Count() > 0`**:  
  ```csharp
  // Špatně: Projde celou kolekci
  if (users.Count() > 0) { ... }

  // Správně: Zastaví se u prvního prvku
  if (users.Any()) { ... }
  ```

- **Negace `All` a `Any`**:  
  - `!kolekce.All(...)` ≠ `kolekce.Any(...)`.  
  - Příklad:  
    ```csharp
    // "Ne všichni uživatelé jsou dospělí" ≠ "Existuje alespoň jeden nedospělý"
    if (!users.All(u => u.Age >= 18)) { ... }
    ```

---

### **Cvičení**  

1. Napište dotaz, který zkontroluje, zda všechny řetězce v kolekci mají délku větší než 3.  
2. Pomocí `Any` ověřte, zda v seznamu produktů existuje alespoň jeden s cenou nad 1000 Kč.  
3. Kombinujte `Where` a `All`: Najděte všechny objednávky, kde všechny položky jsou skladem.  

---

### **Shrnutí**  

- `All`: **Univerzální validace** – podmínka musí platit pro všechny prvky.  
- `Any`: **Kontrola existence** – stačí jeden prvek nebo neprázdná kolekce.  
- Efektivita: `Any()` je rychlejší než `Count() > 0`.  

📖 **Zdroj**: [Microsoft Docs – All](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.all) | [Microsoft Docs – Any](https://learn.microsoft.com/cs-cz/dotnet/api/system.linq.enumerable.any)
