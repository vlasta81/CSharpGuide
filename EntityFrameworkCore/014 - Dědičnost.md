
### Dědičnost v Entity Framework Core

---

#### **1. Co je dědičnost v EF Core?**  

- **Definice**: Mechanismus, který umožňuje mapovat hierarchii tříd (rodičovská a potomkové třídy) do relační databáze.  
- **Účel**: Zjednodušuje práci s objekty, které sdílejí společné vlastnosti, ale mají rozdílné chování.  
- **Příklad**: Hierarchie `Osoba` → `Student`, `Učitel`.  

---

#### **2. Strategie dědičnosti**  

EF Core podporuje tři hlavní přístupy:  

1. **TPH (Table per Hierarchy)**  
   - **Popis**: Všechny třídy v hierarchii jsou uloženy v **jedné tabulce**.  
   - **Diskriminační sloupec**: Rozlišuje typ entity (např. `TypOsoby` s hodnotami "student", "učitel").  
   - **Výhody**: Jednoduchá implementace, rychlé dotazy.  
   - **Nevýhody**: Sparse tabulka (mnoho `NULL` hodnot).  

   **Konfigurace (Fluent API)**:  
   ```csharp  
   modelBuilder.Entity<Osoba>()  
       .HasDiscriminator<string>("TypOsoby")  
       .HasValue<Student>("student")  
       .HasValue<Ucitel>("ucitel");  
   ```  

2. **TPT (Table per Type)**  
   - **Popis**: Každá třída má vlastní tabulku. Potomci dědí sloupce z rodičovské tabulky.  
   - **Výhody**: Normalizovaná struktura, méně `NULL` hodnot.  
   - **Nevýhody**: Pomalejší dotazy kvůli `JOIN`.  

   **Konfigurace (Data Annotations)**:  
   ```csharp  
   [Table("Studenti")]  
   public class Student : Osoba { ... }  

   [Table("Ucitele")]  
   public class Ucitel : Osoba { ... }  
   ```  

3. **TPC (Table per Concrete Class)**  
   - **Popis**: Každá konkrétní třída má vlastní tabulku, která obsahuje **všechny vlastnosti** (včetně zděděných).  
   - **Výhody**: Žádné `JOIN`, vhodné pro velké hierarchie.  
   - **Nevýhody**: Duplikace sloupců, složitější správa.  

   **Konfigurace (EF Core 7.0+)**  
   ```csharp  
   modelBuilder.Entity<Student>().UseTpcMappingStrategy();  
   modelBuilder.Entity<Ucitel>().UseTpcMappingStrategy();  
   ```  

---

#### **3. Kdy použít kterou strategii?**  

| **Strategie** | **Vhodné pro**                                | **Příklad**                      |  
|---------------|-----------------------------------------------|----------------------------------|  
| **TPH**       | Malé hierarchie, rychlé dotazy                | `Osoba` → `Student`, `Učitel`    |  
| **TPT**       | Normalizovaná data, redukce `NULL`            | `Produkt` → `Elektronika`, `Kniha` |  
| **TPC**       | Velké hierarchie bez sdílených vlastností     | `DopravniProstredek` → `Auto`, `Letadlo` |  

---

#### **4. Běžné problémy a řešení**  

- **Chybějící diskriminační sloupec (TPH)**:  
  - **Příznak**: Chyba při dotazování: *"Discriminator column not found"*.  
  - **Řešení**: Explicitně přidejte diskriminační sloupec do modelu.  

- **Duplicitní ID (TPC)**:  
  - **Příznak**: Konflikty primárních klíčů mezi tabulkami.  
  - **Řešení**: Použijte ruční nastavení ID nebo sekvence.  

- **Neočekávané `JOIN` (TPT)**:  
  - **Příznak**: Pomalé dotazy.  
  - **Řešení**: Zvažte přechod na TPH nebo optimalizaci indexů.  

---

#### **5. Doporučené postupy**  

1. **TPH jako výchozí volba**: Pro většinu scénářů je nejjednodušší.  
2. **Fluent API pro komplexní konfiguraci**: Umožňuje větší kontrolu než Data Annotations.  
3. **Migrace**: Po změně strategie vždy spusťte:  
   ```bash  
   dotnet ef migrations add UpdateInheritanceStrategy  
   dotnet ef database update  
   ```  

---

#### **6. Příklad TPH v praxi**  

**Entity**:  
```csharp  
public abstract class Osoba  
{  
    public int Id { get; set; }  
    public string Jmeno { get; set; }  
}  

public class Student : Osoba  
{  
    public string Obor { get; set; }  
}  

public class Ucitel : Osoba  
{  
    public string Titul { get; set; }  
}  
```  

**Generovaná tabulka `Osoba`**:  
| Id | Jmeno  | TypOsoby | Obor  | Titul |  
|----|--------|-----------|-------|-------|  
| 1  | Jan    | student   | IT    | NULL  |  
| 2  | Eva    | ucitel    | NULL  | PhD   |  

---

**Shrnutí**:  
- **TPH**: Jedna tabulka pro celou hierarchii (s diskriminátorem).  
- **TPT**: Samostatné tabulky pro každou třídu (dědičnost přes `JOIN`).  
- **TPC**: Tabulky pro konkrétní třídy bez sdílených sloupců.  

**Zdroje**:  
- [EF Core Dokumentace – Dědičnost](https://learn.microsoft.com/en-us/ef/core/modeling/inheritance)  
- [TPC v EF Core 7](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-7.0/whatsnew#table-per-concrete-type-tpc-mapping)
