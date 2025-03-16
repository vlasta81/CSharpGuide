
### Delegáti v jazyce C# 

Delegáti jsou **typem bezpečné ukazatele na metody**, které umožňují předávat metody jako parametry, ukládat je do proměnných nebo vytvářet události. Jsou základem pro **event-driven programování** a **LINQ**. Zde je jejich přehled:

---

#### **1. Definice a základní použití**  

- Delegát je **referenční typ**, který definuje signaturu metody (návratový typ a parametry).  
- Deklaruje se pomocí klíčového slova `delegate`.  
- **Příklad**:  
  ```csharp
  // Definice delegátu
  public delegate void MojeAkce(string zprava);

  // Metoda, která odpovídá signatuře delegátu
  public void VypisZpravu(string text)
  {
      Console.WriteLine(text);
  }

  // Vytvoření instance delegátu
  MojeAkce delegat = VypisZpravu;
  delegat("Ahoj!"); // Volání metody přes delegát
  ```

---

#### **2. Vestavěné delegáty**  

C# poskytuje obecné delegáty, které není nutné deklarovat:  
- **`Action`**: Metoda bez návratové hodnoty (`void`).  
  ```csharp
  Action<string> vypis = text => Console.WriteLine(text);
  vypis("Hello World");
  ```  
- **`Func`**: Metoda s návratovou hodnotou (poslední parametr je návratový typ).  
  ```csharp
  Func<int, int, int> scitani = (a, b) => a + b;
  int vysledek = scitani(5, 3); // 8
  ```  
- **`Predicate<T>`**: Metoda vracející `bool` (obvykle pro podmínky).  
  ```csharp
  Predicate<int> jeKladne = x => x > 0;
  bool test = jeKladne(-5); // false
  ```

---

#### **3. Multicast delegáti**  

- Delegáti mohou odkazovat na **více metod najednou** (operátory `+=` a `-=`).  
- **Příklad**:  
  ```csharp
  Action operace = () => Console.Write("A");
  operace += () => Console.Write("B");
  operace(); // Výstup: AB
  ```  
- **Upozornění**: Pokud delegát vrací hodnotu, vrátí se výsledek **poslední metody** v řetězci.

---

#### **4. Delegáti a události (Events)** 

- Události jsou založeny na delegátech a umožňují **asynchronní notifikace** (např. reakce na kliknutí tlačítka).  
- **Příklad**:  
  ```csharp
  public class Tlačítko
  {
      public event EventHandler Kliknuti;

      public void Stisk()
      {
          Kliknuti?.Invoke(this, EventArgs.Empty);
      }
  }

  Tlačítko btn = new Tlačítko();
  btn.Kliknuti += (sender, e) => Console.WriteLine("Kliknuto!");
  btn.Stisk(); // Výstup: Kliknuto!
  ```

---

#### **5. Asynchronní delegáti**  

- Delegáti mohou volat **asynchronní metody** pomocí `async` a `await`.  
- **Příklad**:  
  ```csharp
  Func<Task> stahniData = async () => 
  {
      await Task.Delay(1000);
      Console.WriteLine("Data stažena");
  };
  await stahniData();
  ```

---

#### **6. Delegát vs. Rozhraní**  

- **Delegáti** jsou vhodné pro **jednoduché operace** (např. callbacky).  
- **Rozhraní** jsou lepší pro složitější scénáře s více metodami.  

---

#### **Časté chyby**  

- **`NullReferenceException`**: Pokus o volání neinicializovaného delegátu.  
  ```csharp
  Action akce = null;
  akce(); // Vyvolá výjimku
  ```  
- **Nesprávná signatura**: Přidání metody s odlišnými parametry/návratovým typem.  
- **Memory leaks**: Neodhlášení od událostí (zachycení objektů v paměti).

---

#### **Doporučení**  

- Pro události používejte **`EventHandler`** nebo **`EventHandler<TEventArgs>`**.  
- Před voláním delegátu vždy zkontrolujte `null`:  
  ```csharp
  MojeAkce delegat = ...;
  delegat?.Invoke("text");
  ```  
- Pro složitější scénáře použijte **lambda výrazy** nebo **anonymní metody**.  

---

#### **Užitečné techniky**  

- **Anonymní metody**:  
  ```csharp
  Action vypis = delegate() { Console.WriteLine("Anonymní metoda"); };
  ```  
- **Kombinace delegátů**:  
  ```csharp
  Action a = () => Console.Write("A");
  Action b = () => Console.Write("B");
  Action kombinace = a + b;
  kombinace(); // Výstup: AB
  ```  
