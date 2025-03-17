
### Stínové a indexerové vlastnosti v Entity Framework Core

---

#### **1. Stínové vlastnosti (Shadow Properties)**  

- **Definice**: Vlastnosti, které **nejsou definovány v entitě**, ale existují v databázovém modelu.  
- **Účel**: Ukládání dat, která nejsou součástí doménového modelu (např. technické sloupce pro audit, soft delete).  
- **Příklady**: `CreatedAt`, `UpdatedAt`, `IsDeleted`.  

**Konfigurace (Fluent API)**:  
```csharp
modelBuilder.Entity<Student>().Property<DateTime>("DatumVytvoreni"); // Stínová vlastnost
modelBuilder.Entity<Student>().Property<bool>("JeSmazany");
```

**Přístup ke stínovým vlastnostem**:  
```csharp
var student = new Student();
var entry = context.Entry(student);
entry.Property("DatumVytvoreni").CurrentValue = DateTime.Now;
```

---

#### **2. Indexerové vlastnosti (Indexer Properties)** 

- **Definice**: Vlastnosti přístupné přes **indexer** (např. `entity["NazevVlastnosti"]`).  
- **Účel**: Dynamický přístup k hodnotám vlastností bez pevné vazby na třídu.  
- **Příklad**:  
  ```csharp
  public class Student
  {
      // Definice indexeru
      private readonly Dictionary<string, object> _data = new();
      public object this[string key]
      {
          get => _data.TryGetValue(key, out var value) ? value : null;
          set => _data[key] = value;
      }
  }

  // Použití:
  student["DatumUpravy"] = DateTime.Now;
  ```

---

#### **3. Společné rysy a rozdíly**  

| **Vlastnost**          | **Stínové vlastnosti**                  | **Indexerové vlastnosti**              |  
|-------------------------|-----------------------------------------|----------------------------------------|  
| **Definice v entitě**   | Nejsou součástí třídy.                  | Jsou definovány přes indexer.           |  
| **Přístup**             | Pouze přes `DbContext.Entry()`.         | Přímo přes entitu (`entity["Nazev"]`). |  
| **Použití v LINQ**      | Lze použít v dotazech.                  | Omezená podpora (nutné přetypování).    |  

---

#### **4. Konfigurace stínových vlastností**  

- **Datový typ a výchozí hodnota**:  
  ```csharp
  modelBuilder.Entity<Student>()
      .Property<DateTime>("DatumVytvoreni")
      .HasDefaultValueSql("GETDATE()");
  ```  
- **Vztahy pomocí stínových vlastností**:  
  ```csharp
  modelBuilder.Entity<Student>()
      .HasOne<Trida>()
      .WithMany()
      .HasForeignKey("TridaId"); // Stínový cizí klíč
  ```

---

#### **5. Použití indexerových vlastností v dotazech**  

- **LINQ dotaz s přetypováním**:  
  ```csharp
  var studenti = context.Students
      .Where(s => EF.Property<DateTime>(s, "DatumUpravy") > DateTime.Now.AddDays(-7))
      .ToList();
  ```  

---

#### **6. Příklady použití**  

- **Stínové vlastnosti**:  
  - **Audit sloupce**: `CreatedBy`, `ModifiedBy`.  
  - **Soft delete**: `IsDeleted`.  
  - **Optimalizace pro migrace**: Dočasné sloupce.  

- **Indexerové vlastnosti**:  
  - **Dynamické entity**: Když struktura dat není předem známá.  
  - **Flexibilní mapování**: Pro prototypování nebo generické řešení.  

---

#### **7. Časté problémy** 

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|---------------------------------------------|  
| Chyba „The property 'X' is not defined“ | Ověřte název stínové vlastnosti a konfiguraci Fluent API. |  
| Neočekávané chování u indexerů       | Používejte přetypování přes `EF.Property<T>()`. |  
| Migrace nerozpozná stínové vlastnosti | Přidejte explicitní konfiguraci v `OnModelCreating`. |  

---

#### **8. Doporučené postupy**  

- **Stínové vlastnosti**:  
  - Používejte pro technické/metadatové údaje.  
  - Vyhněte se přímému přístupu přes řetězce (raději použijte konstanty).  
- **Indexerové vlastnosti**:  
  - Vhodné pro dynamická data, ale omezeně v komplexních dotazech.  
  - Dokumentujte názvy vlastností pro přehlednost.  

---

**Shrnutí**:  
- **Stínové vlastnosti** umožňují pracovat se sloupci, které nejsou v entitě.  
- **Indexerové vlastnosti** poskytují dynamický přístup k datům.  
- Obě techniky jsou užitečné pro specifické scénáře, ale vyžadují opatrnost.  

**Zdroje**:  
- [EF Core Shadow Properties](https://learn.microsoft.com/en-us/ef/core/modeling/shadow-properties)  
- [EF Core Indexer Properties](https://learn.microsoft.com/en-us/ef/core/modeling/indexer-properties)
