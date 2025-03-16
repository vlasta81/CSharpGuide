
### Lambda v�razy v jazyce C#  

Lambda v�razy jsou anonymn� funkce, kter� umo��uj� definovat k�d p��mo na m�st� jeho pou�it�. Jsou kl��ov� pro pr�ci s **deleg�ty**, **LINQ**, **asynchronn�mi operacemi** a funkcion�ln�m programov�n�m v C#. Zde je jejich p�ehled:

---

#### **1. Z�kladn� syntaxe**  

Lambda v�razy maj� tvar:  
```csharp
(parametry) => v�raz
```  
- **Parametry**: Pokud je jeden a bez typu, z�vorky `()` jsou nepovinn�.  
- `=>`: Lambda oper�tor (�te se �jde do�).  
- **V�raz**: Prov�d� operaci a vrac� v�sledek.  

**P��klady**:  
```csharp
// Bez parametr�
Action vypisAhoj = () => Console.WriteLine("Ahoj");

// S jedn�m parametrem
Func<int, int> druhaMocnina = x => x * x;

// S v�ce parametry
Func<int, int, int> soucet = (a, b) => a + b;
```

---

#### **2. Typy lambda v�raz�**  

- **V�razov� lambdy** (Expression Lambdas):  
  Obsahuj� jeden v�raz (automaticky vrac� v�sledek).  
  ```csharp
  Func<int, bool> jeSude = x => x % 2 == 0;
  ```  

- **Blokov� lambdy** (Statement Lambdas):  
  Obsahuj� blok k�du s `{}` a explicitn�m `return`.  
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

#### **3. Pou�it� s deleg�ty a LINQ**  

Lambda v�razy se �asto kombinuj� s **deleg�ty** (`Func`, `Action`, `Predicate`) a **LINQ dotazy**.  

**P��klad s LINQ**:  
```csharp
List<int> cisla = new List<int> { 1, 2, 3, 4, 5 };
var sudaCisla = cisla.Where(x => x % 2 == 0).ToList(); // [2, 4]
```

**P��klad s deleg�tem**:  
```csharp
Action<string> log = zprava => Console.WriteLine($"[LOG] {zprava}");
log("Aplikace spu�t�na"); // V�stup: [LOG] Aplikace spu�t�na
```

---

#### **4. Zachycov�n� prom�nn�ch (Closures)**  

Lambda v�razy mohou zachyt�vat prom�nn� z okoln�ho rozsahu (tzv. **closures**).  
```csharp
int koeficient = 10;
Func<int, int> vynasob = x => x * koeficient;
Console.WriteLine(vynasob(5)); // V�stup: 50
```  
- Pozor: Pokud se zachycen� prom�nn� zm�n�, lambda pou�ije jej� aktu�ln� hodnotu.  

**Rizikov� p��klad (loop variable capture)**:  
```csharp
List<Action> akce = new List<Action>();
for (int i = 0; i < 3; i++)
    akce.Add(() => Console.WriteLine(i)); // V�echny vyp�ou 3!

foreach (var a in akce)
    a();
```  
**�e�en�**: Pou�ijte lok�ln� kopii prom�nn� v cyklu.  

---

#### **5. Typov� inference**  

Parametry lambda v�raz� mohou odvodit typ z kontextu:  
```csharp
Func<double, double> odmocnina = x => Math.Sqrt(x);
```  
- Pokud nelze typ odvodit, je nutn� jej explicitn� uv�st:  
  ```csharp
  Func<int, string> prevod = (int x) => x.ToString();
  ```

---

#### **6. Asynchronn� lambdy**  

Lze definovat asynchronn� operace pomoc� `async` a `await`:  
```csharp
Func<Task> stahniData = async () => 
{
    await Task.Delay(1000);
    Console.WriteLine("Data sta�ena");
};
```

---

#### **7. V�hody a omezen�**  

- **V�hody**:  
  - Stru�n� z�pis (m�n� k�du ne� u pojmenovan�ch metod).  
  - Vhodn� pro jednor�zov� operace (nap�. filtry v LINQ).  
- **Omezen�**:  
  - Nelze pou��t `ref`, `out`, nebo `in` parametry.  
  - Slo�it�j�� lad�n� (anonymn� funkce).  

---

#### **8. U�ite�n� techniky**  

- **Discards**: Ignorov�n� parametr� pomoc� `_`.  
  ```csharp
  Action<string> logChyby = _ => Console.WriteLine("Do�lo k chyb�");
  ```  
- **Pou�it� s eventy**:  
  ```csharp
  button.Click += (sender, e) => MessageBox.Show("Kliknuto!");
  ```

---

#### **�ast� chyby**  

- **Zm�na zachycen�ch prom�nn�ch**: Neo�ek�van� chov�n� v cyklech.  
- **Memory leaks**: Zachycen� dlouho �ij�c�ch objekt� (nap�. v UI).  
- **P�ekr�v�n� n�zv� parametr�**:  
  ```csharp
  int x = 10;
  Func<int, int> test = x => x * 2; // Chyba: Konflikt n�zv�
  ```

---

#### **Doporu�en�**  

- Pou��vejte smyslupln� n�zvy parametr� (nap�. `student => student.Jmeno`).  
- Pro slo�it�j�� logiku preferujte pojmenovan� metody.  
- Testujte chov�n� s closures v cyklech.  
