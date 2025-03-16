
### Práce s třídou `Task` v jazyce C#  

Třída `Task` je klíčovou součástí **Task Parallel Library (TPL)** a slouží k reprezentaci asynchronní nebo paralelní operace. Umožňuje efektivní správu vláken, zpracování výsledků a ošetření chyb. Zde je přehled klíčových konceptů:

---

#### **1. Co je `Task`?**  

- Reprezentuje **jednu asynchronní operaci**, která může (ale nemusí) vrátit výsledek (`Task<TResult>`).  
- **Výhody**:  
  - Zjednodušuje asynchronní a paralelní programování.  
  - Automaticky využívá **Thread Pool** pro efektivní správu vláken.  
  - Podporuje řetězení operací (continuations) a zpracování výjimek.  

---

#### **2. Vytvoření a spuštění `Task`**  

- **Implicitní spuštění pomocí `Task.Run`** (pro CPU-bound operace):  
  ```csharp
  Task<int> task = Task.Run(() => 
  {
      Thread.Sleep(1000);
      return 42;
  });
  ```  
- **Explicitní vytvoření a spuštění**:  
  ```csharp
  var task = new Task(() => Console.WriteLine("Spuštěno"));
  task.Start();
  ```  
- **Pomocí `Task.Factory.StartNew`** (pokročilejší konfigurace):  
  ```csharp
  Task task = Task.Factory.StartNew(() => { /* ... */ }, TaskCreationOptions.LongRunning);
  ```  

---

#### **3. Čekání na dokončení `Task`**  

- **`await`**: Asynchronní čekání (neblokuje vlákno):  
  ```csharp
  int vysledek = await task;
  ```  
- **Blokující čekání** (pouze ve specifických scénářích!):  
  ```csharp
  task.Wait(); // Čeká na dokončení
  int result = task.Result; // Získá výsledek (riziko deadlocku!)
  ```  

---

#### **4. Stavy `Task`**  

- **`Status`**: Určuje stav úlohy (např. `Created`, `Running`, `RanToCompletion`, `Faulted`, `Canceled`).  
- **Kontrola stavu**:  
  ```csharp
  if (task.Status == TaskStatus.RanToCompletion)
      Console.WriteLine("Úloha dokončena.");
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
- **Pro jednu úlohu**: Výjimka se propaguje přímo (není zabalena).  

---

#### **8. Pokročilé techniky**  

- **Kombinace více úloh**:  
  ```csharp
  Task<int> task1 = Task.Run(() => 10);
  Task<int> task2 = Task.Run(() => 20);
  int[] results = await Task.WhenAll(task1, task2); // [10, 20]
  ```  
  ```csharp
  Task<int> finishedTask = await Task.WhenAny(task1, task2); // Čeká na první dokončenou
  ```  
- **Continuations (řetězení)**:  
  ```csharp
  Task<int> parentTask = Task.Run(() => 5);
  Task continuation = parentTask.ContinueWith(prev => 
  {
      Console.WriteLine($"Předchozí výsledek: {prev.Result}");
  }, TaskContinuationOptions.OnlyOnRanToCompletion);
  ```  

---

#### **9. `Task` vs `ValueTask`**  

- **`ValueTask`**: Optimalizace pro případy, kdy je výsledek často dostupný synchronně (např. caching).  
  ```csharp
  public ValueTask<int> GetDataAsync()
  {
      if (_cachedData != null)
          return new ValueTask<int>(_cachedData); // Synchronní výsledek
      return new ValueTask<int>(LoadDataAsync()); // Asynchronní operace
  }
  ```  

---

#### **10. Doporučené postupy**  

- Preferujte **`async`/`await`** před blokujícími metodami (jako `.Result` nebo `.Wait()`).  
- Pro dlouhodobé úlohy nastavte `TaskCreationOptions.LongRunning`.  
- Vždy ošetřujte **výjimky** a **zrušení**.  
- Pro knihovny používejte `ConfigureAwait(false)` k předejití deadlocků:  
  ```csharp
  await task.ConfigureAwait(false);
  ```  

---

#### **11. Časté chyby**  

- **Deadlock**: Blokování hlavního vlákna pomocí `.Result` v UI aplikacích.  
- **Zapomenutí `await`**:  
  ```csharp
  Task.Run(() => Console.WriteLine("Ahoj")); // Úloha se spustí, ale nikdo na ni nečeká!
  ```  
- **Nadbytečné vytváření úloh**: Pro triviální operace používejte `Task.CompletedTask`.  

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
