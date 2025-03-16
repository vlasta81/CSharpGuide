
### Události v jazyce C#  

Události (events) slouží k implementaci **mechanismu oznámení** mezi objekty. Umožňují třídě (vydavateli) informovat ostatní třídy (odběratele) o změnách nebo akcích (např. kliknutí na tlačítko, změna dat). Jsou založeny na **delegátech** a jsou klíčové pro **event-driven programování**. Zde je jejich přehled:

---

#### **1. Základní koncepty**  

- **Vydavatel (Publisher)**: Třída, která deklaruje a vyvolává událost.  
- **Odběratel (Subscriber)**: Třída, která reaguje na událost.  
- **Mechanismus**: Odběratel se registruje k události pomocí **delegátů** a při jejím vyvolání se spustí jeho metoda.

---

#### **2. Deklarace a použití události**  

- Událost se deklaruje pomocí klíčového slova **`event`** a delegáta.  
- **Standardní schéma**:  
  ```csharp
  public class Vydavatel
  {
      // Deklarace události
      public event EventHandler? Zmena;

      // Metoda pro vyvolání události
      protected virtual void OnZmena()
      {
          Zmena?.Invoke(this, EventArgs.Empty); // Bezpečné volání
      }
  }
  ```

---

#### **3. Předdefinované delegáty pro události**  

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

#### **4. Přihlašování a odhlašování odběratelů**  

- Odběratelé používají operátory **`+=`** (přidání) a **`-=`** (odebrání).  
- **Příklad**:  
  ```csharp
  Vydavatel vydavatel = new Vydavatel();
  vydavatel.Zmena += (sender, e) => Console.WriteLine("Událost vyvolána!");

  // Vyvolání události
  vydavatel.OnZmena(); // Výstup: "Událost vyvolána!"
  ```

---

#### **5. Vlastní data události**  

- Data se předávají pomocí třídy odvozené od **`EventArgs`**.  
- **Příklad**:  
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

#### **7. Bezpečnost vláken**  

- Při vyvolávání událostí v multithreadovém prostředí vždy zkontrolujte, zda není událost `null`:  
  ```csharp
  var handler = TeplotaZmenena;
  handler?.Invoke(this, e); // Kopie pro thread-safe volání
  ```

---

#### **8. Časté chyby**  

- **Memory leaks**: Zapomenutí odhlásit odběratele (udržuje objekt v paměti).  
- **Nesprávné použití `Invoke`**: Vyvolání události bez kontroly `null`.  
- **Použití veřejných delegátů místo `event`**: Narušení zapouzdření.  

---

#### **9. Doporučené postupy**  

- Používejte **`EventHandler<T>`** pro vlastní data namísto vlastních delegátů.  
- Pro vyvolání události vytvořte **chráněnou metodu** (např. `OnZmena`), aby ji mohly přepsat odvozené třídy.  
- Odhlašujte odběratele, pokud již nejsou potřeba (např. v `Dispose`).  

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
btn.Kliknuti += (sender, e) => Console.WriteLine("Tlačítko bylo kliknuto!");
btn.SimulujKlik(); // Výstup: "Tlačítko bylo kliknuto!"
```

---

#### **11. Pokročilé techniky**  

- **Vlastní přístupové metody (add/remove)**:  
  ```csharp
  private EventHandler? _zmena;
  public event EventHandler? Zmena
  {
      add => _zmena += value;
      remove => _zmena -= value;
  }
  ```  
- **Slabé reference (Weak Event Pattern)**: Pro prevenci memory leaks v komplexních scénářích.  
