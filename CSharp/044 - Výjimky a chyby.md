
### Výjimky a chyby v jazyce C#  

Výjimky (exceptions) jsou mechanismus pro **zachycení a zpracování chyb** během běhu programu. Umožňují oddělit normální logiku od chybových stavů a zajistit kontrolovaný pád aplikace. Zde je přehled klíčových konceptů:

---

#### **1. Základní struktura: `try`-`catch`-`finally`**  

- **`try`**: Blok kódu, kde může dojít k výjimce.  
- **`catch`**: Zpracuje konkrétní typ výjimky.  
- **`finally`**: Vždy se provede (čištění zdrojů, např. uzavření souborů).  

**Příklad**:  
```csharp
try
{
    int cislo = int.Parse("abc"); // Vyvolá FormatException
}
catch (FormatException ex)
{
    Console.WriteLine($"Chybný formát: {ex.Message}");
}
catch (Exception ex) // Obecná výjimka (shora dolů – musí být poslední)
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

- Všechny výjimky dědí ze třídy **`System.Exception`**.  
- **Běžné vestavěné výjimky**:  
  - `FormatException`: Neplatný formát (např. parsování řetězce).  
  - `NullReferenceException`: Pokus o přístup k `null` objektu.  
  - `ArgumentNullException`: Argument metody je `null`.  
  - `IndexOutOfRangeException`: Překročení rozsahu pole.  
  - `InvalidOperationException`: Neplatná operace (např. změna kolekce během iterace).  

---

#### **3. Vyvolání výjimky: `throw`**  

- Lze vyvolat libovolnou výjimku pomocí `throw`.  
- **Doporučení**: Zachovejte původní stack trace pomocí `throw;` (ne `throw ex;`).  
```csharp
try
{
    // ...
}
catch (Exception ex)
{
    Console.WriteLine("Logování chyby...");
    throw; // Původní výjimka se propaguje dál
}
```

---

#### **4. Vlastní výjimky**  

- Vytvořte třídu odvozenou od `Exception`.  
- **Příklad**:  
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

- Podmíněné zpracování výjimek:  
```csharp
try
{
    int x = 0;
    int y = 10 / x; // Vyvolá DivideByZeroException
}
catch (DivideByZeroException ex) when (x == 0)
{
    Console.WriteLine("Dělení nulou!");
}
```

---

#### **6. Zpracování výjimek v asynchronním kódu**  

- Výjimky v `async` metodách se propagují jako `AggregateException` nebo přes `await`:  
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

#### **7. Doporučené postupy**  

- **Specifické výjimky**: Chytejte nejdříve konkrétní výjimky, poté obecné.  
- **Logování**: Vždy zaznamenávejte detaily výjimky (`Message`, `StackTrace`, `InnerException`).  
- **Obnova stavu**: V `finally` nebo pomocí `using` pro čištění zdrojů (např. `IDisposable`).  
- **Nepoužívejte prázdné `catch` bloky**:  
  ```csharp
  catch (Exception) { } // Špatně – skrytá chyba!
  ```

---

#### **8. Časté chyby**  

- **Ignorování výjimek**: Prázdné `catch` bloky.  
- **Příliš široké zachycení**: `catch (Exception ex)` bez další analýzy.  
- **Memory leaks**: Neuzavření zdrojů (soubory, databáze) při chybě.  

---

#### **9. Nástroje a techniky pro ladění**  

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

#### **10. Ukázka použití `using` pro čištění zdrojů**  

```csharp
using (var stream = new FileStream("soubor.txt", FileMode.Open))
{
    // Práce se souborem
} // Automaticky volá stream.Dispose() i při výjimce
```

---

#### **11. Výjimky vs. Návratové kódy**  

- **Výhody výjimek**:  
  - Oddělení chybové logiky od běžného kódu.  
  - Automatická propagace napříč zásobníkem volání.  
- **Nevýhody**: Náročnost na výkon (používejte jen pro výjimečné situace).  
