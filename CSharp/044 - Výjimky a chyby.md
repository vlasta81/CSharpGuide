
### Výjimky a chyby v jazyce C#  

Výjimky (exceptions) jsou mechanismus pro **zachycení a zpracování chyb** bìhem bìhu programu. Umožòují oddìlit normální logiku od chybových stavù a zajistit kontrolovaný pád aplikace. Zde je pøehled klíèových konceptù:

---

#### **1. Základní struktura: `try`-`catch`-`finally`**  

- **`try`**: Blok kódu, kde mùže dojít k výjimce.  
- **`catch`**: Zpracuje konkrétní typ výjimky.  
- **`finally`**: Vždy se provede (èištìní zdrojù, napø. uzavøení souborù).  

**Pøíklad**:  
```csharp
try
{
    int cislo = int.Parse("abc"); // Vyvolá FormatException
}
catch (FormatException ex)
{
    Console.WriteLine($"Chybný formát: {ex.Message}");
}
catch (Exception ex) // Obecná výjimka (shora dolù – musí být poslední)
{
    Console.WriteLine($"Neznámá chyba: {ex.Message}");
}
finally
{
    Console.WriteLine("Uklízím zdroje...");
}
```

---

#### **2. Typy výjimek**  

- Všechny výjimky dìdí ze tøídy **`System.Exception`**.  
- **Bìžné vestavìné výjimky**:  
  - `FormatException`: Neplatný formát (napø. parsování øetìzce).  
  - `NullReferenceException`: Pokus o pøístup k `null` objektu.  
  - `ArgumentNullException`: Argument metody je `null`.  
  - `IndexOutOfRangeException`: Pøekroèení rozsahu pole.  
  - `InvalidOperationException`: Neplatná operace (napø. zmìna kolekce bìhem iterace).  

---

#### **3. Vyvolání výjimky: `throw`**  

- Lze vyvolat libovolnou výjimku pomocí `throw`.  
- **Doporuèení**: Zachovejte pùvodní stack trace pomocí `throw;` (ne `throw ex;`).  
```csharp
try
{
    // ...
}
catch (Exception ex)
{
    Console.WriteLine("Logování chyby...");
    throw; // Pùvodní výjimka se propaguje dál
}
```

---

#### **4. Vlastní výjimky**  

- Vytvoøte tøídu odvozenou od `Exception`.  
- **Pøíklad**:  
```csharp
public class MojException : Exception
{
    public MojException(string message) : base(message) { }
    public MojException(string message, Exception inner) : base(message, inner) { }
}

// Použití
throw new MojException("Vlastní chyba");
```

---

#### **5. Filtrování výjimek pomocí `when`**  

- Podmínìné zpracování výjimek:  
```csharp
try
{
    int x = 0;
    int y = 10 / x; // Vyvolá DivideByZeroException
}
catch (DivideByZeroException ex) when (x == 0)
{
    Console.WriteLine("Dìlení nulou!");
}
```

---

#### **6. Zpracování výjimek v asynchronním kódu**  

- Výjimky v `async` metodách se propagují jako `AggregateException` nebo pøes `await`:  
```csharp
try
{
    await Task.Run(() => { throw new InvalidOperationException(); });
}
catch (InvalidOperationException)
{
    Console.WriteLine("Chyba v asynchronní operaci");
}
```

---

#### **7. Doporuèené postupy**  

- **Specifické výjimky**: Chytejte nejdøíve konkrétní výjimky, poté obecné.  
- **Logování**: Vždy zaznamenávejte detaily výjimky (`Message`, `StackTrace`, `InnerException`).  
- **Obnova stavu**: V `finally` nebo pomocí `using` pro èištìní zdrojù (napø. `IDisposable`).  
- **Nepoužívejte prázdné `catch` bloky**:  
  ```csharp
  catch (Exception) { } // Špatnì – skrytá chyba!
  ```

---

#### **8. Èasté chyby**  

- **Ignorování výjimek**: Prázdné `catch` bloky.  
- **Pøíliš široké zachycení**: `catch (Exception ex)` bez další analýzy.  
- **Memory leaks**: Neuzavøení zdrojù (soubory, databáze) pøi chybì.  

---

#### **9. Nástroje a techniky pro ladìní**  

- **Debugger**: Breakpointy v `catch` blocích.  
- **Logování**: Nástroje jako Serilog, NLog nebo `System.Diagnostics.Trace`.  
- **Globalní zachycení**:  
  ```csharp
  AppDomain.CurrentDomain.UnhandledException += (sender, args) => 
  {
      Console.WriteLine("Nezachycená výjimka: " + args.ExceptionObject.ToString());
  };
  ```

---

#### **10. Ukázka použití `using` pro èištìní zdrojù**  

```csharp
using (var stream = new FileStream("soubor.txt", FileMode.Open))
{
    // Práce se souborem
} // Automaticky volá stream.Dispose() i pøi výjimce
```

---

#### **11. Výjimky vs. Návratové kódy**  

- **Výhody výjimek**:  
  - Oddìlení chybové logiky od bìžného kódu.  
  - Automatická propagace napøíè zásobníkem volání.  
- **Nevýhody**: Nároènost na výkon (používejte jen pro výjimeèné situace).  
