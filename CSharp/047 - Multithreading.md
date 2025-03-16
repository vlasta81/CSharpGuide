
### Multithreading v jazyce C#  

Multithreading umoòuje **soubìné provádìní více operací** v rámci jednoho procesu, co zlepšuje vıkon a responzivost aplikací. C# nabízí rùzné nástroje pro práci s vlákny, od nízké úrovnì (`Thread`) po vysokou úrovìò (`Task`, `async/await`). Zde je pøehled klíèovıch konceptù:

---

#### **1. Základní pojmy**  

- **Vlákno (Thread)**: Nejménì nároèná jednotka provádìní v rámci procesu.  
- **Hlavní vlákno (Main Thread)**: Vstupní bod aplikace. Blokování hlavního vlákna (napø. ve UI) zpùsobí „zamrznutí“.  
- **Kontextové pøepínání**: Pøepínání mezi vlákny øízené OS.  

---

#### **2. Tøída `Thread`**  

- Ruèní správa vláken (nízká úroveò).  
- **Pøíklad**:  
  ```csharp
  Thread vlakno = new Thread(() => 
  {
      Console.WriteLine("Vlákno spuštìno");
      Thread.Sleep(1000); // Simulace práce
  });
  vlakno.Start();
  ```  
- **Parametry**:  
  - `IsBackground`: Pokud `true`, vlákno nebrání ukonèení procesu.  
  - `Priority`: Nastavení priority (`ThreadPriority.High`).  

---

#### **3. Thread Pool**  

- Fond pøedem vytvoøenıch vláken pro **efektivní správu zdrojù**.  
- **Pouití**:  
  ```csharp
  ThreadPool.QueueUserWorkItem((state) => 
  {
      Console.WriteLine("Práce ve Thread Poolu");
  });
  ```  
- **Vhodné pro**: Krátkodobé úlohy (dlouhé úlohy mohou vyèerpat pool).  

---

#### **4. Task Parallel Library (TPL)** 

- Vysokoúrovòová abstrakce pro paralelní programování.  
- **Tøída `Task`**:  
  ```csharp
  Task.Run(() => 
  {
      Console.WriteLine("Task spuštìn");
  }).Wait(); // Èekání na dokonèení
  ```  
- **Async/await**:  
  ```csharp
  async Task StahniDataAsync()
  {
      await Task.Delay(1000); // Asynchronní èekání
      Console.WriteLine("Data staena");
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

- Thread-safe kolekce pro paralelní pøístup:  
  - `ConcurrentQueue<T>`  
  - `ConcurrentDictionary<TKey, TValue>`  
  - `BlockingCollection<T>` (pro producer-consumer vzor).  

**Pøíklad producer-consumer**:  
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

- Paralelní zpracování LINQ dotazù:  
  ```csharp
  var data = Enumerable.Range(1, 1000000);
  var vysledek = data.AsParallel()
                     .Where(x => x % 2 == 0)
                     .ToList();
  ```  

---

#### **8. Asynchronní vzory**  

- **CPU-bound operace**: `Task.Run` pro pøesun do vlákna z Thread Poolu.  
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

#### **9. Èasté chyby**  

- **Race Conditions**: Soupeøení vláken o sdílené zdroje.  
- **Deadlocky**: Vzájemné èekání na zámky (napø. `lock(A); lock(B)` vs. `lock(B); lock(A)`).  
- **Async void**: Ztráta monosti zachytit vıjimky (pouívejte `async Task`).  

---

#### **10. Doporuèené postupy**  

- Preferujte **async/await** pøed ruèní správou vláken.  
- Pro sdílená data pouívejte **thread-safe kolekce** nebo synchronizaci.  
- Nepouívejte `Task.Wait()` nebo `Task.Result` v asynchronním kódu (hrozí deadlocky).  
- Pro dlouhodobé úlohy nastavte `LongRunning` u `Task.Factory.StartNew`.  

---

#### **11. Pokroèilé techniky**  

- **Channels**: Komunikaèní kanály pro asynchronní proudy dat.  
- **Interlocked**: Atomické operace (napø. `Interlocked.Increment`).  
- **Memory Barriers**: Øešení problémù s pamìovou konzistencí.  
