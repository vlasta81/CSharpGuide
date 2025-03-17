
### Typy entit bez klíčů v Entity Framework Core

---

#### **1. Definice a účel**  

- **Typy entit bez klíčů (Keyless Entity Types)**: Entity, které **nemají primární klíč** a nejsou sledovány změnovým trackingem.  
- **Účel**:  
  - Mapování na **databázové pohledy** (views), které nemají primární klíč.  
  - Práce s výsledky **raw SQL dotazů** nebo uložených procedur.  
  - Reprezentace dočasných dat (např. výsledky agregací).  

---

#### **2. Kdy použít entity bez klíčů?**  

| **Scénář**                     | **Příklad**                          |  
|--------------------------------|--------------------------------------|  
| **Čtení dat z pohledů**        | `SELECT * FROM StudentiView`         |  
| **Výsledky komplexních dotazů** | Agregace, spojení více tabulek.      |  
| **Dočasné struktury**          | Data pro reporty nebo exporty.       |  

---

#### **3. Konfigurace entit bez klíčů**  

- **Data Annotations**:  
  ```csharp  
  [Keyless] // Atribut označující entitu bez klíče  
  public class StudentiView  
  {  
      public string Jmeno { get; set; }  
      public int Vek { get; set; }  
  }  
  ```  

- **Fluent API**:  
  ```csharp  
  modelBuilder.Entity<StudentiView>()  
      .HasNoKey() // Explicitní označení entity bez klíče  
      .ToView("StudentiView"); // Mapování na databázový pohled  
  ```  

---

#### **4. Omezení**  

- **Žádný změnový tracking**: Entity nelze aktualizovat, mazat ani přidávat přes `SaveChanges()`.  
- **Nelze použít pro standardní operace**: Pouze pro **dotazování** (`SELECT`).  
- **Žádné navigační vlastnosti**: Nelze definovat vztahy (jako `HasMany` nebo `WithOne`).  

---

#### **5. Příklady použití**  

**a) Mapování na databázový pohled**:  
```csharp  
[Keyless]  
public class StudentiView  
{  
    public string Jmeno { get; set; }  
    public int Prumer { get; set; }  
}  

// Konfigurace v DbContextu:  
modelBuilder.Entity<StudentiView>()  
    .ToView("StudentiPrumer");  
```  

**b) Výsledek raw SQL dotazu**:  
```csharp  
var studenti = context.StudentiView  
    .FromSqlRaw("SELECT Jmeno, COUNT(*) AS PocetKurzu FROM Studenti GROUP BY Jmeno")  
    .ToList();  
```  

---

#### **6. Časté problémy a řešení**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|---------------------------------------------|  
| Chyba „The entity type requires a key“ | Přidejte `[Keyless]` nebo `HasNoKey()`.     |  
| Pokus o uložení změn                 | Entity bez klíčů nelze upravovat – používejte je pouze pro čtení. |  
| Neexistující sloupce v pohledu       | Ověřte shodu názvů sloupců mezi entitou a pohledem. |  

---

#### **7. Doporučené postupy**  

1. **Používejte pro čistě read-only operace**: Reporty, statistiky, exporty.  
2. **Kombinujte s `FromSqlRaw`**: Pro flexibilní dotazování.  
3. **Testujte výkon**: Agregace přes pohledy mohou být náročné.  

---

**Shrnutí**:  
- Entity bez klíčů slouží k **mapování dat, která nelze měnit** (pohledy, výsledky dotazů).  
- Konfigurují se pomocí atributu `[Keyless]` nebo metody `HasNoKey()`.  
- Jsou ideální pro **analytické dotazy** a **dočasná data**.  

**Zdroje**:  
- [EF Core Dokumentace – Typy entit bez klíčů](https://learn.microsoft.com/en-us/ef/core/modeling/keyless-entity-types)  
- [EF Core – Práce s pohledy](https://learn.microsoft.com/en-us/ef/core/modeling/keyless-entity-types?tabs=fluent-api#mapping-to-a-sql-view)
