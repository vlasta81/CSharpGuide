
### Pr�ce s t��dou `Task` v jazyce C#  

T��da `Task` je kl��ovou sou��st� **Task Parallel Library (TPL)** a slou�� k reprezentaci asynchronn� nebo paraleln� operace. Umo��uje efektivn� spr�vu vl�ken, zpracov�n� v�sledk� a o�et�en� chyb. Zde je p�ehled kl��ov�ch koncept�:

---

#### **1. Co je `Task`?**  

- Reprezentuje **jednu asynchronn� operaci**, kter� m��e (ale nemus�) vr�tit v�sledek (`Task<TResult>`).  
- **V�hody**:  
  - Zjednodu�uje asynchronn� a paraleln� programov�n�.  
  - Automaticky vyu��v� **Thread Pool** pro efektivn� spr�vu vl�ken.  
  - Podporuje �et�zen� operac� (continuations) a zpracov�n� v�jimek.  

---

#### **2. Vytvo�en� a spu�t�n� `Task`**  

- **Implicitn� spu�t�n� pomoc� `Task.Run`** (pro CPU-bound operace):  
  ```csharp
  Task<int> task = Task.Run(() => 
  {
      Thread.Sleep(1000);
      return 42;
  });
  ```  
- **Explicitn� vytvo�en� a spu�t�n�**:  
  ```csharp
  var task = new Task(() => Console.WriteLine("Spu�t�no"));
  task.Start();
  ```  
- **Pomoc� `Task.Factory.StartNew`** (pokro�ilej�� konfigurace):  
  ```csharp
  Task task = Task.Factory.StartNew(() => { /* ... */ }, TaskCreationOptions.LongRunning);
  ```  

---

#### **3. �ek�n� na dokon�en� `Task`**  

- **`await`**: Asynchronn� �ek�n� (neblokuje vl�kno):  
  ```csharp
  int vysledek = await task;
  ```  
- **Blokuj�c� �ek�n�** (pouze ve specifick�ch sc�n���ch!):  
  ```csharp
  task.Wait(); // �ek� na dokon�en�
  int result = task.Result; // Z�sk� v�sledek (riziko deadlocku!)
  ```  

---

#### **4. Stavy `Task`**  

- **`Status`**: Ur�uje stav �lohy (nap�. `Created`, `Running`, `RanToCompletion`, `Faulted`, `Canceled`).  
- **Kontrola stavu**:  
  ```csharp
  if (task.Status == TaskStatus.RanToCompletion)
      Console.WriteLine("�loha dokon�ena.");
  ```  

---

#### **5. Zpracov�n� v�sledku**  

- **Pro `Task<T>`**:  
  ```csharp
  Task<string> task = Task.Run(() => "Hello");
  string text = await task; // "Hello"
  ```  
- **Pro `Task` bez v�sledku**:  
  ```csharp
  Task task = Task.Run(() => Console.WriteLine("Hotovo"));
  await task;
  ```  

---

#### **6. Zru�en� `Task`**  

- Pou�it� `CancellationTokenSource` a `CancellationToken`:  
  ```csharp
  var cts = new CancellationTokenSource();
  Task task = Task.Run(() => 
  {
      while (true)
      {
          cts.Token.ThrowIfCancellationRequested(); // Kontrola zru�en�
          // Pr�ce...
      }
  }, cts.Token);

  cts.CancelAfter(5000); // Zru�� �lohu po 5 sekund�ch
  ```  

---

#### **7. Zpracov�n� v�jimek**  

- V�jimky jsou zabaleny do `AggregateException`:  
  ```csharp
  try
  {
      await task;
  }
  catch (AggregateException ex)
  {
      foreach (var e in ex.InnerExceptions)
          Console.WriteLine(e.Message);
  }
  ```  
- **Pro jednu �lohu**: V�jimka se propaguje p��mo (nen� zabalena).  

---

#### **8. Pokro�il� techniky**  

- **Kombinace v�ce �loh**:  
  ```csharp
  Task<int> task1 = Task.Run(() => 10);
  Task<int> task2 = Task.Run(() => 20);
  int[] results = await Task.WhenAll(task1, task2); // [10, 20]
  ```  
  ```csharp
  Task<int> finishedTask = await Task.WhenAny(task1, task2); // �ek� na prvn� dokon�enou
  ```  
- **Continuations (�et�zen�)**:  
  ```csharp
  Task<int> parentTask = Task.Run(() => 5);
  Task continuation = parentTask.ContinueWith(prev => 
  {
      Console.WriteLine($"P�edchoz� v�sledek: {prev.Result}");
  }, TaskContinuationOptions.OnlyOnRanToCompletion);
  ```  

---

#### **9. `Task` vs `ValueTask`**  

- **`ValueTask`**: Optimalizace pro p��pady, kdy je v�sledek �asto dostupn� synchronn� (nap�. caching).  
  ```csharp
  public ValueTask<int> GetDataAsync()
  {
      if (_cachedData != null)
          return new ValueTask<int>(_cachedData); // Synchronn� v�sledek
      return new ValueTask<int>(LoadDataAsync()); // Asynchronn� operace
  }
  ```  

---

#### **10. Doporu�en� postupy**  

- Preferujte **`async`/`await`** p�ed blokuj�c�mi metodami (jako `.Result` nebo `.Wait()`).  
- Pro dlouhodob� �lohy nastavte `TaskCreationOptions.LongRunning`.  
- V�dy o�et�ujte **v�jimky** a **zru�en�**.  
- Pro knihovny pou��vejte `ConfigureAwait(false)` k p�edejit� deadlock�:  
  ```csharp
  await task.ConfigureAwait(false);
  ```  

---

#### **11. �ast� chyby**  

- **Deadlock**: Blokov�n� hlavn�ho vl�kna pomoc� `.Result` v UI aplikac�ch.  
- **Zapomenut� `await`**:  
  ```csharp
  Task.Run(() => Console.WriteLine("Ahoj")); // �loha se spust�, ale nikdo na ni ne�ek�!
  ```  
- **Nadbyte�n� vytv��en� �loh**: Pro trivi�ln� operace pou��vejte `Task.CompletedTask`.  

---

#### **12. Uk�zka re�ln�ho pou�it�**  

```csharp
public async Task<string> StahniWebovouStrankuAsync(string url)
{
    using (var client = new HttpClient())
    {
        return await client.GetStringAsync(url);
    }
}

// Vol�n�
string obsah = await StahniWebovouStrankuAsync("https://example.com");
```
