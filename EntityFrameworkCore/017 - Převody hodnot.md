
### Převody hodnot (Value Conversions) v Entity Framework Core

---

#### **1. Co jsou převody hodnot?**  

- **Definice**: Mechanismus pro transformaci hodnot vlastností entit při **zápisu do databáze** nebo **čtení z databáze**.  
- **Účel**: Umožňuje mapovat komplexní typy nebo formáty, které nejsou přímo podporovány databází (např. enumy, JSON, šifrovaná data).  

---

#### **2. Kdy použít převody hodnot?**  

- **Příklady scénářů**:  
  - Ukládání **enumů** jako řetězců nebo čísel.  
  - Serializace objektů do **JSON** pro uložení do sloupce typu `nvarchar`.  
  - Konverze **data a času** do UTC před uložením.  
  - Šifrování citlivých dat (např. hesla).  

---

#### **3. Základní konfigurace**  

- **Data Annotations**:  
  ```csharp  
  public class Product  
  {  
      [Column(TypeName = "varchar(10)")]  
      public Status Status { get; set; } // Status je enum  
  }  
  ```  

- **Fluent API**:  
  ```csharp  
  modelBuilder.Entity<Product>()  
      .Property(p => p.Status)  
      .HasConversion(  
          v => v.ToString(),         // Zápis do DB  
          v => (Status)Enum.Parse(typeof(Status), v) // Čtení z DB  
      );  
  ```  

---

#### **4. Vestavěné převodníky (Built-in Converters)**  

EF Core obsahuje předdefinované převodníky pro běžné typy:  
- **Enum ↔ String**:  
  ```csharp  
  .HasConversion<string>();  
  ```  
- **Bool ↔ Int** (např. `0`/`1`):  
  ```csharp  
  .HasConversion<int>();  
  ```  
- **DateTime ↔ UTC DateTime**:  
  ```csharp  
  .HasConversion(  
      v => v.ToUniversalTime(),  
      v => DateTime.SpecifyKind(v, DateTimeKind.Utc)  
  );  
  ```  

---

#### **5. Vlastní převodníky (Custom Converters)**  

**Krok 1**: Vytvořte třídu implementující `ValueConverter<TModel, TProvider>`.  
**Krok 2**: Zaregistrujte převodník v modelu.  

**Příklad (šifrování hesla)**:  
```csharp  
public class Product  
{  
    public string PasswordHash { get; set; }  
}  

// Vlastní převodník  
public class EncryptionConverter : ValueConverter<string, string>  
{  
    public EncryptionConverter()  
        : base(  
            v => Encrypt(v),  
            v => Decrypt(v)  
        )  
    { }  

    private static string Encrypt(string value) => /* ... */;  
    private static string Decrypt(string value) => /* ... */;  
}  

// Registrace  
modelBuilder.Entity<Product>()  
    .Property(p => p.PasswordHash)  
    .HasConversion(new EncryptionConverter());  
```  

---

#### **6. Převody kolekcí**  

- **Ukládání kolekcí jako JSON**:  
  ```csharp  
  modelBuilder.Entity<Order>()  
      .Property(o => o.Items) // List<string>  
      .HasConversion(  
          v => JsonSerializer.Serialize(v, null),  
          v => JsonSerializer.Deserialize<List<string>>(v, null)  
      );  
  ```  

---

#### **7. Omezení a doporučení**  

- **Výkon**: Komplexní převody mohou zpomalit dotazy.  
- **Ladění**: Převodníky mohou komplikovat debugování (např. šifrovaná data).  
- **LINQ překlad**: Některé převody nejsou podporovány v LINQ dotazech (vyžadují klient-side evaluaci).  

---

#### **8. Časté problémy**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|---------------------------------------------|  
| Chyba při deserializaci JSON         | Ošetřete `null` hodnoty a neplatný formát.  |  
| Nezachycené výjimky v převodníku     | Přidejte try-catch bloky do vlastní logiky. |  
| Neočekávané chování v dotazech       | Ověřte, zda je převodník kompatibilní s LINQ. |  

---

#### **9. Best Practices**  

1. **Testujte převodníky**: Ověřte správnou funkci při čtení i zápisu.  
2. **Používejte vestavěné převodníky**: Pro jednoduché scénáře (enumy, bool).  
3. **Šifrování**: Nikdy neukládejte data v plaintextu – použijte certifikáty nebo klíče.  

---

**Shrnutí**:  
- Převody hodnot umožňují **flexibilní mapování** mezi C# objekty a databází.  
- Lze použít pro **enumy, JSON, šifrování** a další scénáře.  
- Konfigurují se přes **Data Annotations** nebo **Fluent API**.  

**Zdroje**:  
- [EF Core Dokumentace – Převody hodnot](https://learn.microsoft.com/en-us/ef/core/modeling/value-conversions)  
- [EF Core Built-in Converters](https://learn.microsoft.com/en-us/ef/core/modeling/value-conversions#built-in-converters)
