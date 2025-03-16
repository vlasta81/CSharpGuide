
### Pr�ce se soubory a adres��i v jazyce C#  

Pr�ce se soubory a adres��i je kl��ov� pro ukl�d�n� a spr�vu dat. C# poskytuje t��dy v namespace `System.IO` pro operace s file systemem. Zde je p�ehled kl��ov�ch koncept�:

---

#### **1. Z�kladn� t��dy**  

- **`File`**: Statick� t��da pro pr�ci se soubory (�ten�, z�pis, kop�rov�n�).  
- **`Directory`**: Statick� t��da pro pr�ci s adres��i (vytv��en�, maz�n�).  
- **`FileInfo`** a **`DirectoryInfo`**: Instance t��d pro pr�ci s konkr�tn�mi soubory/adres��i (vhodn� pro opakovan� operace).  
- **`Path`**: Pomocn� t��da pro manipulaci s cestami (nap�. `Combine`, `GetExtension`).

---

#### **2. Z�kladn� operace se soubory**  

- **Vytvo�en� souboru**:  
  ```csharp
  File.WriteAllText("test.txt", "Obsah souboru"); // Vytvo�� a zap�e text
  File.Create("novy.txt").Close(); // Pr�zdn� soubor
  ```  
- **�ten� souboru**:  
  ```csharp
  string obsah = File.ReadAllText("test.txt");
  string[] radky = File.ReadAllLines("data.csv");
  ```  
- **P�id�n� obsahu**:  
  ```csharp
  File.AppendAllText("log.txt", $"{DateTime.Now}: Nov� ud�lost\n");
  ```  
- **Smaz�n� souboru**:  
  ```csharp
  File.Delete("stary.txt");
  ```  
- **Kontrola existence**:  
  ```csharp
  if (File.Exists("data.bin")) { /* ... */ }
  ```  

---

#### **3. Z�kladn� operace s adres��i**  

- **Vytvo�en� adres��e**:  
  ```csharp
  Directory.CreateDirectory(@"C:\MujAdresar");
  ```  
- **Smaz�n� adres��e**:  
  ```csharp
  Directory.Delete(@"C:\StaryAdresar", recursive: true); // Sma�e i obsah
  ```  
- **Kontrola existence**:  
  ```csharp
  if (Directory.Exists(@"C:\Temp")) { /* ... */ }
  ```  
- **V�pis soubor� a adres���**:  
  ```csharp
  string[] soubory = Directory.GetFiles(@"C:\Data", "*.txt");
  string[] adresare = Directory.GetDirectories(@"C:\");
  ```

---

#### **4. Pokro�il� operace**  

- **Kop�rov�n� a p�esun souboru**:  
  ```csharp
  File.Copy("zdroj.txt", "cil.txt", overwrite: true);
  File.Move("stary.txt", "novy.txt"); // P�esun nebo p�ejmenov�n�
  ```  
- **Pr�ce s cestami**:  
  ```csharp
  string cesta = Path.Combine(@"C:\Data", "soubor.txt"); // Sestaven� cesty
  string pripona = Path.GetExtension("obrazek.jpg"); // "jpg"
  ```  
- **Pr�va a atributy (pomoc� `FileInfo`/`DirectoryInfo`)**:  
  ```csharp
  FileInfo fi = new FileInfo("data.txt");
  Console.WriteLine(fi.CreationTime); // Datum vytvo�en�
  Console.WriteLine(fi.Length); // Velikost v bajtech
  ```  

---

#### **5. Pr�ce se streamy (pro velk� data)**  

- **Z�pis pomoc� `StreamWriter`**:  
  ```csharp
  using (StreamWriter writer = new StreamWriter("log.txt", append: true))
  {
      await writer.WriteLineAsync("Dal�� z�znam");
  }
  ```  
- **�ten� pomoc� `StreamReader`**:  
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

#### **6. O�et�en� v�jimek**  

- B�n� chyby: `FileNotFoundException`, `UnauthorizedAccessException`, `IOException`.  
- **Uk�zka**:  
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

#### **7. Doporu�en� postupy**  

- Pro pr�ci s cestami v�dy pou��vejte `Path.Combine` (ne ru�n� spojov�n�).  
- Pro �i�t�n� zdroj� (soubory, streamy) vyu��vejte `using` bloky.  
- Pro velk� soubory preferujte **streamy** (ne `ReadAllText`).  
- Kontrolujte u�ivatelsk� opr�vn�n� a existenci soubor� p�ed operacemi.  

---

#### **8. �ast� chyby** 

- **Zam�en� soubor**: Pokus o z�pis do souboru otev�en�ho jin�m procesem.  
- **Relativn� cesty**: Nezapome�te na pracovn� adres�� aplikace (`Environment.CurrentDirectory`).  
- **Case sensitivity**: Na Linuxu/macOS jsou cesty case-sensitive.  

---

#### **9. Pokro�il� techniky**  

- **Monitorov�n� zm�n soubor�**:  
  ```csharp
  FileSystemWatcher watcher = new FileSystemWatcher(@"C:\Data");
  watcher.Created += (sender, e) => Console.WriteLine($"Vytvo�en: {e.Name}");
  watcher.EnableRaisingEvents = true;
  ```  
- **Pr�ce s do�asn�mi soubory**:  
  ```csharp
  string tempSoubor = Path.GetTempFileName();
  File.WriteAllText(tempSoubor, "Do�asn� data");
  ```  
