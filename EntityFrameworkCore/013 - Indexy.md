
### Indexy v Entity Framework Core

---

#### **1. Co je index?**  

- **Definice**: Databázový nástroj pro urychlení vyhledávání a řazení dat.  
- **Účel**: Snižuje čas potřebný k provedení dotazů tím, že umožňuje rychlejší přístup ke konkrétním řádkům.  
- **Princip**: Funguje podobně jako rejstřík v knize – udržuje seřazený seznam hodnot ve sloupci a ukazuje na jejich umístění v tabulce.  

---

#### **2. Typy indexů**  

1. **Jednoduchý index**: Vytvořen na jednom sloupci.  
2. **Složený index (composite)**: Vytvořen na více sloupcích (optimalizace pro dotazy s více podmínkami).  
3. **Unikátní index**: Zajišťuje unikátnost hodnot ve sloupci/sloupcích.  
4. **Clusterovaný vs. neclusterovaný**:  
   - **Clusterovaný**: Data v tabulce jsou fyzicky seřazena podle indexu (pouze jeden na tabulku).  
   - **Neclusterovaný**: Samostatná struktura od dat (lze více na tabulku).  

---

#### **3. Vytvoření indexu**  

- **Data Annotations (od EF Core 5.0)**:  
  ```csharp  
  [Index(nameof(Jmeno))] // Jednoduchý index  
  [Index(nameof(Prijmeni), nameof(Vek))] // Složený index  
  [Index(IsUnique = true)] // Unikátní index  
  public class Student  
  {  
      public int Id { get; set; }  
      public string Jmeno { get; set; }  
      public string Prijmeni { get; set; }  
      public int Vek { get; set; }  
  }  
  ```  

- **Fluent API**:  
  ```csharp  
  modelBuilder.Entity<Student>()  
      .HasIndex(s => s.Jmeno); // Jednoduchý index  

  modelBuilder.Entity<Student>()  
      .HasIndex(s => new { s.Prijmeni, s.Vek }); // Složený index  

  modelBuilder.Entity<Student>()  
      .HasIndex(s => s.Email)  
      .IsUnique(); // Unikátní index  
  ```  

---

#### **4. Pokročilá konfigurace**  

- **Název indexu**:  
  ```csharp  
  modelBuilder.Entity<Student>()  
      .HasIndex(s => s.Jmeno)  
      .HasDatabaseName("IX_Studenti_Jmeno");  
  ```  

- **Řazení (EF Core 7.0+)**:  
  ```csharp  
  modelBuilder.Entity<Student>()  
      .HasIndex(s => s.Prijmeni)  
      .IsDescending(); // Sestupné řazení  
  ```  

- **Filtrované indexy (partial)**:  
  ```csharp  
  modelBuilder.Entity<Student>()  
      .HasIndex(s => s.Jmeno)  
      .HasFilter("Vek > 18"); // Pouze pro studenty starší 18 let  
  ```  

---

#### **5. Kdy použít indexy?**  

- **Doporučené případy**:  
  - Sloupce často používány v podmínkách `WHERE`, `JOIN`, `ORDER BY`.  
  - Sloupce s vysokou kardinalitou (např. e-maily, telefonní čísla).  
  - Unikátní hodnoty (např. primární klíče).  
- **Nežádoucí případy**:  
  - Sloupce s nízkou kardinalitou (např. pohlaví).  
  - Tabulky s častými operacemi `INSERT`/`UPDATE` (indexy zpomalují zápis).  

---

#### **6. Běžné problémy**  

| **Problém** | **Příčina** | **Řešení** |  
|-------------|-------------|------------|  
| Zpomalení zápisu | Příliš mnoho indexů | Omezte indexy na kritické sloupce. |  
| Duplicitní data | Chybějící unikátní index | Použijte `IsUnique()`. |  
| Neočekávané chování dotazů | Nesprávně zvolené sloupce pro index | Analyzujte plány dotazů. |  

---

#### **7. Doporučené postupy**  

1. **Měřte výkon**: Používejte nástroje jako **SQL Server Execution Plans** nebo **EXPLAIN** (PostgreSQL).  
2. **Testujte migrace**: Po přidání indexu spusťte `dotnet ef migrations add`.  
3. **Sledujte velikost databáze**: Indexy zabírají místo na disku.  

---

**Shrnutí**:  
- Indexy urychlují **vyhledávání**, ale zpomalují **zápis**.  
- EF Core umožňuje konfigurovat je přes **Data Annotations** nebo **Fluent API**.  
- Unikátní a složené indexy řeší specifické problémy s integritou a výkonem.  

**Zdroje**:  
- [EF Core Dokumentace – Indexy](https://learn.microsoft.com/en-us/ef/core/modeling/indexes)  
- [EF Core 7.0 – Indexové vylepšení](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/whatsnew#index-enhancements)
