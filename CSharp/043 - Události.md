
### Události v jazyce C#  

Události (events) slouží k implementaci **mechanismu oznámení** mezi objekty. Umožòují tøídì (vydavateli) informovat ostatní tøídy (odbìratele) o zmìnách nebo akcích (napø. kliknutí na tlaèítko, zmìna dat). Jsou založeny na **delegátech** a jsou klíèové pro **event-driven programování**. Zde je jejich pøehled:

---

#### **1. Základní koncepty**  

- **Vydavatel (Publisher)**: Tøída, která deklaruje a vyvolává událost.  
- **Odbìratel (Subscriber)**: Tøída, která reaguje na událost.  
- **Mechanismus**: Odbìratel se registruje k události pomocí **delegátù** a pøi jejím vyvolání se spustí jeho metoda.

---

#### **2. Deklarace a použití události**  

- Událost se deklaruje pomocí klíèového slova **`event`** a delegáta.  
- **Standardní schéma**:  
  ```csharp
  public class Vydavatel
  {
      // Deklarace události
      public event EventHandler? Zmena;

      // Metoda pro vyvolání události
      protected virtual void OnZmena()
      {
          Zmena?.Invoke(this, EventArgs.Empty); // Bezpeèné volání
      }
  }
  ```

---

#### **3. Pøeddefinované delegáty pro události**  

- **`EventHandler`**: Základní delegát pro události bez dat.  
  ```csharp
  public event EventHandler? Kliknuti;
  ```  
- **`EventHandler<TEventArgs>`**: Událost s vlastními daty.  
  ```csharp
  public class MojeEventArgs : EventArgs
  {
      public string? Zprava { get; set; }
  }

  public event EventHandler<MojeEventArgs>? Akce;
  ```

---

#### **4. Pøihlašování a odhlašování odbìratelù**  

- Odbìratelé používají operátory **`+=`** (pøidání) a **`-=`** (odebrání).  
- **Pøíklad**:  
  ```csharp
  Vydavatel vydavatel = new Vydavatel();
  vydavatel.Zmena += (sender, e) => Console.WriteLine("Událost vyvolána!");

  // Vyvolání události
  vydavatel.OnZmena(); // Výstup: "Událost vyvolána!"
  ```

---

#### **5. Vlastní data události**  

- Data se pøedávají pomocí tøídy odvozené od **`EventArgs`**.  
- **Pøíklad**:  
  ```csharp
  public class TeplotaEventArgs : EventArgs
  {
      public double NovaTeplota { get; init; }
  }

  public class Teplomer
  {
      public event EventHandler<TeplotaEventArgs>? TeplotaZmenena;

      public void AktualizujTeplotu(double teplota)
      {
          TeplotaZmenena?.Invoke(this, new TeplotaEventArgs { NovaTeplota = teplota });
      }
  }
  ```

---

#### **6. Anonymní metody a lambdy**  

- Události lze obsluhovat anonymními metodami nebo lambda výrazy:  
  ```csharp
  Teplomer teplomer = new Teplomer();
  teplomer.TeplotaZmenena += (sender, e) => 
  {
      Console.WriteLine($"Nová teplota: {e.NovaTeplota}°C");
  };
  ```

---

#### **7. Bezpeènost vláken**  

- Pøi vyvolávání událostí v multithreadovém prostøedí vždy zkontrolujte, zda není událost `null`:  
  ```csharp
  var handler = TeplotaZmenena;
  handler?.Invoke(this, e); // Kopie pro thread-safe volání
  ```

---

#### **8. Èasté chyby**  

- **Memory leaks**: Zapomenutí odhlásit odbìratele (udržuje objekt v pamìti).  
- **Nesprávné použití `Invoke`**: Vyvolání události bez kontroly `null`.  
- **Použití veøejných delegátù místo `event`**: Narušení zapouzdøení.  

---

#### **9. Doporuèené postupy**  

- Používejte **`EventHandler<T>`** pro vlastní data namísto vlastních delegátù.  
- Pro vyvolání události vytvoøte **chránìnou metodu** (napø. `OnZmena`), aby ji mohly pøepsat odvozené tøídy.  
- Odhlašujte odbìratele, pokud již nejsou potøeba (napø. v `Dispose`).  

---

#### **10. Ukázka reálného použití**  

```csharp
public class Tlacitko
{
    public event EventHandler? Kliknuti;

    public void SimulujKlik()
    {
        Kliknuti?.Invoke(this, EventArgs.Empty);
    }
}

// Použití
Tlacitko btn = new Tlacitko();
btn.Kliknuti += (sender, e) => Console.WriteLine("Tlaèítko bylo kliknuto!");
btn.SimulujKlik(); // Výstup: "Tlaèítko bylo kliknuto!"
```

---

#### **11. Pokroèilé techniky**  

- **Vlastní pøístupové metody (add/remove)**:  
  ```csharp
  private EventHandler? _zmena;
  public event EventHandler? Zmena
  {
      add => _zmena += value;
      remove => _zmena -= value;
  }
  ```  
- **Slabé reference (Weak Event Pattern)**: Pro prevenci memory leaks v komplexních scénáøích.  
