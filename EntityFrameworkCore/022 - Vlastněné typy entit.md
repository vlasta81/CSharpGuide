
### Vlastněné typy entit (Owned Entity Types) v Entity Framework Core

---

#### **1. Co jsou vlastněné typy entit?**  

- **Definice**: Speciální typ entity, který **nemá vlastní identitu** (primární klíč) a je plně závislý na jiné entitě (tzv. "vlastníkovi").  
- **Účel**: Modelovat **hodnotové objekty** (value objects), které nemají samostatný význam mimo kontext vlastníka (např. adresa, peněžní částka).  
- **Charakteristika**:  
  - Nejsou součástí modelu jako samostatné entity (nemají `DbSet<T>`).  
  - Jsou vždy součástí vlastníka a nelze je načíst nebo upravovat nezávisle.  

---

#### **2. Konfigurace vlastněných typů**  

- **Fluent API**:  
  ```csharp  
  modelBuilder.Entity<Vlastnik>().OwnsOne(v => v.VlastnenyTyp);  
  ```  

**Příklad**: Vlastněný typ `Adresa` v entitě `Zakaznik`:  
```csharp  
public class Zakaznik  
{  
    public int Id { get; set; }  
    public string Jmeno { get; set; }  
    public Adresa Adresa { get; set; } // Vlastněný typ  
}  

public class Adresa  
{  
    public string Ulice { get; set; }  
    public string Mesto { get; set; }  
}  

// Konfigurace v DbContextu:  
modelBuilder.Entity<Zakaznik>().OwnsOne(z => z.Adresa);  
```  

---

#### **3. Uložení vlastněných typů**  

- **V jedné tabulce (Table Splitting)**:  
  ```csharp  
  modelBuilder.Entity<Zakaznik>().OwnsOne(z => z.Adresa, a =>  
  {  
      a.Property(adr => adr.Ulice).HasColumnName("Ulice");  
      a.Property(adr => adr.Mesto).HasColumnName("Mesto");  
  });  
  ```  
  *Výsledek*: Sloupce `Ulice` a `Mesto` jsou součástí tabulky `Zakaznici`.  

- **V samostatné tabulce**:  
  ```csharp  
  modelBuilder.Entity<Zakaznik>().OwnsOne(z => z.Adresa, a =>  
  {  
      a.ToTable("Adresy");  
  });  
  ```  
  *Výsledek*: Data adresy jsou uložena v tabulce `Adresy` s cizím klíčem na `Zakaznik`.  

---

#### **4. Vlastněné typy s kolekcemi (OwnsMany)**  

- **Definice**: Vlastněný typ může obsahovat kolekce (např. seznam telefonních čísel).  
- **Příklad**:  
  ```csharp  
  modelBuilder.Entity<Zakaznik>().OwnsMany(z => z.Telefony, t =>  
  {  
      t.WithOwner().HasForeignKey("ZakaznikId");  
      t.Property<int>("Id");  
      t.HasKey("Id");  
  });  
  ```  

---

#### **5. Kdy použít vlastněné typy?**  

| **Scénář**                | **Příklad**                          |  
|---------------------------|--------------------------------------|  
| **Hodnotové objekty**      | Adresa, peněžní částka (měna + hodnota). |  
| **Komplexní struktury**    | Konfigurační nastavení entity.        |  
| **Normalizace dat**        | Rozdělení entity do logických částí.  |  

---

#### **6. Omezení vlastněných typů**  

- **Žádný vlastní klíč**: Nelze definovat `[Key]` nebo `HasKey()`.  
- **Omezené dotazování**: Nelze načíst přes `Include()` jako samostatné entity.  
- **Migrace**: Změny ve struktuře vlastněného typu vyžadují aktualizaci migrací.  

---

#### **7. Rozdíl oproti standardním vztahům**  

| **Vlastněný typ**               | **Standardní vztah (1:1)**           |  
|---------------------------------|--------------------------------------|  
| Nemá vlastní identitu.          | Má vlastní primární klíč.            |  
| Životní cyklus závisí na vlastníkovi. | Může existovat nezávisle.           |  
| Nelze načíst přes `DbSet<T>`.   | Lze načíst a upravovat samostatně.   |  

---

#### **8. Doporučené postupy**  

1. **Používejte pro hodnotové objekty**: Kde identita není důležitá (např. `Adresa`, `Penize`).  
2. **Sdílejte konfiguraci**: Pokud je vlastněný typ používán více entitami, vytvořte pro něj extension metody.  
3. **Testujte migrace**: Změny ve vlastněných typech mohou ovlivnit schéma databáze.  

---

**Shrnutí**:  
- Vlastněné typy umožňují modelovat **logické části entity** bez nutnosti samostatné identity.  
- Konfigurují se pomocí `OwnsOne()` nebo `OwnsMany()` v Fluent API.  
- Jsou ideální pro **hodnotové objekty** a snižují redundanci kódu.  

**Zdroje**:  
- [EF Core Dokumentace – Vlastněné typy](https://learn.microsoft.com/en-us/ef/core/modeling/owned-entities)  
- [Value Objects vs. Entities](https://martinfowler.com/bliki/ValueObject.html)
