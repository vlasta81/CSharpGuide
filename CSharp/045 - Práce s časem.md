
### Práce s časem v jazyce C#  

Práce s datem a časem je klíčová pro mnoho aplikací. C# nabízí několik tříd pro manipulaci s časovými údaji, včetně `DateTime`, `DateOnly`, `TimeOnly`, `TimeSpan` a `DateTimeOffset`. Zde je přehled jejich použití:

---

#### **1. Třída `DateTime`**  

- Reprezentuje datum a čas (např. `12. 5. 2024 15:30:00`).  
- **Vlastnosti**:  
  - `Now`: Aktuální systémový čas.  
  - `UtcNow`: Aktuální čas v UTC.  
  - `Today`: Datum s časem nastaveným na půlnoc.  
- **Metody**:  
  ```csharp
  DateTime dnes = DateTime.Now;
  DateTime zitra = dnes.AddDays(1);
  DateTime vcera = dnes.AddDays(-1);
  ```  
- **Formátování**:  
  ```csharp
  string formatovano = dnes.ToString("dd.MM.yyyy HH:mm:ss"); // "12.05.2024 15:30:00"
  ```

---

#### **2. Třída `DateOnly` a `TimeOnly` (.NET 6+)**  

- **`DateOnly`**: Pouze datum (bez času).  
  ```csharp
  DateOnly datum = DateOnly.FromDateTime(DateTime.Now); // 12.05.2024
  ```  
- **`TimeOnly`**: Pouze čas (bez data).  
  ```csharp
  TimeOnly cas = TimeOnly.FromDateTime(DateTime.Now); // 15:30:00
  ```  
- **Výhody**: Eliminuje problémy s časovými pásmy a zjednodušuje práci, když není čas potřeba.

---

#### **3. Třída `TimeSpan`**  

- Reprezentuje časový interval (např. 2 hodiny 15 minut).  
- **Vytvoření**:  
  ```csharp
  TimeSpan interval = new TimeSpan(2, 15, 0); // 02:15:00
  TimeSpan interval2 = TimeSpan.FromHours(3.5); // 03:30:00
  ```  
- **Operace**:  
  ```csharp
  DateTime za3Hodiny = DateTime.Now.Add(TimeSpan.FromHours(3));
  TimeSpan rozdil = DateTime.Now - DateTime.Today; // Doba od půlnoci
  ```  
- **Formátování**:  
  ```csharp
  string casovyRozdil = interval.ToString(@"hh\:mm"); // "02:15"
  ```

---

#### **4. Třída `DateTimeOffset`**  

- Reprezentuje datum a čas s **offsetem časového pásma** (např. `2024-05-12T15:30:00+02:00`).  
- **Výhody**: Umožňuje přesnou práci s časovými pásmy.  
- **Příklad**:  
  ```csharp
  DateTimeOffset casSPasmem = DateTimeOffset.Now;
  DateTimeOffset utcCas = casSPasmem.ToUniversalTime();
  ```

---

#### **5. Parsování a konverze**  

- **Parsování z řetězce**:  
  ```csharp
  DateTime datum = DateTime.Parse("2024-05-12");
  bool uspech = DateTime.TryParse("12.5.2024", out DateTime result); // Bezpečnější varianta
  ```  
- **Vlastní formáty**:  
  ```csharp
  DateTime datum = DateTime.ParseExact("12/05/24", "dd/MM/yy", CultureInfo.InvariantCulture);
  ```

---

#### **6. Časová pásma a `TimeZoneInfo`**  

- Konverze mezi pásmy:  
  ```csharp
  TimeZoneInfo prazskePasmo = TimeZoneInfo.FindSystemTimeZoneById("Central Europe Standard Time");
  DateTimeOffset casVPraze = TimeZoneInfo.ConvertTimeBySystemTimeZoneId(DateTimeOffset.UtcNow, "Central Europe Standard Time");
  ```  
- **UTC vs. lokální čas**:  
  ```csharp
  DateTime utcCas = DateTime.UtcNow;
  DateTime lokalniCas = utcCas.ToLocalTime(); // Podle systémového pásma
  ```

---

#### **7. Běžné operace**  

- **Porovnávání**:  
  ```csharp
  bool jeVetsi = DateTime.Now > DateTime.Today;
  ```  
- **Extrakce částí**:  
  ```csharp
  int rok = DateTime.Now.Year;
  int minuta = DateTime.Now.Minute;
  ```  
- **Výpočet věku**:  
  ```csharp
  DateTime narozeni = new DateTime(2000, 1, 1);
  int vek = DateTime.Now.Year - narozeni.Year;
  if (DateTime.Now < narozeni.AddYears(vek)) vek--;
  ```

---

#### **8. Doporučené postupy**  

- Pro ukládání do databází používejte **UTC čas**.  
- Pro reprezentaci pouze data/času preferujte `DateOnly`/`TimeOnly` (od .NET 6).  
- Pro časová pásma vždy používejte `DateTimeOffset` místo `DateTime`.  
- Validujte vstupy pomocí `TryParse`, abyste předešli výjimkám.

---

#### **9. Časté chyby**  

- Ignorování časových pásem (např. u mezinárodních aplikací).  
- Záměna `DateTime.Now` a `DateTime.UtcNow`.  
- Ukládání `DateTime` bez specifikace `Kind` (Local/Utc/Unspecified).  

---

#### **10. Pokročilé techniky**  

- **Periodické úlohy**:  
  ```csharp
  using var timer = new PeriodicTimer(TimeSpan.FromMinutes(5));
  while (await timer.WaitForNextTickAsync())
  {
      // Spustí se každých 5 minut
  }
  ```  
- **Měření času**:  
  ```csharp
  var stopky = System.Diagnostics.Stopwatch.StartNew();
  // ... kód ...
  stopky.Stop();
  Console.WriteLine($"Uběhlo: {stopky.ElapsedMilliseconds} ms");
  ```
