
### Ud�losti v jazyce C#  

Ud�losti (events) slou�� k implementaci **mechanismu ozn�men�** mezi objekty. Umo��uj� t��d� (vydavateli) informovat ostatn� t��dy (odb�ratele) o zm�n�ch nebo akc�ch (nap�. kliknut� na tla��tko, zm�na dat). Jsou zalo�eny na **deleg�tech** a jsou kl��ov� pro **event-driven programov�n�**. Zde je jejich p�ehled:

---

#### **1. Z�kladn� koncepty**  

- **Vydavatel (Publisher)**: T��da, kter� deklaruje a vyvol�v� ud�lost.  
- **Odb�ratel (Subscriber)**: T��da, kter� reaguje na ud�lost.  
- **Mechanismus**: Odb�ratel se registruje k ud�losti pomoc� **deleg�t�** a p�i jej�m vyvol�n� se spust� jeho metoda.

---

#### **2. Deklarace a pou�it� ud�losti**  

- Ud�lost se deklaruje pomoc� kl��ov�ho slova **`event`** a deleg�ta.  
- **Standardn� sch�ma**:  
  ```csharp
  public class Vydavatel
  {
      // Deklarace ud�losti
      public event EventHandler? Zmena;

      // Metoda pro vyvol�n� ud�losti
      protected virtual void OnZmena()
      {
          Zmena?.Invoke(this, EventArgs.Empty); // Bezpe�n� vol�n�
      }
  }
  ```

---

#### **3. P�eddefinovan� deleg�ty pro ud�losti**  

- **`EventHandler`**: Z�kladn� deleg�t pro ud�losti bez dat.  
  ```csharp
  public event EventHandler? Kliknuti;
  ```  
- **`EventHandler<TEventArgs>`**: Ud�lost s vlastn�mi daty.  
  ```csharp
  public class MojeEventArgs : EventArgs
  {
      public string? Zprava { get; set; }
  }

  public event EventHandler<MojeEventArgs>? Akce;
  ```

---

#### **4. P�ihla�ov�n� a odhla�ov�n� odb�ratel�**  

- Odb�ratel� pou��vaj� oper�tory **`+=`** (p�id�n�) a **`-=`** (odebr�n�).  
- **P��klad**:  
  ```csharp
  Vydavatel vydavatel = new Vydavatel();
  vydavatel.Zmena += (sender, e) => Console.WriteLine("Ud�lost vyvol�na!");

  // Vyvol�n� ud�losti
  vydavatel.OnZmena(); // V�stup: "Ud�lost vyvol�na!"
  ```

---

#### **5. Vlastn� data ud�losti**  

- Data se p�ed�vaj� pomoc� t��dy odvozen� od **`EventArgs`**.  
- **P��klad**:  
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

#### **6. Anonymn� metody a lambdy**  

- Ud�losti lze obsluhovat anonymn�mi metodami nebo lambda v�razy:  
  ```csharp
  Teplomer teplomer = new Teplomer();
  teplomer.TeplotaZmenena += (sender, e) => 
  {
      Console.WriteLine($"Nov� teplota: {e.NovaTeplota}�C");
  };
  ```

---

#### **7. Bezpe�nost vl�ken**  

- P�i vyvol�v�n� ud�lost� v multithreadov�m prost�ed� v�dy zkontrolujte, zda nen� ud�lost `null`:  
  ```csharp
  var handler = TeplotaZmenena;
  handler?.Invoke(this, e); // Kopie pro thread-safe vol�n�
  ```

---

#### **8. �ast� chyby**  

- **Memory leaks**: Zapomenut� odhl�sit odb�ratele (udr�uje objekt v pam�ti).  
- **Nespr�vn� pou�it� `Invoke`**: Vyvol�n� ud�losti bez kontroly `null`.  
- **Pou�it� ve�ejn�ch deleg�t� m�sto `event`**: Naru�en� zapouzd�en�.  

---

#### **9. Doporu�en� postupy**  

- Pou��vejte **`EventHandler<T>`** pro vlastn� data nam�sto vlastn�ch deleg�t�.  
- Pro vyvol�n� ud�losti vytvo�te **chr�n�nou metodu** (nap�. `OnZmena`), aby ji mohly p�epsat odvozen� t��dy.  
- Odhla�ujte odb�ratele, pokud ji� nejsou pot�eba (nap�. v `Dispose`).  

---

#### **10. Uk�zka re�ln�ho pou�it�**  

```csharp
public class Tlacitko
{
    public event EventHandler? Kliknuti;

    public void SimulujKlik()
    {
        Kliknuti?.Invoke(this, EventArgs.Empty);
    }
}

// Pou�it�
Tlacitko btn = new Tlacitko();
btn.Kliknuti += (sender, e) => Console.WriteLine("Tla��tko bylo kliknuto!");
btn.SimulujKlik(); // V�stup: "Tla��tko bylo kliknuto!"
```

---

#### **11. Pokro�il� techniky**  

- **Vlastn� p��stupov� metody (add/remove)**:  
  ```csharp
  private EventHandler? _zmena;
  public event EventHandler? Zmena
  {
      add => _zmena += value;
      remove => _zmena -= value;
  }
  ```  
- **Slab� reference (Weak Event Pattern)**: Pro prevenci memory leaks v komplexn�ch sc�n���ch.  
