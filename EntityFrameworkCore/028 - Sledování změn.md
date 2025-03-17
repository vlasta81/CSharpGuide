
### Sledování změn (Change Tracking) v Entity Framework Core

---

#### **1. Co je sledování změn?**  

- **Definice**: Mechanismus, který EF Core používá k **detekci změn** u entit a jejich synchronizaci s databází při volání `SaveChanges()`.  
- **Účel**:  
  - Sledovat stav entit (přidány, upraveny, smazány).  
  - Automaticky generovat SQL příkazy pro INSERT/UPDATE/DELETE.  
  - Optimalizovat výkon (změny se detekují pouze u upravených entit).  

---

#### **2. Stavy entit (Entity States)**  

Každá entita má stav, který určuje, jak bude zpracována při `SaveChanges()`:  
| **Stav**           | **Popis**                                                                 |  
|---------------------|---------------------------------------------------------------------------|  
| **Detached**        | Entita není sledována (např. nově vytvořená před `Add()`).               |  
| **Unchanged**       | Entita je načtena z DB a nebyla změněna.                                 |  
| **Added**           | Entita bude přidána do DB při `SaveChanges()`.                           |  
| **Modified**        | Entita byla upravena a změny budou uloženy.                              |  
| **Deleted**         | Entita bude smazána z DB při `SaveChanges()`.                            |  

**Příklad**:  
```csharp  
var student = context.Studenti.Find(1);  
student.Jmeno = "Nové jméno"; // Stav se změní na Modified  
context.SaveChanges(); // UPDATE příkaz  
```  

---

#### **3. Jak funguje detekce změn?**  

- **Snapshot Tracking (Výchozí režim)**:  
  - EF Core ukládá **původní hodnoty** entit při načtení.  
  - Při `SaveChanges()` porovná původní a aktuální hodnoty.  
- **Proaktivní detekce**:  
  - Entity implementují `INotifyPropertyChanged` a hlásí změny automaticky.  
  - Aktivuje se nastavením `ChangeTracker.AutoDetectChangesEnabled = true`.  

---

#### **4. Konfigurace sledování změn**  

- **Vypnutí sledování pro konkrétní dotaz**:  
  ```csharp  
  var studenti = context.Studenti.AsNoTracking().ToList(); // Entity nejsou sledovány  
  ```  
- **Globální vypnutí sledování**:  
  ```csharp  
  context.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;  
  ```  
- **Ruční správa stavu entit**:  
  ```csharp  
  context.Entry(student).State = EntityState.Modified; // Vynucení stavu  
  ```  

---

#### **5. Metody pro práci se stavy** 

- **Attach()**: Připojí entitu do kontextu ve stavu `Unchanged`.  
  ```csharp  
  context.Attach(student); // Stav: Unchanged  
  ```  
- **Detach()**: Odpojí entitu od kontextu (stav `Detached`).  
  ```csharp  
  context.Entry(student).State = EntityState.Detached;  
  ```  
- **Update()**: Označí entitu jako `Modified`.  
  ```csharp  
  context.Update(student); // Stav: Modified  
  ```  

---

#### **6. Výkonnostní aspekty**  

- **Problémy**:  
  - Sledování změn u velkých datasetů může spotřebovávat paměť a CPU.  
  - Časté volání `DetectChanges()` zpomaluje aplikaci.  
- **Řešení**:  
  - Používejte `AsNoTracking()` pro read-only operace.  
  - Vypínejte automatickou detekci (`AutoDetectChangesEnabled = false`) a volejte `DetectChanges()` ručně.  

---

#### **7. Běžné problémy a řešení**  

| **Problém**                          | **Příčina**                     | **Řešení**                         |  
|--------------------------------------|----------------------------------|-------------------------------------|  
| Neočekávané změny v DB               | Nechtěné sledování entit.        | Použijte `AsNoTracking()`.          |  
| Duplicitní entity                    | Stejná entita načtena vícekrát. | Použijte `Detach()` nebo `NoTracking`. |  
| Nízký výkon                          | Sledování velkého počtu entit.   | Omezte sledování jen na nutné entity. |  

---

#### **8. Best Practices**  

1. **Omezte sledování**: Pro čtení dat používejte `AsNoTracking()`.  
2. **Ruční správa stavů**: U komplexních operací explicitně nastavujte `EntityState`.  
3. **Detekce změn**: Vypněte `AutoDetectChangesEnabled` a volejte `DetectChanges()` ručně, pokud je to nutné.  

---

**Shrnutí**:  
- Sledování změn umožňuje EF Core **automaticky detekovat a ukládat změny** entit.  
- **Stavy entit** určují, jaké operace se provedou v databázi.  
- Pro optimalizaci výkonu používejte `AsNoTracking()` a ruční správu stavů.  

**Zdroje**:  
- [EF Core Dokumentace – Sledování změn](https://learn.microsoft.com/en-us/ef/core/change-tracking/)  
- [EF Core – Entity States](https://learn.microsoft.com/en-us/ef/core/change-tracking/entity-states)
