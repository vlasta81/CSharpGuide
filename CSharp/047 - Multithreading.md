
### Multithreading v jazyce C#  

Multithreading umožňuje **souběžné provádění více operací** v rámci jednoho procesu, což zlepšuje výkon a responzivost aplikací. C# nabízí různé nástroje pro práci s vlákny, od nízké úrovně (`Thread`) po vysokou úrověň (`Task`, `async/await`). Zde je přehled klíčových konceptů:

---

#### **1. Základní pojmy**  

- **Vlákno (Thread)**: Nejméně náročná jednotka provádění v rámci procesu.  
- **Hlavní vlákno (Main Thread)**: Vstupní bod aplikace. Blokování hlavního vlákna (např. ve UI) způsobí „zamrznutí“.  
- **Kontextové přepínání**: Přepínání mezi vlákny řízené OS.  

---

#### **2. Třída `Thread`**  

- Ruční správa vláken (nízká úroveň).  
- **Příklad**:  
  ```csharp
  Thread vlakno = new Thread(() => 
  {
      Console.WriteLine("Vlákno spuštěno");
      Thread.Sleep(1000); // Simulace práce
  });
  vlakno.Start();
  ```  
- **Parametry**:  
  - `IsBackground`: Pokud `true`, vlákno nebrání ukončení procesu.  
  - `Priority`: Nastavení priority (`ThreadPriority.High`).  

---

#### **3. Thread Pool**  

- Fond předem vytvořených vláken pro **efektivní správu zdrojů**.  
- **Použití**:  
  ```csharp
  ThreadPool.QueueUserWorkItem((state) => 
  {
      Console.WriteLine("Práce ve Thread Poolu");
  });
  ```  
- **Vhodné pro**: Krátkodobé úlohy (dlouhé úlohy mohou vyčerpat pool).  

---

#### **4. Task Parallel Library (TPL)** 

- Vysokoúrovňová abstrakce pro paralelní programování.  
- **Třída `Task`**:  
  ```csharp
  Task.Run(() => 
  {
      Console.WriteLine("Task spuštěn");
  }).Wait(); // Čekání na dokončení
  ```  
- **Async/await**:  
  ```csharp
  async Task StahniDataAsync()
  {
      await Task.Delay(1000); // Asynchronní čekání
      Console.WriteLine("Data stažena");
  }
  ```  

---

#### **5. Synchronizace vláken**  

- **Lock (zámek)**:  
  ```csharp
  private object _lock = new object();
  lock (_lock)
  {
      // Kritická sekce
  }
  ```  
- **Monitor**:  
  ```csharp
  Monitor.Enter(_lock);
  try { /* ... */ }
  finally { Monitor.Exit(_lock); }
  ```  
- **Semaphore/Mutex**: Pro synchronizaci mezi procesy.  
- **AutoResetEvent/ManualResetEvent**: Signalizace mezi vlákny.  

---

#### **6. Concurrent Collections**  

- Thread-safe kolekce pro paralelní přístup:  
  - `ConcurrentQueue<T>`  
  - `ConcurrentDictionary<TKey, TValue>`  
  - `BlockingCollection<T>` (pro producer-consumer vzor).  

**Příklad producer-consumer**:  
```csharp
BlockingCollection<int> buffer = new BlockingCollection<int>();

// Producer
Task.Run(() => 
{
    for (int i = 0; i < 10; i++)
        buffer.Add(i);
    buffer.CompleteAdding();
});

// Consumer
Task.Run(() => 
{
    foreach (int item in buffer.GetConsumingEnumerable())
        Console.WriteLine(item);
});
```

---

#### **7. Parallel LINQ (PLINQ)**  

- Paralelní zpracování LINQ dotazů:  
  ```csharp
  var data = Enumerable.Range(1, 1000000);
  var vysledek = data.AsParallel()
                     .Where(x => x % 2 == 0)
                     .ToList();
  ```  

---

#### **8. Asynchronní vzory**  

- **CPU-bound operace**: `Task.Run` pro přesun do vlákna z Thread Poolu.  
- **I/O-bound operace**: `async/await` bez blokování vláken.  
- **Cancelace**: `CancellationTokenSource`:  
  ```csharp
  var cts = new CancellationTokenSource();
  Task.Run(() => 
  {
      while (!cts.Token.IsCancellationRequested)
      {
          // Práce...
      }
  }, cts.Token);

  cts.CancelAfter(5000); // Zrušení po 5 sekundách
  ```  

---

#### **9. Časté chyby**  

- **Race Conditions**: Soupeření vláken o sdílené zdroje.  
- **Deadlocky**: Vzájemné čekání na zámky (např. `lock(A); lock(B)` vs. `lock(B); lock(A)`).  
- **Async void**: Ztráta možnosti zachytit výjimky (používejte `async Task`).  

---

#### **10. Doporučené postupy**  

- Preferujte **async/await** před ruční správou vláken.  
- Pro sdílená data používejte **thread-safe kolekce** nebo synchronizaci.  
- Nepoužívejte `Task.Wait()` nebo `Task.Result` v asynchronním kódu (hrozí deadlocky).  
- Pro dlouhodobé úlohy nastavte `LongRunning` u `Task.Factory.StartNew`.  

---

#### **11. Pokročilé techniky**  

- **Channels**: Komunikační kanály pro asynchronní proudy dat.  
- **Interlocked**: Atomické operace (např. `Interlocked.Increment`).  
- **Memory Barriers**: Řešení problémů s paměťovou konzistencí.  
