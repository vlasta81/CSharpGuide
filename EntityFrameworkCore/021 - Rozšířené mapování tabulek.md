
### Rozšířené mapování tabulek v Entity Framework Core

---

#### **1. Úvod do rozšířeného mapování**  

Rozšířené mapování tabulek umožňuje přizpůsobit, jak entity a jejich vztahy odpovídají databázovým strukturám. EF Core nabízí flexibilní nástroje pro práci s nestandardními scénáři, jako je rozdělení entit do více tabulek, kombinace entit do jedné tabulky nebo mapování na databázové pohledy a procedury.

---

#### **2. Hlavní techniky rozšířeného mapování**  

**a) Dělení tabulky (Table Splitting)**  
- **Definice**: Jedna entita je mapována na více tabulek.  
- **Použití**: Když entita obsahuje mnoho sloupců a je vhodné je rozdělit pro optimalizaci výkonu.  
- **Příklad**:  
  ```csharp  
  modelBuilder.Entity<Student>(entity =>  
  {  
      entity.ToTable("Studenti");  
      entity.OwnsOne(s => s.Kontakt, k =>  
      {  
          k.ToTable("Kontakty");  
          k.Property(k => k.Email).HasColumnName("Email");  
      });  
  });  
  ```  

**b) Vlastněné typy (Owned Types)**  
- **Definice**: Typ bez vlastního klíče, který je součástí jiné entity (např. `Adresa` v `Zakaznik`).  
- **Použití**: Pro hodnotové objekty, které nemají samostatnou identitu.  
- **Příklad**:  
  ```csharp  
  modelBuilder.Entity<Zakaznik>().OwnsOne(z => z.Adresa);  
  ```  

**c) Mapování dědičnosti**  
- **TPH (Table per Hierarchy)**: Všechny třídy v hierarchii jsou v jedné tabulce s diskriminačním sloupcem.  
- **TPT (Table per Type)**: Každá třída má vlastní tabulku.  
- **TPC (Table per Concrete Class)**: Každá konkrétní třída má vlastní tabulku s kopií všech zděděných vlastností.  

**d) Mapování na pohledy (Views)**  
- **Definice**: Entita je mapována na SQL pohled místo tabulky.  
- **Příklad**:  
  ```csharp  
  modelBuilder.Entity<Student>().ToView("StudentiView");  
  ```  

**e) Mapování na uložené procedury**  
- **Definice**: CRUD operace jsou prováděny přes uložené procedury.  
- **Příklad**:  
  ```csharp  
  modelBuilder.Entity<Student>()  
      .InsertUsingStoredProcedure("InsertStudent")  
      .DeleteUsingStoredProcedure("DeleteStudent");  
  ```  

---

#### **3. Pokročilá konfigurace pomocí Fluent API**  

**a) Přizpůsobení názvů sloupců a tabulek**  
```csharp  
modelBuilder.Entity<Student>()  
    .ToTable("Studenti")  
    .Property(s => s.Jmeno)  
    .HasColumnName("CeleJmeno");  
```  

**b) Složené klíče**  
```csharp  
modelBuilder.Entity<Hodnoceni>()  
    .HasKey(h => new { h.StudentId, h.PredmetId });  
```  

**c) Výchozí hodnoty a restrikce**  
```csharp  
modelBuilder.Entity<Produkt>()  
    .Property(p => p.Cena)  
    .HasDefaultValue(0)  
    .HasCheckConstraint("Cena >= 0");  
```  

---

#### **4. Scénáře a doporučení**  

**a) Kdy použít dělení tabulek?**  
- Když entita obsahuje zřídka používané sloupce (např. BLOB data), která je vhodné oddělit.  

**b) Kdy použít vlastněné typy?**  
- Pro logické seskupení vlastností (např. adresa, kontaktní údaje).  

**c) Kdy použít mapování na pohledy?**  
- Pro zjednodušení dotazů nebo skrytí komplexních dotazů za abstrakcí.  

---

#### **5. Časté problémy a řešení**  

| **Problém** | **Příčina** | **Řešení** |  
|-------------|-------------|------------|  
| Chybějící diskriminační sloupec u TPH | EF Core očekává sloupec pro rozlišení typů. | Explicitně přidejte diskriminační sloupec do modelu. |  
| Nevalidní data při použití pohledů | Pohled neobsahuje požadované sloupce. | Ověřte strukturu pohledu a mapování. |  
| Konflikty klíčů u TPC | Duplicitní ID napříč tabulkami. | Použijte ruční nastavení ID nebo sekvence. |  

---

#### **6. Shrnutí**  

- **Rozšířené mapování** umožňuje přizpůsobit EF Core nestandardním databázovým strukturám.  
- **Vlastněné typy**, **dělení tabulek** a **mapování dědičnosti** řeší specifické problémy s návrhem databáze.  
- **Fluent API** poskytuje nejvyšší míru kontroly nad konfigurací.  

**Zdroje**:  
- Oficiální dokumentace EF Core: [Advanced Table Mapping](https://learn.microsoft.com/en-us/ef/core/modeling/table-splitting)  
