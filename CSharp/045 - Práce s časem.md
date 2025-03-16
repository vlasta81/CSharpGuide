
### Pr�ce s �asem v jazyce C#  

Pr�ce s datem a �asem je kl��ov� pro mnoho aplikac�. C# nab�z� n�kolik t��d pro manipulaci s �asov�mi �daji, v�etn� `DateTime`, `DateOnly`, `TimeOnly`, `TimeSpan` a `DateTimeOffset`. Zde je p�ehled jejich pou�it�:

---

#### **1. T��da `DateTime`**  

- Reprezentuje datum a �as (nap�. `12. 5. 2024 15:30:00`).  
- **Vlastnosti**:  
  - `Now`: Aktu�ln� syst�mov� �as.  
  - `UtcNow`: Aktu�ln� �as v UTC.  
  - `Today`: Datum s �asem nastaven�m na p�lnoc.  
- **Metody**:  
  ```csharp
  DateTime dnes = DateTime.Now;
  DateTime zitra = dnes.AddDays(1);
  DateTime vcera = dnes.AddDays(-1);
  ```  
- **Form�tov�n�**:  
  ```csharp
  string formatovano = dnes.ToString("dd.MM.yyyy HH:mm:ss"); // "12.05.2024 15:30:00"
  ```

---

#### **2. T��da `DateOnly` a `TimeOnly` (.NET 6+)**  

- **`DateOnly`**: Pouze datum (bez �asu).  
  ```csharp
  DateOnly datum = DateOnly.FromDateTime(DateTime.Now); // 12.05.2024
  ```  
- **`TimeOnly`**: Pouze �as (bez data).  
  ```csharp
  TimeOnly cas = TimeOnly.FromDateTime(DateTime.Now); // 15:30:00
  ```  
- **V�hody**: Eliminuje probl�my s �asov�mi p�smy a zjednodu�uje pr�ci, kdy� nen� �as pot�eba.

---

#### **3. T��da `TimeSpan`**  

- Reprezentuje �asov� interval (nap�. 2 hodiny 15 minut).  
- **Vytvo�en�**:  
  ```csharp
  TimeSpan interval = new TimeSpan(2, 15, 0); // 02:15:00
  TimeSpan interval2 = TimeSpan.FromHours(3.5); // 03:30:00
  ```  
- **Operace**:  
  ```csharp
  DateTime za3Hodiny = DateTime.Now.Add(TimeSpan.FromHours(3));
  TimeSpan rozdil = DateTime.Now - DateTime.Today; // Doba od p�lnoci
  ```  
- **Form�tov�n�**:  
  ```csharp
  string casovyRozdil = interval.ToString(@"hh\:mm"); // "02:15"
  ```

---

#### **4. T��da `DateTimeOffset`**  

- Reprezentuje datum a �as s **offsetem �asov�ho p�sma** (nap�. `2024-05-12T15:30:00+02:00`).  
- **V�hody**: Umo��uje p�esnou pr�ci s �asov�mi p�smy.  
- **P��klad**:  
  ```csharp
  DateTimeOffset casSPasmem = DateTimeOffset.Now;
  DateTimeOffset utcCas = casSPasmem.ToUniversalTime();
  ```

---

#### **5. Parsov�n� a konverze**  

- **Parsov�n� z �et�zce**:  
  ```csharp
  DateTime datum = DateTime.Parse("2024-05-12");
  bool uspech = DateTime.TryParse("12.5.2024", out DateTime result); // Bezpe�n�j�� varianta
  ```  
- **Vlastn� form�ty**:  
  ```csharp
  DateTime datum = DateTime.ParseExact("12/05/24", "dd/MM/yy", CultureInfo.InvariantCulture);
  ```

---

#### **6. �asov� p�sma a `TimeZoneInfo`**  

- Konverze mezi p�smy:  
  ```csharp
  TimeZoneInfo prazskePasmo = TimeZoneInfo.FindSystemTimeZoneById("Central Europe Standard Time");
  DateTimeOffset casVPraze = TimeZoneInfo.ConvertTimeBySystemTimeZoneId(DateTimeOffset.UtcNow, "Central Europe Standard Time");
  ```  
- **UTC vs. lok�ln� �as**:  
  ```csharp
  DateTime utcCas = DateTime.UtcNow;
  DateTime lokalniCas = utcCas.ToLocalTime(); // Podle syst�mov�ho p�sma
  ```

---

#### **7. B�n� operace**  

- **Porovn�v�n�**:  
  ```csharp
  bool jeVetsi = DateTime.Now > DateTime.Today;
  ```  
- **Extrakce ��st�**:  
  ```csharp
  int rok = DateTime.Now.Year;
  int minuta = DateTime.Now.Minute;
  ```  
- **V�po�et v�ku**:  
  ```csharp
  DateTime narozeni = new DateTime(2000, 1, 1);
  int vek = DateTime.Now.Year - narozeni.Year;
  if (DateTime.Now < narozeni.AddYears(vek)) vek--;
  ```

---

#### **8. Doporu�en� postupy**  

- Pro ukl�d�n� do datab�z� pou��vejte **UTC �as**.  
- Pro reprezentaci pouze data/�asu preferujte `DateOnly`/`TimeOnly` (od .NET 6).  
- Pro �asov� p�sma v�dy pou��vejte `DateTimeOffset` m�sto `DateTime`.  
- Validujte vstupy pomoc� `TryParse`, abyste p�ede�li v�jimk�m.

---

#### **9. �ast� chyby**  

- Ignorov�n� �asov�ch p�sem (nap�. u mezin�rodn�ch aplikac�).  
- Z�m�na `DateTime.Now` a `DateTime.UtcNow`.  
- Ukl�d�n� `DateTime` bez specifikace `Kind` (Local/Utc/Unspecified).  

---

#### **10. Pokro�il� techniky**  

- **Periodick� �lohy**:  
  ```csharp
  using var timer = new PeriodicTimer(TimeSpan.FromMinutes(5));
  while (await timer.WaitForNextTickAsync())
  {
      // Spust� se ka�d�ch 5 minut
  }
  ```  
- **M��en� �asu**:  
  ```csharp
  var stopky = System.Diagnostics.Stopwatch.StartNew();
  // ... k�d ...
  stopky.Stop();
  Console.WriteLine($"Ub�hlo: {stopky.ElapsedMilliseconds} ms");
  ```
