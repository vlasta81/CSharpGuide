
### Entity (Entita) v Entity Framework Core

---

#### **1. Co je Entita?**  

- **Definice**: Entita je C# třída reprezentující databázovou tabulku. Každá instance entity odpovídá řádku v tabulce.  
- **Vlastnosti**:  
  - **POCO (Plain Old CLR Object)**: Entita je obyčejná třída bez závislosti na EF Core.  
  - **Mapování**: Sloupce v tabulce odpovídají vlastnostem entity.  
  - **Navigační vlastnosti**: Definují vztahy mezi entitami (např. `public Trida Trida { get; set; }`).  

**Příklad entity**:  
```csharp
public class Student
{
    public int Id { get; set; }         // Primární klíč (konvencí)
    public string Jmeno { get; set; }    // Sloupec v tabulce
    public int TridaId { get; set; }     // Cizí klíč pro vztah 1:N
    public Trida Trida { get; set; }     // Navigační vlastnost
}
```

---

#### **2. Klíčové vlastnosti entit**  

1. **Primární klíč**:  
   - Identifikuje jednoznačně řádek v tabulce.  
   - Konvence: Vlastnost pojmenovaná `Id` nebo `[NázevEntita]Id` (např. `StudentId`).  
   - Explicitní konfigurace:  
     ```csharp
     [Key] // Data Annotation
     public int StudentId { get; set; }

     // Fluent API:
     modelBuilder.Entity<Student>().HasKey(s => s.StudentId);
     ```

2. **Cizí klíč**:  
   - Definuje vztah mezi entitami (např. `TridaId` v `Student`).  
   - Konvence: Název ve formátu `[NavigačníVlastnost]Id`.  

3. **Datové typy**:  
   - Mapování C# typů na databázové typy (např. `string` → `nvarchar`, `DateTime` → `datetime`).  

---

#### **3. Konfigurace entit**  

- **Data Annotations**: Atributy přímo v entitě.  
  ```csharp
  [Table("Studenti")]               // Mapování na tabulku "Studenti"
  public class Student
  {
      [Key]                        // Primární klíč
      [MaxLength(100)]             // Maximální délka
      public string Email { get; set; }
  }
  ```

- **Fluent API**: Konfigurace v `OnModelCreating` metodě DbContextu.  
  ```csharp
  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
      modelBuilder.Entity<Student>(entity =>
      {
          entity.ToTable("Studenti");
          entity.Property(s => s.Email).HasMaxLength(100);
      });
  }
  ```

---

#### **4. Navigační vlastnosti a vztahy**  

- **1:N (jeden k mnoha)**:  
  ```csharp
  public class Trida
  {
      public int Id { get; set; }
      public List<Student> Studenti { get; set; } // Navigační vlastnost
  }

  public class Student
  {
      public int TridaId { get; set; }     // Cizí klíč
      public Trida Trida { get; set; }     // Navigační vlastnost
  }

  // Fluent API:
  modelBuilder.Entity<Trida>()
      .HasMany(t => t.Studenti)
      .WithOne(s => s.Trida);
  ```

- **M:N (mnoho k mnoha)**:  
  - **EF Core 5+**: Podpora bez join entity.  
  ```csharp
  modelBuilder.Entity<Student>()
      .HasMany(s => s.Kurzy)
      .WithMany(k => k.Studenti);
  ```

---

#### **5. Dědičnost entit**  

- **TPH (Table per Hierarchy)**: Všechny podtřídy v jedné tabulce s diskriminátorem.  
  ```csharp
  public class Osoba { public int Id { get; set; } }
  public class Student : Osoba { public string Obor { get; set; } }

  // Konfigurace:
  modelBuilder.Entity<Osoba>()
      .HasDiscriminator<string>("Typ")
      .HasValue<Student>("student");
  ```

---

#### **6. Pokročilé koncepty**  

- **Stínové vlastnosti (Shadow Properties)**: Sloupce bez odpovídající vlastnosti v entitě.  
  ```csharp
  modelBuilder.Entity<Student>().Property<DateTime>("DatumVytvoreni");
  // Přístup přes DbContext:
  var datum = context.Entry(student).Property<DateTime>("DatumVytvoreni").CurrentValue;
  ```

- **Owned Entities (Vlastněné entity)**: Entita je součástí jiné entity (např. adresa).  
  ```csharp
  public class Student
  {
      public Adresa Adresa { get; set; } // Vlastněná entita
  }

  modelBuilder.Entity<Student>().OwnsOne(s => s.Adresa);
  ```

---

#### **7. Časté chyby**  

- **Chybějící navigační vlastnost**: Vede k nefunkčnímu vztahu.  
- **Nesprávný název cizího klíče**: Např. `TridaId` vs. `TridaID`.  
- **Nedefinovaný primární klíč**: EF Core vyžaduje explicitní nebo konvenční PK.  

---

#### **8. Doporučené postupy** 

- **Oddělte logiku**: Entity by měly být jednoduché (bez business logiky).  
- **Používejte migrace**: Po změně entity vždy vytvořte migraci (`dotnet ef migrations add`).  
- **Validace dat**: Používejte atributy jako `[Required]` nebo `[MaxLength]`.  

---

**Shrnutí**:  
- Entita je základní stavební jednotka modelu v EF Core.  
- Konfiguruje se přes **Data Annotations** nebo **Fluent API**.  
- Navigační vlastnosti definují vztahy mezi entitami.  

**Zdroje**:  
- [EF Core Dokumentace – Entity](https://learn.microsoft.com/en-us/ef/core/modeling/entity-types)  
- [EF Core Data Annotations](https://learn.microsoft.com/en-us/ef/core/modeling/entity-properties?tabs=data-annotations)
