
### Asynchronní programování s `async` a `await` v jazyce C# 

Asynchronní programování umoòuje **neblokující provádìní operací** (napø. I/O, síové volání) a zlepšuje vıkon a responzivost aplikací. Klíèové koncepty `async` a `await` zjednodušují psaní asynchronního kódu. Zde je pøehled:

---

#### **1. Základní koncepty**  

- **`async`**: Modifikátor metody, kterı oznaèuje, e metoda obsahuje asynchronní operace.  
- **`await`**: Operátor, kterı pozastaví provádìní metody, dokud se nedokonèí asynchronní operace (napø. `Task`).  
- **Vıhody**:  
  - Neblokuje hlavní vlákno (dùleité pro UI aplikace).  
  - Efektivní vyuití vláken (napø. ve webovıch slubách).  

---

#### **2. Základní syntaxe**  

```csharp
public async Task<int> SpoctiSoucetAsync(int a, int b)
{
    await Task.Delay(1000); // Simulace asynchronní operace (napø. HTTP poadavek)
    return a + b;
}

// Volání
int vysledek = await SpoctiSoucetAsync(5, 3);
```  
- **Návratové typy**:  
  - `Task`: Metoda bez návratové hodnoty.  
  - `Task<T>`: Metoda vrací hodnotu typu `T`.  
  - `void`: Pouze pro obsluhy událostí (napø. `button.Click += async (s, e) => { ... }`).  

---

#### **3. Jak to funguje?**  

- Metoda oznaèená `async` je kompilátorem pøevedena na **stavovı automat**.  
- Pøi `await` se vlákno uvolní a po dokonèení asynchronní operace se provádìní obnoví (nemusí na stejném vláknì!).  
- **Pozor**: `await` neblokuje vlákno – umoòuje jeho opìtovné vyuití.  

---

#### **4. Klíèové tøídy a nástroje**  

- **`Task` a `Task<T>`**: Reprezentují asynchronní operaci.  
- **`ValueTask` a `ValueTask<T>`**: Optimalizace pro operace, které èasto synchronnì dokonèí.  
- **`CancellationToken`**: Zrušení dlouhotrvající operace.  
- **`ConfigureAwait(false)`**: Potlaèí obnovení v pùvodním kontextu (napø. pro knihovny).  

---

#### **5. Zpracování chyb**  

- Vıjimky v asynchronních metodách se propagují do `Task.Exception` nebo pøi `await`.  
```csharp
try
{
    await MetodaKteraMuzeSelhatAsync();
}
catch (HttpRequestException ex)
{
    Console.WriteLine($"Chyba: {ex.Message}");
}
```  
- **Zrušení operace**:  
```csharp
var cts = new CancellationTokenSource();
cts.CancelAfter(5000); // Zruší operaci po 5 sekundách

try
{
    await StahniDataAsync(cts.Token);
}
catch (TaskCanceledException)
{
    Console.WriteLine("Operace zrušena.");
}
```

---

#### **6. Doporuèené postupy**  

1. **Async all the way**: Vyhnìte se mísení synchronního a asynchronního kódu (napø. `Result` nebo `Wait()`).  
2. **Pouívejte `ConfigureAwait(false)`** v knihovnách (zabraòuje deadlockùm v konzolovıch aplikacích).  
3. **Preferujte `Task`/`Task<T>`** pøed `async void` (kromì obsluh událostí).  
4. **Ošetøujte vıjimky** pomocí `try/catch` kolem `await`.  

---

#### **7. Èasté chyby**  

- **Deadlock**: Blokování asynchronního kódu (napø. `.Result` v UI vláknì).  
```csharp
// ŠPATNÌ:
var data = MetodaAsync().Result; // Blokuje vlákno!

// SPRÁVNÌ:
var data = await MetodaAsync();
```  
- **Zapomenutí `await`**:  
```csharp
public async Task UlozDataAsync()
{
    await Task.Delay(1000); // Bez await by se operace ignorovala!
}
```  
- **Nadbyteèné pouití `async`**: Pokud metoda neobsahuje `await`, není `async` potøeba.  

---

#### **8. Pokroèilé techniky**  

- **Paralelní spouštìní úloh**:  
```csharp
var task1 = StahniDataAsync();
var task2 = NactiSouborAsync();
await Task.WhenAll(task1, task2); // Èeká na všechny
// nebo
var vysledek = await Task.WhenAny(task1, task2); // Èeká na první dokonèenou
```  
- **Asynchronní streamy (C# 8+)**:  
```csharp
await foreach (var polozka in ZiskejDataAsync())
{
    Console.WriteLine(polozka);
}
```  
- **`IAsyncDisposable`**: Pro asynchronní èištìní zdrojù.  

---

#### **9. Kdy pouít asynchronní kód?**  

- **I/O operace**: Ètení souborù, HTTP poadavky, databázové dotazy.  
- **UI aplikace**: Zachování responzivity (napø. naèítání dat bez zamrznutí).  
- **Serverové aplikace**: Zvıšení škálovatelnosti (efektivní vyuití vláken).  
