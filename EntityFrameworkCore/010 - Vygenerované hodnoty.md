
### Vygenerované hodnoty v Entity Framework Core 

#### **1. Typy generovaných hodnot**  

- **Výchozí hodnoty**: Hodnota při vložení záznamu, pokud není explicitně zadána.  
- **Počítané sloupce**: Hodnota vypočtená databází (např. `A + B`).  
- **Automatické generování klíčů**: Primární klíče s autoinkrementací nebo GUID.  
- **Datum/čas**: Automatické generování časových značek (např. `CreatedAt`).  

---

#### **2. Výchozí hodnoty**  

Nastaví se, pokud aplikace neposkytne hodnotu.  
- **Data Annotations**:  
  ```csharp
  [DatabaseGenerated(DatabaseGeneratedOption.Identity)] // Autoinkrementace
  public int Id { get; set; }

  [DefaultValue(true)] // Pouze pro scaffolding, nefunguje s migracemi
  public bool IsActive { get; set; }
  ```  

- **Fluent API**:  
  ```csharp
  modelBuilder.Entity<Product>(entity => 
  {
      entity.Property(p => p.CreatedAt)
          .HasDefaultValueSql("GETDATE()"); // SQL Server

      entity.Property(p => p.Price)
          .HasDefaultValue(100.0); // Výchozí hodnota v kódu
  });
  ```  

---

#### **3. Počítané sloupce**  

Hodnota se počítá přímo v databázi.  
- **Virtuální (netrvalé)**: Vypočte se při každém dotazu.  
- **Trvalé (stored)**: Uloží se do tabulky.  

**Příklad konfigurace**:  
```csharp
modelBuilder.Entity<Order>(entity => 
{
    entity.Property(o => o.TotalPrice)
        .HasComputedColumnSql("[Price] * [Quantity]"); // SQL výraz
});
```

---

#### **4. Generování primárních klíčů**  

- **Autoinkrementace (Identity)**:  
  ```csharp
  [Key]
  [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
  public int Id { get; set; }
  ```  

- **GUID**:  
  ```csharp
  public Guid Id { get; set; } // EF Core generuje GUID automaticky
  ```  

- **Ruční generování**:  
  ```csharp
  modelBuilder.Entity<Student>()
      .Property(s => s.Id)
      .ValueGeneratedNever(); // Zakáže automatické generování
  ```  

---

#### **5. Generování data a času**  

- **Při vytvoření záznamu**:  
  ```csharp
  entity.Property(p => p.CreatedAt)
      .ValueGeneratedOnAdd() // Pouze pro nové záznamy
      .HasDefaultValueSql("GETDATE()");
  ```  

- **Při aktualizaci záznamu**:  
  ```csharp
  entity.Property(p => p.UpdatedAt)
      .ValueGeneratedOnAddOrUpdate() // Při insertu i update
      .HasDefaultValueSql("GETDATE()");
  ```  

---

#### **6. Explicitní konfigurace generování** 

- **Fluent API**:  
  ```csharp
  modelBuilder.Entity<Invoice>(entity => 
  {
      entity.Property(i => i.InvoiceNumber)
          .HasValueGenerator<InvoiceNumberGenerator>(); // Vlastní generátor
  });
  ```  

- **Vlastní generátor hodnot**:  
  ```csharp
  public class InvoiceNumberGenerator : ValueGenerator<string>
  {
      public override string Next(EntityEntry entry) => "INV-" + Guid.NewGuid().ToString().Substring(0, 8);
      public override bool GeneratesTemporaryValues => false;
  }
  ```  

---

#### **7. Přepsání generování hodnot**  

- **Zakázání automatického generování**:  
  ```csharp
  modelBuilder.Entity<Student>()
      .Property(s => s.Id)
      .ValueGeneratedNever(); // Ruční nastavení ID
  ```  

- **Podmíněné generování**:  
  ```csharp
  entity.Property(p => p.Discount)
      .HasDefaultValue(0.0)
      .HasValueGenerator((p, e) => 
          e.Entity.IsVIP ? new VIPDiscountGenerator() : null);
  ```  

---

#### **8. Časté problémy a řešení**  

| **Problém** | **Příčina** | **Řešení** |  
|-------------|-------------|------------|  
| `InvalidOperationException` při ručním nastavení generované hodnoty | Pokus o nastavení pole s `ValueGeneratedOnAdd` | Použijte `ValueGeneratedNever()` nebo nechte EF Core generovat hodnotu. |  
| Počítaný sloupec není aktualizován | Chybí `IsStored` u virtuálního sloupce | Přidejte `.HasComputedColumnSql(..., stored: true)` pro trvalé uložení. |  
| Konflikt časových značek | Použití `DateTime.Now` místo databázového `GETDATE()` | Vždy používejte databázové funkce pro konzistenci. |  

---

#### **9. Doporučené postupy**  

- **Datum/čas**: Vždy generujte na straně databáze (např. `GETDATE()`), ne v kódu.  
- **Počítané sloupce**: Používejte pro denormalizaci výpočtů, které jsou časté v dotazech.  
- **Testování**: Ověřte chování generovaných hodnot v testovacím prostředí s reálnou databází.  

---

**Shrnutí**:  
- **Výchozí hodnoty** a **počítané sloupce** zjednodušují správu dat.  
- **Primární klíče** se generují automaticky, ale lze je přizpůsobit.  
- **Datum/čas** se nejlépe generuje na straně databáze pro konzistenci.  

**Zdroje**:  
- [EF Core Generated Values](https://learn.microsoft.com/en-us/ef/core/modeling/generated-properties)  
- [EF Core Value Generators](https://learn.microsoft.com/en-us/ef/core/modeling/value-generators)
