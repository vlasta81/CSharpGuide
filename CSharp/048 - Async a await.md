
### Asynchronn� programov�n� s `async` a `await` v jazyce C# 

Asynchronn� programov�n� umo��uje **neblokuj�c� prov�d�n� operac�** (nap�. I/O, s�ov� vol�n�) a zlep�uje v�kon a responzivost aplikac�. Kl��ov� koncepty `async` a `await` zjednodu�uj� psan� asynchronn�ho k�du. Zde je p�ehled:

---

#### **1. Z�kladn� koncepty**  

- **`async`**: Modifik�tor metody, kter� ozna�uje, �e metoda obsahuje asynchronn� operace.  
- **`await`**: Oper�tor, kter� pozastav� prov�d�n� metody, dokud se nedokon�� asynchronn� operace (nap�. `Task`).  
- **V�hody**:  
  - Neblokuje hlavn� vl�kno (d�le�it� pro UI aplikace).  
  - Efektivn� vyu�it� vl�ken (nap�. ve webov�ch slu�b�ch).  

---

#### **2. Z�kladn� syntaxe**  

```csharp
public async Task<int> SpoctiSoucetAsync(int a, int b)
{
    await Task.Delay(1000); // Simulace asynchronn� operace (nap�. HTTP po�adavek)
    return a + b;
}

// Vol�n�
int vysledek = await SpoctiSoucetAsync(5, 3);
```  
- **N�vratov� typy**:  
  - `Task`: Metoda bez n�vratov� hodnoty.  
  - `Task<T>`: Metoda vrac� hodnotu typu `T`.  
  - `void`: Pouze pro obsluhy ud�lost� (nap�. `button.Click += async (s, e) => { ... }`).  

---

#### **3. Jak to funguje?**  

- Metoda ozna�en� `async` je kompil�torem p�evedena na **stavov� automat**.  
- P�i `await` se vl�kno uvoln� a po dokon�en� asynchronn� operace se prov�d�n� obnov� (nemus� na stejn�m vl�kn�!).  
- **Pozor**: `await` neblokuje vl�kno � umo��uje jeho op�tovn� vyu�it�.  

---

#### **4. Kl��ov� t��dy a n�stroje**  

- **`Task` a `Task<T>`**: Reprezentuj� asynchronn� operaci.  
- **`ValueTask` a `ValueTask<T>`**: Optimalizace pro operace, kter� �asto synchronn� dokon��.  
- **`CancellationToken`**: Zru�en� dlouhotrvaj�c� operace.  
- **`ConfigureAwait(false)`**: Potla�� obnoven� v p�vodn�m kontextu (nap�. pro knihovny).  

---

#### **5. Zpracov�n� chyb**  

- V�jimky v asynchronn�ch metod�ch se propaguj� do `Task.Exception` nebo p�i `await`.  
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
- **Zru�en� operace**:  
```csharp
var cts = new CancellationTokenSource();
cts.CancelAfter(5000); // Zru�� operaci po 5 sekund�ch

try
{
    await StahniDataAsync(cts.Token);
}
catch (TaskCanceledException)
{
    Console.WriteLine("Operace zru�ena.");
}
```

---

#### **6. Doporu�en� postupy**  

1. **Async all the way**: Vyhn�te se m�sen� synchronn�ho a asynchronn�ho k�du (nap�. `Result` nebo `Wait()`).  
2. **Pou��vejte `ConfigureAwait(false)`** v knihovn�ch (zabra�uje deadlock�m v konzolov�ch aplikac�ch).  
3. **Preferujte `Task`/`Task<T>`** p�ed `async void` (krom� obsluh ud�lost�).  
4. **O�et�ujte v�jimky** pomoc� `try/catch` kolem `await`.  

---

#### **7. �ast� chyby**  

- **Deadlock**: Blokov�n� asynchronn�ho k�du (nap�. `.Result` v UI vl�kn�).  
```csharp
// �PATN�:
var data = MetodaAsync().Result; // Blokuje vl�kno!

// SPR�VN�:
var data = await MetodaAsync();
```  
- **Zapomenut� `await`**:  
```csharp
public async Task UlozDataAsync()
{
    await Task.Delay(1000); // Bez await by se operace ignorovala!
}
```  
- **Nadbyte�n� pou�it� `async`**: Pokud metoda neobsahuje `await`, nen� `async` pot�eba.  

---

#### **8. Pokro�il� techniky**  

- **Paraleln� spou�t�n� �loh**:  
```csharp
var task1 = StahniDataAsync();
var task2 = NactiSouborAsync();
await Task.WhenAll(task1, task2); // �ek� na v�echny
// nebo
var vysledek = await Task.WhenAny(task1, task2); // �ek� na prvn� dokon�enou
```  
- **Asynchronn� streamy (C# 8+)**:  
```csharp
await foreach (var polozka in ZiskejDataAsync())
{
    Console.WriteLine(polozka);
}
```  
- **`IAsyncDisposable`**: Pro asynchronn� �i�t�n� zdroj�.  

---

#### **9. Kdy pou��t asynchronn� k�d?**  

- **I/O operace**: �ten� soubor�, HTTP po�adavky, datab�zov� dotazy.  
- **UI aplikace**: Zachov�n� responzivity (nap�. na��t�n� dat bez zamrznut�).  
- **Serverov� aplikace**: Zv��en� �k�lovatelnosti (efektivn� vyu�it� vl�ken).  
