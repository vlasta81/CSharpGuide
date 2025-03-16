
### Lambda výrazy v jazyce C#  

Lambda výrazy jsou anonymní funkce, které umožňují definovat kód přímo na místě jeho použití. Jsou klíčové pro práci s **delegáty**, **LINQ**, **asynchronními operacemi** a funkcionálním programováním v C#. Zde je jejich přehled:

---

#### **1. Základní syntaxe**  

Lambda výrazy mají tvar:  
```csharp
(parametry) => výraz
```  
- **Parametry**: Pokud je jeden a bez typu, závorky `()` jsou nepovinné.  
- `=>`: Lambda operátor (čte se „jde do“).  
- **Výraz**: Provádí operaci a vrací výsledek.  

**Příklady**:  
```csharp
// Bez parametrů
Action vypisAhoj = () => Console.WriteLine("Ahoj");

// S jedním parametrem
Func<int, int> druhaMocnina = x => x * x;

// S více parametry
Func<int, int, int> soucet = (a, b) => a + b;
```

---

#### **2. Typy lambda výrazů**  

- **Výrazové lambdy** (Expression Lambdas):  
  Obsahují jeden výraz (automaticky vrací výsledek).  
  ```csharp
  Func<int, bool> jeSude = x => x % 2 == 0;
  ```  

- **Blokové lambdy** (Statement Lambdas):  
  Obsahují blok kódu s `{}` a explicitním `return`.  
  ```csharp
  Func<int, int> faktorial = n => 
  {
      int vysledek = 1;
      for (int i = 1; i <= n; i++)
          vysledek *= i;
      return vysledek;
  };
  ```

---

#### **3. Použití s delegáty a LINQ**  

Lambda výrazy se často kombinují s **delegáty** (`Func`, `Action`, `Predicate`) a **LINQ dotazy**.  

**Příklad s LINQ**:  
```csharp
List<int> cisla = new List<int> { 1, 2, 3, 4, 5 };
var sudaCisla = cisla.Where(x => x % 2 == 0).ToList(); // [2, 4]
```

**Příklad s delegátem**:  
```csharp
Action<string> log = zprava => Console.WriteLine($"[LOG] {zprava}");
log("Aplikace spuštěna"); // Výstup: [LOG] Aplikace spuštěna
```

---

#### **4. Zachycování proměnných (Closures)**  

Lambda výrazy mohou zachytávat proměnné z okolního rozsahu (tzv. **closures**).  
```csharp
int koeficient = 10;
Func<int, int> vynasob = x => x * koeficient;
Console.WriteLine(vynasob(5)); // Výstup: 50
```  
- Pozor: Pokud se zachycená proměnná změní, lambda použije její aktuální hodnotu.  

**Rizikový příklad (loop variable capture)**:  
```csharp
List<Action> akce = new List<Action>();
for (int i = 0; i < 3; i++)
    akce.Add(() => Console.WriteLine(i)); // Všechny vypíšou 3!

foreach (var a in akce)
    a();
```  
**Řešení**: Použijte lokální kopii proměnné v cyklu.  

---

#### **5. Typová inference**  

Parametry lambda výrazů mohou odvodit typ z kontextu:  
```csharp
Func<double, double> odmocnina = x => Math.Sqrt(x);
```  
- Pokud nelze typ odvodit, je nutné jej explicitně uvést:  
  ```csharp
  Func<int, string> prevod = (int x) => x.ToString();
  ```

---

#### **6. Asynchronní lambdy**  

Lze definovat asynchronní operace pomocí `async` a `await`:  
```csharp
Func<Task> stahniData = async () => 
{
    await Task.Delay(1000);
    Console.WriteLine("Data stažena");
};
```

---

#### **7. Výhody a omezení**  

- **Výhody**:  
  - Stručný zápis (méně kódu než u pojmenovaných metod).  
  - Vhodné pro jednorázové operace (např. filtry v LINQ).  
- **Omezení**:  
  - Nelze použít `ref`, `out`, nebo `in` parametry.  
  - Složitější ladění (anonymní funkce).  

---

#### **8. Užitečné techniky**  

- **Discards**: Ignorování parametrů pomocí `_`.  
  ```csharp
  Action<string> logChyby = _ => Console.WriteLine("Došlo k chybě");
  ```  
- **Použití s eventy**:  
  ```csharp
  button.Click += (sender, e) => MessageBox.Show("Kliknuto!");
  ```

---

#### **Časté chyby**  

- **Změna zachycených proměnných**: Neočekávané chování v cyklech.  
- **Memory leaks**: Zachycení dlouho žijících objektů (např. v UI).  
- **Překrývání názvů parametrů**:  
  ```csharp
  int x = 10;
  Func<int, int> test = x => x * 2; // Chyba: Konflikt názvů
  ```

---

#### **Doporučení**  

- Používejte smysluplné názvy parametrů (např. `student => student.Jmeno`).  
- Pro složitější logiku preferujte pojmenované metody.  
- Testujte chování s closures v cyklech.  
