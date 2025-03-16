
### Delegáti v jazyce C# 

Delegáti jsou **typem bezpeèné ukazatele na metody**, které umožòují pøedávat metody jako parametry, ukládat je do promìnných nebo vytváøet události. Jsou základem pro **event-driven programování** a **LINQ**. Zde je jejich pøehled:

---

#### **1. Definice a základní použití**  

- Delegát je **referenèní typ**, který definuje signaturu metody (návratový typ a parametry).  
- Deklaruje se pomocí klíèového slova `delegate`.  
- **Pøíklad**:  
  ```csharp
  // Definice delegátu
  public delegate void MojeAkce(string zprava);

  // Metoda, která odpovídá signatuøe delegátu
  public void VypisZpravu(string text)
  {
      Console.WriteLine(text);
  }

  // Vytvoøení instance delegátu
  MojeAkce delegat = VypisZpravu;
  delegat("Ahoj!"); // Volání metody pøes delegát
  ```

---

#### **2. Vestavìné delegáty**  

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
- **Pøíklad**:  
  ```csharp
  Action operace = () => Console.Write("A");
  operace += () => Console.Write("B");
  operace(); // Výstup: AB
  ```  
- **Upozornìní**: Pokud delegát vrací hodnotu, vrátí se výsledek **poslední metody** v øetìzci.

---

#### **4. Delegáti a události (Events)** 

- Události jsou založeny na delegátech a umožòují **asynchronní notifikace** (napø. reakce na kliknutí tlaèítka).  
- **Pøíklad**:  
  ```csharp
  public class Tlaèítko
  {
      public event EventHandler Kliknuti;

      public void Stisk()
      {
          Kliknuti?.Invoke(this, EventArgs.Empty);
      }
  }

  Tlaèítko btn = new Tlaèítko();
  btn.Kliknuti += (sender, e) => Console.WriteLine("Kliknuto!");
  btn.Stisk(); // Výstup: Kliknuto!
  ```

---

#### **5. Asynchronní delegáti**  

- Delegáti mohou volat **asynchronní metody** pomocí `async` a `await`.  
- **Pøíklad**:  
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

- **Delegáti** jsou vhodné pro **jednoduché operace** (napø. callbacky).  
- **Rozhraní** jsou lepší pro složitìjší scénáøe s více metodami.  

---

#### **Èasté chyby**  

- **`NullReferenceException`**: Pokus o volání neinicializovaného delegátu.  
  ```csharp
  Action akce = null;
  akce(); // Vyvolá výjimku
  ```  
- **Nesprávná signatura**: Pøidání metody s odlišnými parametry/návratovým typem.  
- **Memory leaks**: Neodhlášení od událostí (zachycení objektù v pamìti).

---

#### **Doporuèení**  

- Pro události používejte **`EventHandler`** nebo **`EventHandler<TEventArgs>`**.  
- Pøed voláním delegátu vždy zkontrolujte `null`:  
  ```csharp
  MojeAkce delegat = ...;
  delegat?.Invoke("text");
  ```  
- Pro složitìjší scénáøe použijte **lambda výrazy** nebo **anonymní metody**.  

---

#### **Užiteèné techniky**  

- **Anonymní metody**:  
  ```csharp
  Action vypis = delegate() { Console.WriteLine("Anonymní metoda"); };
  ```  
- **Kombinace delegátù**:  
  ```csharp
  Action a = () => Console.Write("A");
  Action b = () => Console.Write("B");
  Action kombinace = a + b;
  kombinace(); // Výstup: AB
  ```  
