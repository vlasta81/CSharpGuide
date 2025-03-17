
### Vlastnosti entit v Entity Framework Core

---

#### **1. Co jsou vlastnosti entit?**  

- **Definice**: Vlastnosti (properties) entit jsou členské proměnné C# tříd, které mapují na **sloupce v databázové tabulce**.  
- **Účel**: Definují strukturu dat, datové typy, omezení a vztahy mezi entitami.  

**Příklad entity s vlastnostmi**:  
```csharp
public class Student
{
    public int Id { get; set; }          // Primární klíč
    public string Jmeno { get; set; }    // Sloupec typu nvarchar
    public DateTime DatumNarozeni { get; set; }
    public int TridaId { get; set; }     // Cizí klíč
    public Trida Trida { get; set; }     // Navigační vlastnost
}
```

---

#### **2. Základní konfigurace vlastností**  

- **Data Annotations**: Atributy přímo v entitě.  
  ```csharp
  [Required]                   // Povinné pole
  [MaxLength(50)]              // Maximální délka 50 znaků
  public string Jmeno { get; set; }

  [Column("DatumVytvoreni")]   // Mapování na sloupec s jiným názvem
  public DateTime CreatedAt { get; set; }
  ```

- **Fluent API**: Konfigurace v metodě `OnModelCreating` DbContextu.  
  ```csharp
  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
      modelBuilder.Entity<Student>(entity =>
      {
          entity.Property(s => s.Jmeno)
              .IsRequired()
              .HasMaxLength(50)
              .HasColumnName("JmenoStudenta");
      });
  }
  ```

---

#### **3. Běžné typy konfigurací**  

1. **Primární klíč**:  
   - Konvence: Vlastnost `Id` nebo `[NázevEntita]Id` (např. `StudentId`).  
   - Explicitní nastavení:  
     ```csharp
     [Key] // Data Annotation
     public int StudentId { get; set; }

     // Fluent API:
     modelBuilder.Entity<Student>().HasKey(s => s.StudentId);
     ```

2. **Cizí klíč**:  
   - Definuje vztah mezi entitami.  
   ```csharp
   public int TridaId { get; set; }          // Cizí klíč
   public Trida Trida { get; set; }          // Navigační vlastnost
   ```

3. **Datové typy**:  
   - EF Core automaticky mapuje C# typy na databázové (např. `string` → `nvarchar`, `bool` → `bit`).  
   - Přepsání datového typu:  
     ```csharp
     entity.Property(s => s.DatumNarozeni).HasColumnType("date"); // Datum bez času
     ```

4. **Výchozí hodnoty**:  
   ```csharp
   entity.Property(s => s.DatumVytvoreni)
       .HasDefaultValueSql("GETDATE()"); // SQL Server
   ```

5. **Generované hodnoty**:  
   - Autoinkrementace:  
     ```csharp
     [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
     public int Id { get; set; }
     ```

---

#### **4. Pokročilé konfigurace**  

1. **Konkurenční tokeny** (pro optimistickou konkurenci):  
   ```csharp
   [Timestamp] // Generuje se při každé změně
   public byte[] RowVersion { get; set; }
   ```

2. **Stínové vlastnosti** (Shadow Properties):  
   - Sloupce bez odpovídající vlastnosti v entitě.  
   ```csharp
   modelBuilder.Entity<Student>().Property<DateTime>("DatumUpravy");
   // Přístup přes DbContext:
   var datum = context.Entry(student).Property<DateTime>("DatumUpravy").CurrentValue;
   ```

3. **Value Converters**:  
   - Převod hodnot mezi C# a databází (např. enum → string).  
   ```csharp
   entity.Property(s => s.Status)
       .HasConversion(
           v => v.ToString(),
           v => (Status)Enum.Parse(typeof(Status), v)
       );
   ```

4. **Vypočítané sloupce**:  
   ```csharp
   entity.Property(s => s.CeleJmeno)
       .HasComputedColumnSql("Jmeno + ' ' + Prijmeni"); // SQL Server
   ```

---

#### **5. Navigační vlastnosti a vztahy**  

- **1:N (jeden k mnoha)**:  
  ```csharp
  public class Trida
  {
      public List<Student> Studenti { get; set; } // Navigační vlastnost
  }

  // Fluent API:
  modelBuilder.Entity<Trida>()
      .HasMany(t => t.Studenti)
      .WithOne(s => s.Trida);
  ```

- **M:N (mnoho k mnoha)**:  
  ```csharp
  modelBuilder.Entity<Student>()
      .HasMany(s => s.Kurzy)
      .WithMany(k => k.Studenti);
  ```

---

#### **6. Časté chyby a řešení**  

- **Chybějící `[Required]`**: Pole je v databázi `NOT NULL`, ale v kódu není označeno jako povinné → chyba při ukládání.  
- **Špatný datový typ**: Např. `string` bez `MaxLength` → vytvoří se `nvarchar(MAX)`.  
- **Nesprávný cizí klíč**: Např. `TridaID` místo `TridaId` → EF Core nerozpozná vztah.  

---

#### **7. Doporučené postupy**  

- **Konvence před konfigurací**: Využívejte výchozí konvence EF Core (např. `Id` jako primární klíč).  
- **Fluent API pro komplexní konfiguraci**: Lepší kontrola než Data Annotations.  
- **Migrace**: Po změně vlastností vždy spusťte `dotnet ef migrations add`.  

---

**Shrnutí**:  
- Vlastnosti entit definují **mapování mezi C# třídami a databázovými sloupci**.  
- Konfigurace probíhá přes **Data Annotations** nebo **Fluent API**.  
- Navigační vlastnosti a cizí klíče určují **vztahy mezi entitami**.  

**Zdroje**:  
- [EF Core Dokumentace – Vlastnosti entit](https://learn.microsoft.com/en-us/ef/core/modeling/entity-properties)  
- [EF Core Value Conversions](https://learn.microsoft.com/en-us/ef/core/modeling/value-conversions)
