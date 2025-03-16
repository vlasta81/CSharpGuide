
### Lambda výrazy v jazyce C#  

Lambda výrazy jsou anonymní funkce, které umožòují definovat kód pøímo na místì jeho použití. Jsou klíèové pro práci s **delegáty**, **LINQ**, **asynchronními operacemi** a funkcionálním programováním v C#. Zde je jejich pøehled:

---

#### **1. Základní syntaxe**  

Lambda výrazy mají tvar:  
```csharp
(parametry) => výraz
```  
- **Parametry**: Pokud je jeden a bez typu, závorky `()` jsou nepovinné.  
- `=>`: Lambda operátor (ète se „jde do“).  
- **Výraz**: Provádí operaci a vrací výsledek.  

**Pøíklady**:  
```csharp
// Bez parametrù
Action vypisAhoj = () => Console.WriteLine("Ahoj");

// S jedním parametrem
Func<int, int> druhaMocnina = x => x * x;

// S více parametry
Func<int, int, int> soucet = (a, b) => a + b;
```

---

#### **2. Typy lambda výrazù**  

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

Lambda výrazy se èasto kombinují s **delegáty** (`Func`, `Action`, `Predicate`) a **LINQ dotazy**.  

**Pøíklad s LINQ**:  
```csharp
List<int> cisla = new List<int> { 1, 2, 3, 4, 5 };
var sudaCisla = cisla.Where(x => x % 2 == 0).ToList(); // [2, 4]
```

**Pøíklad s delegátem**:  
```csharp
Action<string> log = zprava => Console.WriteLine($"[LOG] {zprava}");
log("Aplikace spuštìna"); // Výstup: [LOG] Aplikace spuštìna
```

---

#### **4. Zachycování promìnných (Closures)**  

Lambda výrazy mohou zachytávat promìnné z okolního rozsahu (tzv. **closures**).  
```csharp
int koeficient = 10;
Func<int, int> vynasob = x => x * koeficient;
Console.WriteLine(vynasob(5)); // Výstup: 50
```  
- Pozor: Pokud se zachycená promìnná zmìní, lambda použije její aktuální hodnotu.  

**Rizikový pøíklad (loop variable capture)**:  
```csharp
List<Action> akce = new List<Action>();
for (int i = 0; i < 3; i++)
    akce.Add(() => Console.WriteLine(i)); // Všechny vypíšou 3!

foreach (var a in akce)
    a();
```  
**Øešení**: Použijte lokální kopii promìnné v cyklu.  

---

#### **5. Typová inference**  

Parametry lambda výrazù mohou odvodit typ z kontextu:  
```csharp
Func<double, double> odmocnina = x => Math.Sqrt(x);
```  
- Pokud nelze typ odvodit, je nutné jej explicitnì uvést:  
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
  - Struèný zápis (ménì kódu než u pojmenovaných metod).  
  - Vhodné pro jednorázové operace (napø. filtry v LINQ).  
- **Omezení**:  
  - Nelze použít `ref`, `out`, nebo `in` parametry.  
  - Složitìjší ladìní (anonymní funkce).  

---

#### **8. Užiteèné techniky**  

- **Discards**: Ignorování parametrù pomocí `_`.  
  ```csharp
  Action<string> logChyby = _ => Console.WriteLine("Došlo k chybì");
  ```  
- **Použití s eventy**:  
  ```csharp
  button.Click += (sender, e) => MessageBox.Show("Kliknuto!");
  ```

---

#### **Èasté chyby**  

- **Zmìna zachycených promìnných**: Neoèekávané chování v cyklech.  
- **Memory leaks**: Zachycení dlouho žijících objektù (napø. v UI).  
- **Pøekrývání názvù parametrù**:  
  ```csharp
  int x = 10;
  Func<int, int> test = x => x * 2; // Chyba: Konflikt názvù
  ```

---

#### **Doporuèení**  

- Používejte smysluplné názvy parametrù (napø. `student => student.Jmeno`).  
- Pro složitìjší logiku preferujte pojmenované metody.  
- Testujte chování s closures v cyklech.  
