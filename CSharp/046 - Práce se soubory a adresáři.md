
### Práce se soubory a adresáøi v jazyce C#  

Práce se soubory a adresáøi je klíèová pro ukládání a správu dat. C# poskytuje tøídy v namespace `System.IO` pro operace s file systemem. Zde je pøehled klíèových konceptù:

---

#### **1. Základní tøídy**  

- **`File`**: Statická tøída pro práci se soubory (ètení, zápis, kopírování).  
- **`Directory`**: Statická tøída pro práci s adresáøi (vytváøení, mazání).  
- **`FileInfo`** a **`DirectoryInfo`**: Instance tøíd pro práci s konkrétními soubory/adresáøi (vhodné pro opakované operace).  
- **`Path`**: Pomocná tøída pro manipulaci s cestami (napø. `Combine`, `GetExtension`).

---

#### **2. Základní operace se soubory**  

- **Vytvoøení souboru**:  
  ```csharp
  File.WriteAllText("test.txt", "Obsah souboru"); // Vytvoøí a zapíše text
  File.Create("novy.txt").Close(); // Prázdný soubor
  ```  
- **Ètení souboru**:  
  ```csharp
  string obsah = File.ReadAllText("test.txt");
  string[] radky = File.ReadAllLines("data.csv");
  ```  
- **Pøidání obsahu**:  
  ```csharp
  File.AppendAllText("log.txt", $"{DateTime.Now}: Nová událost\n");
  ```  
- **Smazání souboru**:  
  ```csharp
  File.Delete("stary.txt");
  ```  
- **Kontrola existence**:  
  ```csharp
  if (File.Exists("data.bin")) { /* ... */ }
  ```  

---

#### **3. Základní operace s adresáøi**  

- **Vytvoøení adresáøe**:  
  ```csharp
  Directory.CreateDirectory(@"C:\MujAdresar");
  ```  
- **Smazání adresáøe**:  
  ```csharp
  Directory.Delete(@"C:\StaryAdresar", recursive: true); // Smaže i obsah
  ```  
- **Kontrola existence**:  
  ```csharp
  if (Directory.Exists(@"C:\Temp")) { /* ... */ }
  ```  
- **Výpis souborù a adresáøù**:  
  ```csharp
  string[] soubory = Directory.GetFiles(@"C:\Data", "*.txt");
  string[] adresare = Directory.GetDirectories(@"C:\");
  ```

---

#### **4. Pokroèilé operace**  

- **Kopírování a pøesun souboru**:  
  ```csharp
  File.Copy("zdroj.txt", "cil.txt", overwrite: true);
  File.Move("stary.txt", "novy.txt"); // Pøesun nebo pøejmenování
  ```  
- **Práce s cestami**:  
  ```csharp
  string cesta = Path.Combine(@"C:\Data", "soubor.txt"); // Sestavení cesty
  string pripona = Path.GetExtension("obrazek.jpg"); // "jpg"
  ```  
- **Práva a atributy (pomocí `FileInfo`/`DirectoryInfo`)**:  
  ```csharp
  FileInfo fi = new FileInfo("data.txt");
  Console.WriteLine(fi.CreationTime); // Datum vytvoøení
  Console.WriteLine(fi.Length); // Velikost v bajtech
  ```  

---

#### **5. Práce se streamy (pro velká data)**  

- **Zápis pomocí `StreamWriter`**:  
  ```csharp
  using (StreamWriter writer = new StreamWriter("log.txt", append: true))
  {
      await writer.WriteLineAsync("Další záznam");
  }
  ```  
- **Ètení pomocí `StreamReader`**:  
  ```csharp
  using (StreamReader reader = new StreamReader("data.txt"))
  {
      string radek;
      while ((radek = await reader.ReadLineAsync()) != null)
      {
          Console.WriteLine(radek);
      }
  }
  ```

---

#### **6. Ošetøení výjimek**  

- Bìžné chyby: `FileNotFoundException`, `UnauthorizedAccessException`, `IOException`.  
- **Ukázka**:  
  ```csharp
  try
  {
      File.ReadAllText("neexistujici.txt");
  }
  catch (FileNotFoundException ex)
  {
      Console.WriteLine($"Soubor nenalezen: {ex.Message}");
  }
  catch (IOException ex)
  {
      Console.WriteLine($"Chyba I/O: {ex.Message}");
  }
  ```

---

#### **7. Doporuèené postupy**  

- Pro práci s cestami vždy používejte `Path.Combine` (ne ruèní spojování).  
- Pro èištìní zdrojù (soubory, streamy) využívejte `using` bloky.  
- Pro velké soubory preferujte **streamy** (ne `ReadAllText`).  
- Kontrolujte uživatelská oprávnìní a existenci souborù pøed operacemi.  

---

#### **8. Èasté chyby** 

- **Zamèený soubor**: Pokus o zápis do souboru otevøeného jiným procesem.  
- **Relativní cesty**: Nezapomeòte na pracovní adresáø aplikace (`Environment.CurrentDirectory`).  
- **Case sensitivity**: Na Linuxu/macOS jsou cesty case-sensitive.  

---

#### **9. Pokroèilé techniky**  

- **Monitorování zmìn souborù**:  
  ```csharp
  FileSystemWatcher watcher = new FileSystemWatcher(@"C:\Data");
  watcher.Created += (sender, e) => Console.WriteLine($"Vytvoøen: {e.Name}");
  watcher.EnableRaisingEvents = true;
  ```  
- **Práce s doèasnými soubory**:  
  ```csharp
  string tempSoubor = Path.GetTempFileName();
  File.WriteAllText(tempSoubor, "Doèasná data");
  ```  
