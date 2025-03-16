
### Multithreading v jazyce C#  

Multithreading umo��uje **soub�n� prov�d�n� v�ce operac�** v r�mci jednoho procesu, co� zlep�uje v�kon a responzivost aplikac�. C# nab�z� r�zn� n�stroje pro pr�ci s vl�kny, od n�zk� �rovn� (`Thread`) po vysokou �rov�� (`Task`, `async/await`). Zde je p�ehled kl��ov�ch koncept�:

---

#### **1. Z�kladn� pojmy**  

- **Vl�kno (Thread)**: Nejm�n� n�ro�n� jednotka prov�d�n� v r�mci procesu.  
- **Hlavn� vl�kno (Main Thread)**: Vstupn� bod aplikace. Blokov�n� hlavn�ho vl�kna (nap�. ve UI) zp�sob� �zamrznut�.  
- **Kontextov� p�ep�n�n�**: P�ep�n�n� mezi vl�kny ��zen� OS.  

---

#### **2. T��da `Thread`**  

- Ru�n� spr�va vl�ken (n�zk� �rove�).  
- **P��klad**:  
  ```csharp
  Thread vlakno = new Thread(() => 
  {
      Console.WriteLine("Vl�kno spu�t�no");
      Thread.Sleep(1000); // Simulace pr�ce
  });
  vlakno.Start();
  ```  
- **Parametry**:  
  - `IsBackground`: Pokud `true`, vl�kno nebr�n� ukon�en� procesu.  
  - `Priority`: Nastaven� priority (`ThreadPriority.High`).  

---

#### **3. Thread Pool**  

- Fond p�edem vytvo�en�ch vl�ken pro **efektivn� spr�vu zdroj�**.  
- **Pou�it�**:  
  ```csharp
  ThreadPool.QueueUserWorkItem((state) => 
  {
      Console.WriteLine("Pr�ce ve Thread Poolu");
  });
  ```  
- **Vhodn� pro**: Kr�tkodob� �lohy (dlouh� �lohy mohou vy�erpat pool).  

---

#### **4. Task Parallel Library (TPL)** 

- Vysoko�rov�ov� abstrakce pro paraleln� programov�n�.  
- **T��da `Task`**:  
  ```csharp
  Task.Run(() => 
  {
      Console.WriteLine("Task spu�t�n");
  }).Wait(); // �ek�n� na dokon�en�
  ```  
- **Async/await**:  
  ```csharp
  async Task StahniDataAsync()
  {
      await Task.Delay(1000); // Asynchronn� �ek�n�
      Console.WriteLine("Data sta�ena");
  }
  ```  

---

#### **5. Synchronizace vl�ken**  

- **Lock (z�mek)**:  
  ```csharp
  private object _lock = new object();
  lock (_lock)
  {
      // Kritick� sekce
  }
  ```  
- **Monitor**:  
  ```csharp
  Monitor.Enter(_lock);
  try { /* ... */ }
  finally { Monitor.Exit(_lock); }
  ```  
- **Semaphore/Mutex**: Pro synchronizaci mezi procesy.  
- **AutoResetEvent/ManualResetEvent**: Signalizace mezi vl�kny.  

---

#### **6. Concurrent Collections**  

- Thread-safe kolekce pro paraleln� p��stup:  
  - `ConcurrentQueue<T>`  
  - `ConcurrentDictionary<TKey, TValue>`  
  - `BlockingCollection<T>` (pro producer-consumer vzor).  

**P��klad producer-consumer**:  
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

- Paraleln� zpracov�n� LINQ dotaz�:  
  ```csharp
  var data = Enumerable.Range(1, 1000000);
  var vysledek = data.AsParallel()
                     .Where(x => x % 2 == 0)
                     .ToList();
  ```  

---

#### **8. Asynchronn� vzory**  

- **CPU-bound operace**: `Task.Run` pro p�esun do vl�kna z Thread Poolu.  
- **I/O-bound operace**: `async/await` bez blokov�n� vl�ken.  
- **Cancelace**: `CancellationTokenSource`:  
  ```csharp
  var cts = new CancellationTokenSource();
  Task.Run(() => 
  {
      while (!cts.Token.IsCancellationRequested)
      {
          // Pr�ce...
      }
  }, cts.Token);

  cts.CancelAfter(5000); // Zru�en� po 5 sekund�ch
  ```  

---

#### **9. �ast� chyby**  

- **Race Conditions**: Soupe�en� vl�ken o sd�len� zdroje.  
- **Deadlocky**: Vz�jemn� �ek�n� na z�mky (nap�. `lock(A); lock(B)` vs. `lock(B); lock(A)`).  
- **Async void**: Ztr�ta mo�nosti zachytit v�jimky (pou��vejte `async Task`).  

---

#### **10. Doporu�en� postupy**  

- Preferujte **async/await** p�ed ru�n� spr�vou vl�ken.  
- Pro sd�len� data pou��vejte **thread-safe kolekce** nebo synchronizaci.  
- Nepou��vejte `Task.Wait()` nebo `Task.Result` v asynchronn�m k�du (hroz� deadlocky).  
- Pro dlouhodob� �lohy nastavte `LongRunning` u `Task.Factory.StartNew`.  

---

#### **11. Pokro�il� techniky**  

- **Channels**: Komunika�n� kan�ly pro asynchronn� proudy dat.  
- **Interlocked**: Atomick� operace (nap�. `Interlocked.Increment`).  
- **Memory Barriers**: �e�en� probl�m� s pam�ovou konzistenc�.  
