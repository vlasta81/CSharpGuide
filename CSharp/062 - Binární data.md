
### **Práce s binárními daty a soubory v jazyce C#**

Práce s binárními daty (soubory) v C# umožňuje čtení, zápis a manipulaci s netextovými daty (např. obrázky, PDF, binární formáty). Základní operace jsou založeny na třídách z namespace `System.IO`.

---

#### **1. Klíčové třídy a metody**

- **`FileStream`**: Slouží k přímé práci s bajty (otevírání, čtení, zápis).
- **`BinaryReader`** a **`BinaryWriter`**: Zjednodušují čtení/zápis primitivních typů (int, double, string) z/do binárního proudu.
- **`MemoryStream`**: Umožňuje práci s daty uloženými v paměti (místo na disku).
- **`File`** a **`FileInfo`**: Statické/metody pro práci se soubory (vytvoření, kopírování, kontrola existence).

---

#### **2. Čtení a zápis binárních dat**

##### **Zápis do binárního souboru**:

```csharp
using (FileStream fs = new FileStream("data.bin", FileMode.Create))
using (BinaryWriter writer = new BinaryWriter(fs))
{
    writer.Write(100);          // Zápis int
    writer.Write(3.14);         // Zápis double
    writer.Write("Hello");      // Zápis string (UTF-8)
}
```

##### **Čtení z binárního souboru**:

```csharp
using (FileStream fs = new FileStream("data.bin", FileMode.Open))
using (BinaryReader reader = new BinaryReader(fs))
{
    int number = reader.ReadInt32();
    double value = reader.ReadDouble();
    string text = reader.ReadString();
}
```

---

#### **3. Práce se soubory**

- **Vytvoření/smazání souboru**:
  ```csharp
  File.WriteAllBytes("file.bin", new byte[] { 0x01, 0x02 }); // Vytvoření
  File.Delete("file.bin"); // Smazání
  ```
- **Kontrola existence**:
  ```csharp
  bool exists = File.Exists("file.bin");
  ```
- **Kopírování/přesun**:
  ```csharp
  File.Copy("source.bin", "target.bin");
  File.Move("old.bin", "new.bin");
  ```

---

#### **4. Serializace binárních dat**

- **Binární serializace objektů**:
  ```csharp
  [Serializable]
  public class Person { public string Name; public int Age; }

  // Serializace
  Person person = new Person { Name = "Alice", Age = 30 };
  using (FileStream fs = new FileStream("person.bin", FileMode.Create))
  {
      BinaryFormatter formatter = new BinaryFormatter();
      formatter.Serialize(fs, person); // Pozor: BinaryFormatter je zastaralý (použijte alternativy jako JSON)
  }
  ```
- **Upozornění**: Třída `BinaryFormatter` je od .NET 5+ považována za **nezabezpečenou** (používejte např. `System.Text.Json` nebo protobuf).

---

#### **5. Příklady použití**

##### **Čtení obrázku do bajtového pole**:

```csharp
byte[] imageBytes = File.ReadAllBytes("image.jpg");
```

##### **Zápis bajtového pole do souboru**:

```csharp
File.WriteAllBytes("backup.bin", imageBytes);
```

##### **Kopírování souboru po částech (s bufferem)**:

```csharp
using (FileStream source = new FileStream("source.bin", FileMode.Open))
using (FileStream target = new FileStream("target.bin", FileMode.Create))
{
    byte[] buffer = new byte[4096];
    int bytesRead;
    while ((bytesRead = source.Read(buffer, 0, buffer.Length)) > 0)
    {
        target.Write(buffer, 0, bytesRead);
    }
}
```

---

#### **6. Práce s `MemoryStream`**

Uložení dat do paměti místo na disk:
```csharp
byte[] data = { 0x01, 0x02, 0x03 };
using (MemoryStream ms = new MemoryStream(data))
using (BinaryReader reader = new BinaryReader(ms))
{
    byte firstByte = reader.ReadByte(); // 0x01
}
```

---

#### **7. Časté chyby a tipy**

- **Nezavření proudu**: Vždy používejte `using` nebo ručně zavolejte `Dispose()`.
- **Přístup k neexistujícímu souboru**: Ošetřete výjimky `FileNotFoundException`.
- **Kódování řetězců**: Při čtení/zápisu stringů specifikujte kódování (např. `Encoding.UTF8`).
  ```csharp
  using (BinaryWriter writer = new BinaryWriter(fs, Encoding.UTF8)) { ... }
  ```
- **Endianness**: Při práci se soubory z jiných platforem zkontrolujte pořadí bajtů.

---

#### **8. Best Practices**

1. **Vždy používejte `using`** pro automatické uvolnění prostředků (`FileStream`, `BinaryReader`).
2. **Ošetřujte výjimky** (např. `IOException`, `UnauthorizedAccessException`).
3. **Pro velké soubory použijte buffer** (např. 4–8 KB).
4. **Nepoužívejte `BinaryFormatter`** – preferujte bezpečnější formáty (JSON, XML, protobuf).

---

### **Shrnutí**

- **Základní třídy**: `FileStream`, `BinaryReader`, `BinaryWriter`, `MemoryStream`.
- **Operace**: Čtení/zápis bajtů, primitivních typů a řetězců.
- **Důležité**: Vždy zavírejte proudy, používejte `using`, ošetřujte výjimky.
- **Použití**: Serializace, práce s obrázky, binární protokoly.

📌 **Příklad – Uložení struktury do binárního souboru**:
```csharp
public struct Point { public int X; public int Y; }

// Zápis
Point p = new Point { X = 10, Y = 20 };
using (var writer = new BinaryWriter(File.Open("point.bin", FileMode.Create)))
{
    writer.Write(p.X);
    writer.Write(p.Y);
}
```
