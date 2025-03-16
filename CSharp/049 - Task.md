
### Práce s tøídou `Task` v jazyce C#  

Tøída `Task` je klíèovou souèástí **Task Parallel Library (TPL)** a slouží k reprezentaci asynchronní nebo paralelní operace. Umožòuje efektivní správu vláken, zpracování výsledkù a ošetøení chyb. Zde je pøehled klíèových konceptù:

---

#### **1. Co je `Task`?**  

- Reprezentuje **jednu asynchronní operaci**, která mùže (ale nemusí) vrátit výsledek (`Task<TResult>`).  
- **Výhody**:  
  - Zjednodušuje asynchronní a paralelní programování.  
  - Automaticky využívá **Thread Pool** pro efektivní správu vláken.  
  - Podporuje øetìzení operací (continuations) a zpracování výjimek.  

---

#### **2. Vytvoøení a spuštìní `Task`**  

- **Implicitní spuštìní pomocí `Task.Run`** (pro CPU-bound operace):  
  ```csharp
  Task<int> task = Task.Run(() => 
  {
      Thread.Sleep(1000);
      return 42;
  });
  ```  
- **Explicitní vytvoøení a spuštìní**:  
  ```csharp
  var task = new Task(() => Console.WriteLine("Spuštìno"));
  task.Start();
  ```  
- **Pomocí `Task.Factory.StartNew`** (pokroèilejší konfigurace):  
  ```csharp
  Task task = Task.Factory.StartNew(() => { /* ... */ }, TaskCreationOptions.LongRunning);
  ```  

---

#### **3. Èekání na dokonèení `Task`**  

- **`await`**: Asynchronní èekání (neblokuje vlákno):  
  ```csharp
  int vysledek = await task;
  ```  
- **Blokující èekání** (pouze ve specifických scénáøích!):  
  ```csharp
  task.Wait(); // Èeká na dokonèení
  int result = task.Result; // Získá výsledek (riziko deadlocku!)
  ```  

---

#### **4. Stavy `Task`**  

- **`Status`**: Urèuje stav úlohy (napø. `Created`, `Running`, `RanToCompletion`, `Faulted`, `Canceled`).  
- **Kontrola stavu**:  
  ```csharp
  if (task.Status == TaskStatus.RanToCompletion)
      Console.WriteLine("Úloha dokonèena.");
  ```  

---

#### **5. Zpracování výsledku**  

- **Pro `Task<T>`**:  
  ```csharp
  Task<string> task = Task.Run(() => "Hello");
  string text = await task; // "Hello"
  ```  
- **Pro `Task` bez výsledku**:  
  ```csharp
  Task task = Task.Run(() => Console.WriteLine("Hotovo"));
  await task;
  ```  

---

#### **6. Zrušení `Task`**  

- Použití `CancellationTokenSource` a `CancellationToken`:  
  ```csharp
  var cts = new CancellationTokenSource();
  Task task = Task.Run(() => 
  {
      while (true)
      {
          cts.Token.ThrowIfCancellationRequested(); // Kontrola zrušení
          // Práce...
      }
  }, cts.Token);

  cts.CancelAfter(5000); // Zruší úlohu po 5 sekundách
  ```  

---

#### **7. Zpracování výjimek**  

- Výjimky jsou zabaleny do `AggregateException`:  
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
- **Pro jednu úlohu**: Výjimka se propaguje pøímo (není zabalena).  

---

#### **8. Pokroèilé techniky**  

- **Kombinace více úloh**:  
  ```csharp
  Task<int> task1 = Task.Run(() => 10);
  Task<int> task2 = Task.Run(() => 20);
  int[] results = await Task.WhenAll(task1, task2); // [10, 20]
  ```  
  ```csharp
  Task<int> finishedTask = await Task.WhenAny(task1, task2); // Èeká na první dokonèenou
  ```  
- **Continuations (øetìzení)**:  
  ```csharp
  Task<int> parentTask = Task.Run(() => 5);
  Task continuation = parentTask.ContinueWith(prev => 
  {
      Console.WriteLine($"Pøedchozí výsledek: {prev.Result}");
  }, TaskContinuationOptions.OnlyOnRanToCompletion);
  ```  

---

#### **9. `Task` vs `ValueTask`**  

- **`ValueTask`**: Optimalizace pro pøípady, kdy je výsledek èasto dostupný synchronnì (napø. caching).  
  ```csharp
  public ValueTask<int> GetDataAsync()
  {
      if (_cachedData != null)
          return new ValueTask<int>(_cachedData); // Synchronní výsledek
      return new ValueTask<int>(LoadDataAsync()); // Asynchronní operace
  }
  ```  

---

#### **10. Doporuèené postupy**  

- Preferujte **`async`/`await`** pøed blokujícími metodami (jako `.Result` nebo `.Wait()`).  
- Pro dlouhodobé úlohy nastavte `TaskCreationOptions.LongRunning`.  
- Vždy ošetøujte **výjimky** a **zrušení**.  
- Pro knihovny používejte `ConfigureAwait(false)` k pøedejití deadlockù:  
  ```csharp
  await task.ConfigureAwait(false);
  ```  

---

#### **11. Èasté chyby**  

- **Deadlock**: Blokování hlavního vlákna pomocí `.Result` v UI aplikacích.  
- **Zapomenutí `await`**:  
  ```csharp
  Task.Run(() => Console.WriteLine("Ahoj")); // Úloha se spustí, ale nikdo na ni neèeká!
  ```  
- **Nadbyteèné vytváøení úloh**: Pro triviální operace používejte `Task.CompletedTask`.  

---

#### **12. Ukázka reálného použití**  

```csharp
public async Task<string> StahniWebovouStrankuAsync(string url)
{
    using (var client = new HttpClient())
    {
        return await client.GetStringAsync(url);
    }
}

// Volání
string obsah = await StahniWebovouStrankuAsync("https://example.com");
```
