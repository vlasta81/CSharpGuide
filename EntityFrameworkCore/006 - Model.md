
### Model v Entity Framework Core

---

#### **1. Co je Model v EF Core?**  

- **Definice**: Model reprezentuje strukturu databáze pomocí **entit** (C# tříd) a jejich vztahů.  
- **Účel**: Mapuje objekty v kódu na databázové tabulky, sloupce a relace.  
- **Součásti modelu**:  
  - **Entity**: Třídy mapované na tabulky.  
  - **Vztahy**: Definice 1:1, 1:N, M:N mezi entitami.  
  - **Konfigurace**: Nastavení primárních klíčů, omezení, datových typů.  

---

#### **2. Struktura entity**  

- **POCO třída**: Entita je obyčejná C# třída (Plain Old CLR Object) bez závislosti na EF Core.  
- **Vlastnosti**: Odpovídají sloupcům v tabulce.  
- **Příklad entity**:  
  ```csharp
  public class Student
  {
      public int Id { get; set; }          // Primární klíč (konvence)
      public string Jmeno { get; set; }     // Povinný sloupec (lze konfigurovat)
      public DateTime DatumNarozeni { get; set; }
      public int TridaId { get; set; }      // Cizí klíč pro vztah 1:N
      public Trida Trida { get; set; }      // Navigační vlastnost
  }
  ```

---

#### **3. Konfigurace modelu**  

- **Data Annotations**: Atributy přímo v entitě.  
  ```csharp
  [Table("Studenti")]               // Mapování na tabulku "Studenti"
  public class Student
  {
      [Key]                        // Primární klíč
      [DatabaseGenerated(DatabaseGeneratedOption.Identity)] // Autoinkrement
      public int Id { get; set; }

      [Required]                   // Povinné pole
      [MaxLength(50)]              // Maximální délka
      public string Jmeno { get; set; }
  }
  ```

- **Fluent API**: Konfigurace v metodě `OnModelCreating` DbContextu.  
  ```csharp
  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
      modelBuilder.Entity<Student>(entity =>
      {
          entity.ToTable("Studenti");
          entity.HasKey(s => s.Id);
          entity.Property(s => s.Jmeno)
              .IsRequired()
              .HasMaxLength(50);
          entity.HasOne(s => s.Trida)       // 1:N vztah
              .WithMany(t => t.Studenti)
              .HasForeignKey(s => s.TridaId);
      });
  }
  ```

---

#### **4. Typy vztahů**  

1. **1:1 (jeden k jednomu)**  
   ```csharp
   modelBuilder.Entity<Student>()
       .HasOne(s => s.StudijniProfil)
       .WithOne(sp => sp.Student)
       .HasForeignKey<StudijniProfil>(sp => sp.StudentId);
   ```

2. **1:N (jeden k mnoha)**  
   ```csharp
   modelBuilder.Entity<Trida>()
       .HasMany(t => t.Studenti)
       .WithOne(s => s.Trida);
   ```

3. **M:N (mnoho k mnoha)**  
   - **EF Core 5+**: Podpora bez nutnosti join entity.  
   ```csharp
   modelBuilder.Entity<Student>()
       .HasMany(s => s.Kurzy)
       .WithMany(k => k.Studenti);
   ```

---

#### **5. Dědičnost v modelu**  

- **TPH (Table per Hierarchy)**: Všechny podtřídy v jedné tabulce s diskriminátorem.  
  ```csharp
  modelBuilder.Entity<Osoba>()
      .HasDiscriminator<string>("Typ")
      .HasValue<Student>("student")
      .HasValue<Ucitel>("ucitel");
  ```

- **TPT (Table per Type)**: Každá podtřída má vlastní tabulku.  
  ```csharp
  modelBuilder.Entity<Student>().ToTable("Studenti");
  modelBuilder.Entity<Ucitel>().ToTable("Ucitele");
  ```

---

#### **6. Pokročilé koncepty**  

- **Stínové vlastnosti (Shadow Properties)**: Sloupce bez odpovídající vlastnosti v entitě.  
  ```csharp
  modelBuilder.Entity<Student>().Property<DateTime>("DatumVytvoreni");
  ```

- **Owned Entities (Vlastněné entity)**: Entita je součástí jiné entity (např. adresa v profilu studenta).  
  ```csharp
  modelBuilder.Entity<Student>().OwnsOne(s => s.Adresa);
  ```

- **Value Converters**: Převod hodnot mezi C# a databází.  
  ```csharp
  modelBuilder.Entity<Student>()
      .Property(s => s.Status)
      .HasConversion(
          v => v.ToString(),
          v => (Status)Enum.Parse(typeof(Status), v)
      );
  ```

---

#### **7. Best Practices**  

- **Oddělte konfiguraci**: Používejte třídy implementující `IEntityTypeConfiguration<T>`.  
  ```csharp
  public class StudentConfig : IEntityTypeConfiguration<Student>
  {
      public void Configure(EntityTypeBuilder<Student> builder)
      {
          builder.ToTable("Studenti");
      }
  }
  ```

- **Migrace**: Po změně modelu vždy vytvořte migraci (`dotnet ef migrations add`).  
- **Validace**: Používejte Data Annotations pro základní omezení (např. `[Required]`).  

---

#### **8. Časté chyby**  

- **Chybějící navigační vlastnosti**: Způsobuje nefunkční vztahy.  
- **Nekonzistentní názvy cizích klíčů**: Např. `TridaId` vs. `TridaID`.  
- **Zapomenutá migrace**: Změny modelu se neprojeví v databázi.  

---

**Shrnutí**:  
- Model v EF Core definuje strukturu databáze pomocí entit a jejich vztahů.  
- Konfigurace se provádí přes **Data Annotations** nebo **Fluent API**.  
- DbContext slouží jako most mezi kódem a databází.  

**Zdroje**:  
- [EF Core Dokumentace – Modelování](https://learn.microsoft.com/en-us/ef/core/modeling/)  
- [EF Core Fluent API](https://learn.microsoft.com/en-us/ef/core/modeling/#fluent-api)
