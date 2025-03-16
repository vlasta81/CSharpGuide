
### V�jimky a chyby v jazyce C#  

V�jimky (exceptions) jsou mechanismus pro **zachycen� a zpracov�n� chyb** b�hem b�hu programu. Umo��uj� odd�lit norm�ln� logiku od chybov�ch stav� a zajistit kontrolovan� p�d aplikace. Zde je p�ehled kl��ov�ch koncept�:

---

#### **1. Z�kladn� struktura: `try`-`catch`-`finally`**  

- **`try`**: Blok k�du, kde m��e doj�t k v�jimce.  
- **`catch`**: Zpracuje konkr�tn� typ v�jimky.  
- **`finally`**: V�dy se provede (�i�t�n� zdroj�, nap�. uzav�en� soubor�).  

**P��klad**:  
```csharp
try
{
    int cislo = int.Parse("abc"); // Vyvol� FormatException
}
catch (FormatException ex)
{
    Console.WriteLine($"Chybn� form�t: {ex.Message}");
}
catch (Exception ex) // Obecn� v�jimka (shora dol� � mus� b�t posledn�)
{
    Console.WriteLine($"Nezn�m� chyba: {ex.Message}");
}
finally
{
    Console.WriteLine("Ukl�z�m zdroje...");
}
```

---

#### **2. Typy v�jimek**  

- V�echny v�jimky d�d� ze t��dy **`System.Exception`**.  
- **B�n� vestav�n� v�jimky**:  
  - `FormatException`: Neplatn� form�t (nap�. parsov�n� �et�zce).  
  - `NullReferenceException`: Pokus o p��stup k `null` objektu.  
  - `ArgumentNullException`: Argument metody je `null`.  
  - `IndexOutOfRangeException`: P�ekro�en� rozsahu pole.  
  - `InvalidOperationException`: Neplatn� operace (nap�. zm�na kolekce b�hem iterace).  

---

#### **3. Vyvol�n� v�jimky: `throw`**  

- Lze vyvolat libovolnou v�jimku pomoc� `throw`.  
- **Doporu�en�**: Zachovejte p�vodn� stack trace pomoc� `throw;` (ne `throw ex;`).  
```csharp
try
{
    // ...
}
catch (Exception ex)
{
    Console.WriteLine("Logov�n� chyby...");
    throw; // P�vodn� v�jimka se propaguje d�l
}
```

---

#### **4. Vlastn� v�jimky**  

- Vytvo�te t��du odvozenou od `Exception`.  
- **P��klad**:  
```csharp
public class MojException : Exception
{
    public MojException(string message) : base(message) { }
    public MojException(string message, Exception inner) : base(message, inner) { }
}

// Pou�it�
throw new MojException("Vlastn� chyba");
```

---

#### **5. Filtrov�n� v�jimek pomoc� `when`**  

- Podm�n�n� zpracov�n� v�jimek:  
```csharp
try
{
    int x = 0;
    int y = 10 / x; // Vyvol� DivideByZeroException
}
catch (DivideByZeroException ex) when (x == 0)
{
    Console.WriteLine("D�len� nulou!");
}
```

---

#### **6. Zpracov�n� v�jimek v asynchronn�m k�du**  

- V�jimky v `async` metod�ch se propaguj� jako `AggregateException` nebo p�es `await`:  
```csharp
try
{
    await Task.Run(() => { throw new InvalidOperationException(); });
}
catch (InvalidOperationException)
{
    Console.WriteLine("Chyba v asynchronn� operaci");
}
```

---

#### **7. Doporu�en� postupy**  

- **Specifick� v�jimky**: Chytejte nejd��ve konkr�tn� v�jimky, pot� obecn�.  
- **Logov�n�**: V�dy zaznamen�vejte detaily v�jimky (`Message`, `StackTrace`, `InnerException`).  
- **Obnova stavu**: V `finally` nebo pomoc� `using` pro �i�t�n� zdroj� (nap�. `IDisposable`).  
- **Nepou��vejte pr�zdn� `catch` bloky**:  
  ```csharp
  catch (Exception) { } // �patn� � skryt� chyba!
  ```

---

#### **8. �ast� chyby**  

- **Ignorov�n� v�jimek**: Pr�zdn� `catch` bloky.  
- **P��li� �irok� zachycen�**: `catch (Exception ex)` bez dal�� anal�zy.  
- **Memory leaks**: Neuzav�en� zdroj� (soubory, datab�ze) p�i chyb�.  

---

#### **9. N�stroje a techniky pro lad�n�**  

- **Debugger**: Breakpointy v `catch` bloc�ch.  
- **Logov�n�**: N�stroje jako Serilog, NLog nebo `System.Diagnostics.Trace`.  
- **Globaln� zachycen�**:  
  ```csharp
  AppDomain.CurrentDomain.UnhandledException += (sender, args) => 
  {
      Console.WriteLine("Nezachycen� v�jimka: " + args.ExceptionObject.ToString());
  };
  ```

---

#### **10. Uk�zka pou�it� `using` pro �i�t�n� zdroj�**  

```csharp
using (var stream = new FileStream("soubor.txt", FileMode.Open))
{
    // Pr�ce se souborem
} // Automaticky vol� stream.Dispose() i p�i v�jimce
```

---

#### **11. V�jimky vs. N�vratov� k�dy**  

- **V�hody v�jimek**:  
  - Odd�len� chybov� logiky od b�n�ho k�du.  
  - Automatick� propagace nap��� z�sobn�kem vol�n�.  
- **Nev�hody**: N�ro�nost na v�kon (pou��vejte jen pro v�jime�n� situace).  
