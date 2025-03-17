
### Sekvence v Entity Framework Core

---

#### **1. Co je sekvence?**  

- **Definice**: Databázový objekt, který generuje **posloupnost čísel** nezávisle na tabulkách.  
- **Účel**:  
  - Generování unikátních hodnot pro primární klíče nebo jiné sloupce.  
  - Sdílení číselné řady mezi více tabulkami nebo databázemi.  
- **Výhody oproti autoinkrementaci**:  
  - Kontrola nad formátem (start, krok, cyklování).  
  - Nezávislost na konkrétní tabulce.  

---

#### **2. Vytvoření sekvence**  

**Konfigurace pomocí Fluent API** (v metodě `OnModelCreating`):  
```csharp  
modelBuilder.HasSequence<int>("OrderNumberSequence")  
    .StartsAt(1000)    // Počáteční hodnota  
    .IncrementsBy(5)   // Krok  
    .HasMin(100)       // Minimální hodnota  
    .HasMax(9999)      // Maximální hodnota  
    .IsCyclic();       // Po dosažení maxima začne znovu od minima  
```  

---

#### **3. Použití sekvence v entitách** 

**Přiřazení sekvence k vlastnosti entity**:  
```csharp  
modelBuilder.Entity<Order>(entity =>  
{  
    entity.Property(o => o.OrderNumber)  
        .HasDefaultValueSql("NEXT VALUE FOR OrderNumberSequence");  
});  
```  

**Příklad entity**:  
```csharp  
public class Order  
{  
    public int Id { get; set; }  
    public int OrderNumber { get; set; } // Hodnota generovaná sekvencí  
    public string Customer { get; set; }  
}  
```  

---

#### **4. Pokročilé nastavení**  

- **Definice datového typu**:  
  ```csharp  
  modelBuilder.HasSequence<decimal>("PriceSequence");  
  ```  

- **Cacheování hodnot** (optimalizace výkonu):  
  ```csharp  
  modelBuilder.HasSequence("OrderNumberSequence")  
      .StartsAt(1000)  
      .IncrementsBy(10)  
      .HasCache(20); // Ukládá 20 hodnot do mezipaměti  
  ```  

- **Resetování sekvence**:  
  ```sql  
  ALTER SEQUENCE OrderNumberSequence RESTART WITH 1000;  
  ```  

---

#### **5. Kdy použít sekvenci?**  

| **Scénář**                     | **Výhoda sekvence**                      |  
|--------------------------------|------------------------------------------|  
| Sdílení číselné řady mezi tabulkami | Jedna sekvence pro více entit.           |  
| Vlastní formát čísel (např. začátek od 1000) | Flexibilita nastavení.                   |  
| Generování hodnot mimo transakce | Sekvence není ovlivněna rollbacky.       |  

---

#### **6. Omezení a problémy**  

- **Podpora databází**:  
  - Podporováno v **SQL Server**, **PostgreSQL**, **Oracle**.  
  - **Nepodporuje SQLite**.  
- **Časté chyby**:  
  - `NEXT VALUE FOR` není povoleno ve výchozích hodnotách bez explicitní konfigurace.  
  - Sekvence musí být vytvořena před prvním použitím (pomocí migrací).  

---

#### **7. Porovnání s autoinkrementacím**  

| **Parametr**       | **Sekvence**                     | **Autoinkrementace**               |  
|--------------------|----------------------------------|------------------------------------|  
| **Nezávislost**    | Ano (lze sdílet mezi tabulkami) | Ne (vázáno na konkrétní tabulku)   |  
| **Formátování**    | Flexibilní (start, krok, min/max)| Pevné (vždy +1)                    |  
| **Výkon**          | Lepší u vysoké zátěže (cache)    | Závisí na databázi                 |  

---

#### **8. Doporučené postupy**  

1. **Migrace**: Po definici sekvence vždy vytvořte migraci:  
   ```bash  
   dotnet ef migrations add AddOrderNumberSequence  
   dotnet ef database update  
   ```  
2. **Testování**: Ověřte generování hodnot v testovací databázi.  
3. **Dokumentace**: Popište účel sekvence v kódu pro přehlednost.  

---

**Shrnutí**:  
- Sekvence generují unikátní čísla **nezávisle na tabulkách**.  
- Konfigurují se přes **Fluent API** a používají se pro sloupce pomocí `NEXT VALUE FOR`.  
- Jsou vhodné pro komplexní scénáře, kde autoinkrementace nestačí.  

**Zdroje**:  
- [EF Core Dokumentace – Sekvence](https://learn.microsoft.com/en-us/ef/core/modeling/sequences)  
- [SQL Server Sequence vs Identity](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-sequence-transact-sql)
