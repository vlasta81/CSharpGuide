
### Práce s èasem v jazyce C#  

Práce s datem a èasem je klíèová pro mnoho aplikací. C# nabízí nìkolik tøíd pro manipulaci s èasovými údaji, vèetnì `DateTime`, `DateOnly`, `TimeOnly`, `TimeSpan` a `DateTimeOffset`. Zde je pøehled jejich použití:

---

#### **1. Tøída `DateTime`**  

- Reprezentuje datum a èas (napø. `12. 5. 2024 15:30:00`).  
- **Vlastnosti**:  
  - `Now`: Aktuální systémový èas.  
  - `UtcNow`: Aktuální èas v UTC.  
  - `Today`: Datum s èasem nastaveným na pùlnoc.  
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

#### **2. Tøída `DateOnly` a `TimeOnly` (.NET 6+)**  

- **`DateOnly`**: Pouze datum (bez èasu).  
  ```csharp
  DateOnly datum = DateOnly.FromDateTime(DateTime.Now); // 12.05.2024
  ```  
- **`TimeOnly`**: Pouze èas (bez data).  
  ```csharp
  TimeOnly cas = TimeOnly.FromDateTime(DateTime.Now); // 15:30:00
  ```  
- **Výhody**: Eliminuje problémy s èasovými pásmy a zjednodušuje práci, když není èas potøeba.

---

#### **3. Tøída `TimeSpan`**  

- Reprezentuje èasový interval (napø. 2 hodiny 15 minut).  
- **Vytvoøení**:  
  ```csharp
  TimeSpan interval = new TimeSpan(2, 15, 0); // 02:15:00
  TimeSpan interval2 = TimeSpan.FromHours(3.5); // 03:30:00
  ```  
- **Operace**:  
  ```csharp
  DateTime za3Hodiny = DateTime.Now.Add(TimeSpan.FromHours(3));
  TimeSpan rozdil = DateTime.Now - DateTime.Today; // Doba od pùlnoci
  ```  
- **Formátování**:  
  ```csharp
  string casovyRozdil = interval.ToString(@"hh\:mm"); // "02:15"
  ```

---

#### **4. Tøída `DateTimeOffset`**  

- Reprezentuje datum a èas s **offsetem èasového pásma** (napø. `2024-05-12T15:30:00+02:00`).  
- **Výhody**: Umožòuje pøesnou práci s èasovými pásmy.  
- **Pøíklad**:  
  ```csharp
  DateTimeOffset casSPasmem = DateTimeOffset.Now;
  DateTimeOffset utcCas = casSPasmem.ToUniversalTime();
  ```

---

#### **5. Parsování a konverze**  

- **Parsování z øetìzce**:  
  ```csharp
  DateTime datum = DateTime.Parse("2024-05-12");
  bool uspech = DateTime.TryParse("12.5.2024", out DateTime result); // Bezpeènìjší varianta
  ```  
- **Vlastní formáty**:  
  ```csharp
  DateTime datum = DateTime.ParseExact("12/05/24", "dd/MM/yy", CultureInfo.InvariantCulture);
  ```

---

#### **6. Èasová pásma a `TimeZoneInfo`**  

- Konverze mezi pásmy:  
  ```csharp
  TimeZoneInfo prazskePasmo = TimeZoneInfo.FindSystemTimeZoneById("Central Europe Standard Time");
  DateTimeOffset casVPraze = TimeZoneInfo.ConvertTimeBySystemTimeZoneId(DateTimeOffset.UtcNow, "Central Europe Standard Time");
  ```  
- **UTC vs. lokální èas**:  
  ```csharp
  DateTime utcCas = DateTime.UtcNow;
  DateTime lokalniCas = utcCas.ToLocalTime(); // Podle systémového pásma
  ```

---

#### **7. Bìžné operace**  

- **Porovnávání**:  
  ```csharp
  bool jeVetsi = DateTime.Now > DateTime.Today;
  ```  
- **Extrakce èástí**:  
  ```csharp
  int rok = DateTime.Now.Year;
  int minuta = DateTime.Now.Minute;
  ```  
- **Výpoèet vìku**:  
  ```csharp
  DateTime narozeni = new DateTime(2000, 1, 1);
  int vek = DateTime.Now.Year - narozeni.Year;
  if (DateTime.Now < narozeni.AddYears(vek)) vek--;
  ```

---

#### **8. Doporuèené postupy**  

- Pro ukládání do databází používejte **UTC èas**.  
- Pro reprezentaci pouze data/èasu preferujte `DateOnly`/`TimeOnly` (od .NET 6).  
- Pro èasová pásma vždy používejte `DateTimeOffset` místo `DateTime`.  
- Validujte vstupy pomocí `TryParse`, abyste pøedešli výjimkám.

---

#### **9. Èasté chyby**  

- Ignorování èasových pásem (napø. u mezinárodních aplikací).  
- Zámìna `DateTime.Now` a `DateTime.UtcNow`.  
- Ukládání `DateTime` bez specifikace `Kind` (Local/Utc/Unspecified).  

---

#### **10. Pokroèilé techniky**  

- **Periodické úlohy**:  
  ```csharp
  using var timer = new PeriodicTimer(TimeSpan.FromMinutes(5));
  while (await timer.WaitForNextTickAsync())
  {
      // Spustí se každých 5 minut
  }
  ```  
- **Mìøení èasu**:  
  ```csharp
  var stopky = System.Diagnostics.Stopwatch.StartNew();
  // ... kód ...
  stopky.Stop();
  Console.WriteLine($"Ubìhlo: {stopky.ElapsedMilliseconds} ms");
  ```
