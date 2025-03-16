
### Práce se soubory a adresáři v jazyce C#  

Práce se soubory a adresáři je klíčová pro ukládání a správu dat. C# poskytuje třídy v namespace `System.IO` pro operace s file systemem. Zde je přehled klíčových konceptů:

---

#### **1. Základní třídy**  

- **`File`**: Statická třída pro práci se soubory (čtení, zápis, kopírování).  
- **`Directory`**: Statická třída pro práci s adresáři (vytváření, mazání).  
- **`FileInfo`** a **`DirectoryInfo`**: Instance tříd pro práci s konkrétními soubory/adresáři (vhodné pro opakované operace).  
- **`Path`**: Pomocná třída pro manipulaci s cestami (např. `Combine`, `GetExtension`).

---

#### **2. Základní operace se soubory**  

- **Vytvoření souboru**:  
  ```csharp
  File.WriteAllText("test.txt", "Obsah souboru"); // Vytvoří a zapíše text
  File.Create("novy.txt").Close(); // Prázdný soubor
  ```  
- **Čtení souboru**:  
  ```csharp
  string obsah = File.ReadAllText("test.txt");
  string[] radky = File.ReadAllLines("data.csv");
  ```  
- **Přidání obsahu**:  
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

#### **3. Základní operace s adresáři**  

- **Vytvoření adresáře**:  
  ```csharp
  Directory.CreateDirectory(@"C:\MujAdresar");
  ```  
- **Smazání adresáře**:  
  ```csharp
  Directory.Delete(@"C:\StaryAdresar", recursive: true); // Smaže i obsah
  ```  
- **Kontrola existence**:  
  ```csharp
  if (Directory.Exists(@"C:\Temp")) { /* ... */ }
  ```  
- **Výpis souborů a adresářů**:  
  ```csharp
  string[] soubory = Directory.GetFiles(@"C:\Data", "*.txt");
  string[] adresare = Directory.GetDirectories(@"C:\");
  ```

---

#### **4. Pokročilé operace**  

- **Kopírování a přesun souboru**:  
  ```csharp
  File.Copy("zdroj.txt", "cil.txt", overwrite: true);
  File.Move("stary.txt", "novy.txt"); // Přesun nebo přejmenování
  ```  
- **Práce s cestami**:  
  ```csharp
  string cesta = Path.Combine(@"C:\Data", "soubor.txt"); // Sestavení cesty
  string pripona = Path.GetExtension("obrazek.jpg"); // "jpg"
  ```  
- **Práva a atributy (pomocí `FileInfo`/`DirectoryInfo`)**:  
  ```csharp
  FileInfo fi = new FileInfo("data.txt");
  Console.WriteLine(fi.CreationTime); // Datum vytvoření
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
- **Čtení pomocí `StreamReader`**:  
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

#### **6. Ošetření výjimek**  

- Běžné chyby: `FileNotFoundException`, `UnauthorizedAccessException`, `IOException`.  
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

#### **7. Doporučené postupy**  

- Pro práci s cestami vždy používejte `Path.Combine` (ne ruční spojování).  
- Pro čištění zdrojů (soubory, streamy) využívejte `using` bloky.  
- Pro velké soubory preferujte **streamy** (ne `ReadAllText`).  
- Kontrolujte uživatelská oprávnění a existenci souborů před operacemi.  

---

#### **8. Časté chyby** 

- **Zamčený soubor**: Pokus o zápis do souboru otevřeného jiným procesem.  
- **Relativní cesty**: Nezapomeňte na pracovní adresář aplikace (`Environment.CurrentDirectory`).  
- **Case sensitivity**: Na Linuxu/macOS jsou cesty case-sensitive.  

---

#### **9. Pokročilé techniky**  

- **Monitorování změn souborů**:  
  ```csharp
  FileSystemWatcher watcher = new FileSystemWatcher(@"C:\Data");
  watcher.Created += (sender, e) => Console.WriteLine($"Vytvořen: {e.Name}");
  watcher.EnableRaisingEvents = true;
  ```  
- **Práce s dočasnými soubory**:  
  ```csharp
  string tempSoubor = Path.GetTempFileName();
  File.WriteAllText(tempSoubor, "Dočasná data");
  ```  
