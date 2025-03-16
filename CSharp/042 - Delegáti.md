
### Deleg�ti v jazyce C# 

Deleg�ti jsou **typem bezpe�n� ukazatele na metody**, kter� umo��uj� p�ed�vat metody jako parametry, ukl�dat je do prom�nn�ch nebo vytv��et ud�losti. Jsou z�kladem pro **event-driven programov�n�** a **LINQ**. Zde je jejich p�ehled:

---

#### **1. Definice a z�kladn� pou�it�**  

- Deleg�t je **referen�n� typ**, kter� definuje signaturu metody (n�vratov� typ a parametry).  
- Deklaruje se pomoc� kl��ov�ho slova `delegate`.  
- **P��klad**:  
  ```csharp
  // Definice deleg�tu
  public delegate void MojeAkce(string zprava);

  // Metoda, kter� odpov�d� signatu�e deleg�tu
  public void VypisZpravu(string text)
  {
      Console.WriteLine(text);
  }

  // Vytvo�en� instance deleg�tu
  MojeAkce delegat = VypisZpravu;
  delegat("Ahoj!"); // Vol�n� metody p�es deleg�t
  ```

---

#### **2. Vestav�n� deleg�ty**  

C# poskytuje obecn� deleg�ty, kter� nen� nutn� deklarovat:  
- **`Action`**: Metoda bez n�vratov� hodnoty (`void`).  
  ```csharp
  Action<string> vypis = text => Console.WriteLine(text);
  vypis("Hello World");
  ```  
- **`Func`**: Metoda s n�vratovou hodnotou (posledn� parametr je n�vratov� typ).  
  ```csharp
  Func<int, int, int> scitani = (a, b) => a + b;
  int vysledek = scitani(5, 3); // 8
  ```  
- **`Predicate<T>`**: Metoda vracej�c� `bool` (obvykle pro podm�nky).  
  ```csharp
  Predicate<int> jeKladne = x => x > 0;
  bool test = jeKladne(-5); // false
  ```

---

#### **3. Multicast deleg�ti**  

- Deleg�ti mohou odkazovat na **v�ce metod najednou** (oper�tory `+=` a `-=`).  
- **P��klad**:  
  ```csharp
  Action operace = () => Console.Write("A");
  operace += () => Console.Write("B");
  operace(); // V�stup: AB
  ```  
- **Upozorn�n�**: Pokud deleg�t vrac� hodnotu, vr�t� se v�sledek **posledn� metody** v �et�zci.

---

#### **4. Deleg�ti a ud�losti (Events)** 

- Ud�losti jsou zalo�eny na deleg�tech a umo��uj� **asynchronn� notifikace** (nap�. reakce na kliknut� tla��tka).  
- **P��klad**:  
  ```csharp
  public class Tla��tko
  {
      public event EventHandler Kliknuti;

      public void Stisk()
      {
          Kliknuti?.Invoke(this, EventArgs.Empty);
      }
  }

  Tla��tko btn = new Tla��tko();
  btn.Kliknuti += (sender, e) => Console.WriteLine("Kliknuto!");
  btn.Stisk(); // V�stup: Kliknuto!
  ```

---

#### **5. Asynchronn� deleg�ti**  

- Deleg�ti mohou volat **asynchronn� metody** pomoc� `async` a `await`.  
- **P��klad**:  
  ```csharp
  Func<Task> stahniData = async () => 
  {
      await Task.Delay(1000);
      Console.WriteLine("Data sta�ena");
  };
  await stahniData();
  ```

---

#### **6. Deleg�t vs. Rozhran�**  

- **Deleg�ti** jsou vhodn� pro **jednoduch� operace** (nap�. callbacky).  
- **Rozhran�** jsou lep�� pro slo�it�j�� sc�n��e s v�ce metodami.  

---

#### **�ast� chyby**  

- **`NullReferenceException`**: Pokus o vol�n� neinicializovan�ho deleg�tu.  
  ```csharp
  Action akce = null;
  akce(); // Vyvol� v�jimku
  ```  
- **Nespr�vn� signatura**: P�id�n� metody s odli�n�mi parametry/n�vratov�m typem.  
- **Memory leaks**: Neodhl�en� od ud�lost� (zachycen� objekt� v pam�ti).

---

#### **Doporu�en�**  

- Pro ud�losti pou��vejte **`EventHandler`** nebo **`EventHandler<TEventArgs>`**.  
- P�ed vol�n�m deleg�tu v�dy zkontrolujte `null`:  
  ```csharp
  MojeAkce delegat = ...;
  delegat?.Invoke("text");
  ```  
- Pro slo�it�j�� sc�n��e pou�ijte **lambda v�razy** nebo **anonymn� metody**.  

---

#### **U�ite�n� techniky**  

- **Anonymn� metody**:  
  ```csharp
  Action vypis = delegate() { Console.WriteLine("Anonymn� metoda"); };
  ```  
- **Kombinace deleg�t�**:  
  ```csharp
  Action a = () => Console.Write("A");
  Action b = () => Console.Write("B");
  Action kombinace = a + b;
  kombinace(); // V�stup: AB
  ```  
