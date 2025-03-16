
### Asynchronní programování s `async` a `await` v jazyce C# 

Asynchronní programování umožňuje **neblokující provádění operací** (např. I/O, síťové volání) a zlepšuje výkon a responzivost aplikací. Klíčové koncepty `async` a `await` zjednodušují psaní asynchronního kódu. Zde je přehled:

---

#### **1. Základní koncepty**  

- **`async`**: Modifikátor metody, který označuje, že metoda obsahuje asynchronní operace.  
- **`await`**: Operátor, který pozastaví provádění metody, dokud se nedokončí asynchronní operace (např. `Task`).  
- **Výhody**:  
  - Neblokuje hlavní vlákno (důležité pro UI aplikace).  
  - Efektivní využití vláken (např. ve webových službách).  

---

#### **2. Základní syntaxe**  

```csharp
public async Task<int> SpoctiSoucetAsync(int a, int b)
{
    await Task.Delay(1000); // Simulace asynchronní operace (např. HTTP požadavek)
    return a + b;
}

// Volání
int vysledek = await SpoctiSoucetAsync(5, 3);
```  
- **Návratové typy**:  
  - `Task`: Metoda bez návratové hodnoty.  
  - `Task<T>`: Metoda vrací hodnotu typu `T`.  
  - `void`: Pouze pro obsluhy událostí (např. `button.Click += async (s, e) => { ... }`).  

---

#### **3. Jak to funguje?**  

- Metoda označená `async` je kompilátorem převedena na **stavový automat**.  
- Při `await` se vlákno uvolní a po dokončení asynchronní operace se provádění obnoví (nemusí na stejném vlákně!).  
- **Pozor**: `await` neblokuje vlákno – umožňuje jeho opětovné využití.  

---

#### **4. Klíčové třídy a nástroje**  

- **`Task` a `Task<T>`**: Reprezentují asynchronní operaci.  
- **`ValueTask` a `ValueTask<T>`**: Optimalizace pro operace, které často synchronně dokončí.  
- **`CancellationToken`**: Zrušení dlouhotrvající operace.  
- **`ConfigureAwait(false)`**: Potlačí obnovení v původním kontextu (např. pro knihovny).  

---

#### **5. Zpracování chyb**  

- Výjimky v asynchronních metodách se propagují do `Task.Exception` nebo při `await`.  
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

#### **6. Doporučené postupy**  

1. **Async all the way**: Vyhněte se mísení synchronního a asynchronního kódu (např. `Result` nebo `Wait()`).  
2. **Používejte `ConfigureAwait(false)`** v knihovnách (zabraňuje deadlockům v konzolových aplikacích).  
3. **Preferujte `Task`/`Task<T>`** před `async void` (kromě obsluh událostí).  
4. **Ošetřujte výjimky** pomocí `try/catch` kolem `await`.  

---

#### **7. Časté chyby**  

- **Deadlock**: Blokování asynchronního kódu (např. `.Result` v UI vlákně).  
```csharp
// ŠPATNĚ:
var data = MetodaAsync().Result; // Blokuje vlákno!

// SPRÁVNĚ:
var data = await MetodaAsync();
```  
- **Zapomenutí `await`**:  
```csharp
public async Task UlozDataAsync()
{
    await Task.Delay(1000); // Bez await by se operace ignorovala!
}
```  
- **Nadbytečné použití `async`**: Pokud metoda neobsahuje `await`, není `async` potřeba.  

---

#### **8. Pokročilé techniky**  

- **Paralelní spouštění úloh**:  
```csharp
var task1 = StahniDataAsync();
var task2 = NactiSouborAsync();
await Task.WhenAll(task1, task2); // Čeká na všechny
// nebo
var vysledek = await Task.WhenAny(task1, task2); // Čeká na první dokončenou
```  
- **Asynchronní streamy (C# 8+)**:  
```csharp
await foreach (var polozka in ZiskejDataAsync())
{
    Console.WriteLine(polozka);
}
```  
- **`IAsyncDisposable`**: Pro asynchronní čištění zdrojů.  

---

#### **9. Kdy použít asynchronní kód?**  

- **I/O operace**: Čtení souborů, HTTP požadavky, databázové dotazy.  
- **UI aplikace**: Zachování responzivity (např. načítání dat bez zamrznutí).  
- **Serverové aplikace**: Zvýšení škálovatelnosti (efektivní využití vláken).  
