
### Ukládání dat v Entity Framework Core

---

#### **1. Základní operace (CRUD)**  

- **Create (Vytvoření)**:  
  ```csharp  
  var student = new Student { Jmeno = "Jan", Prijmeni = "Novák" };  
  context.Studenti.Add(student);  
  await context.SaveChangesAsync();  
  ```  
  - Metody: `Add()`, `AddAsync()`, `AddRange()` pro hromadné přidání.  

- **Read (Čtení)**:  
  ```csharp  
  var studenti = await context.Studenti.Where(s => s.Vek > 18).ToListAsync();  
  ```  

- **Update (Aktualizace)**:  
  ```csharp  
  var student = await context.Studenti.FindAsync(1);  
  student.Jmeno = "Petr";  
  await context.SaveChangesAsync();  
  ```  
  - Změny jsou automaticky detekovány díky **sledování změn (Change Tracking)**.  

- **Delete (Smazání)**:  
  ```csharp  
  var student = await context.Studenti.FindAsync(1);  
  context.Studenti.Remove(student);  
  await context.SaveChangesAsync();  
  ```  
  - Metody: `Remove()`, `RemoveRange()`.  

---

#### **2. Ukládání změn**  

- **SaveChanges() / SaveChangesAsync()**:  
  - Provádí všechny čekající operace (INSERT, UPDATE, DELETE) v jedné **transakci**.  
  - Vrací počet ovlivněných řádků.  
  - **Asynchronní verze** (`SaveChangesAsync`) je vhodná pro webové aplikace.  

---

#### **3. Transakce**  

- **Implicitní transakce**: `SaveChanges` vytváří transakci automaticky.  
- **Explicitní transakce**:  
  ```csharp  
  using var transaction = await context.Database.BeginTransactionAsync();  
  try  
  {  
      // Operace...  
      await context.SaveChangesAsync();  
      await transaction.CommitAsync();  
  }  
  catch  
  {  
      await transaction.RollbackAsync();  
  }  
  ```  

---

#### **4. Řešení konfliktů (Optimistická souběžnost)**  

- **Concurrency Token**: Sloupec (např. `RowVersion`) označený atributem `[Timestamp]`.  
  ```csharp  
  public class Student  
  {  
      [Timestamp]  
      public byte[] RowVersion { get; set; }  
  }  
  ```  
- **Ošetření výjimky `DbUpdateConcurrencyException`**:  
  ```csharp  
  try  
  {  
      await context.SaveChangesAsync();  
  }  
  catch (DbUpdateConcurrencyException ex)  
  {  
      // Reload entity a znovu aplikujte změny  
  }  
  ```  

---

#### **5. Validace před uložením**  

- **Automatická validace**: EF Core ověřuje data dle anotací (`[Required]`, `[MaxLength]`).  
- **Vypnutí validace**:  
  ```csharp  
  context.ChangeTracker.AutoDetectChangesEnabled = false;  
  ```  
- **Vlastní validace**:  
  ```csharp  
  var validationResults = context.Entry(student).GetValidationResult();  
  ```  

---

#### **6. Práce s nesledovanými entitami**  

- **Attach (Připojení entit)**:  
  ```csharp  
  context.Attach(student); // Začne sledovat existující entitu  
  student.Jmeno = "Nové jméno";  
  await context.SaveChangesAsync();  
  ```  
- **Detach (Odpojení entit)**:  
  ```csharp  
  context.Entry(student).State = EntityState.Detached;  
  ```  

---

#### **7. Hromadné operace**  

- **EF Core Bulk Extensions** (externí knihovna):  
  ```csharp  
  await context.BulkInsertAsync(seznamStudentu);  
  await context.BulkUpdateAsync(seznamStudentu);  
  ```  
- **Omezení EF Core**: Pro hromadné operace je lepší použít přímo SQL nebo knihovny.  

---

#### **8. Zpracování chyb**  

- **Výjimky**:  
  - `DbUpdateException`: Obecná chyba při ukládání (např. porušení UNIQUE constraint).  
  - `DbUpdateConcurrencyException`: Konflikt při optimistické souběžnosti.  

---

#### **9. Pokročilé techniky**  

- **Interceptory**:  
  ```csharp  
  public class SaveChangesInterceptor : SaveChangesInterceptor  
  {  
      public override InterceptionResult<int> SavingChanges(...)  
      {  
          // Logika před uložením  
          return base.SavingChanges(...);  
      }  
  }  
  ```  
- **Sledování změn (Change Tracking)**:  
  - `context.ChangeTracker.Entries()` – přístup ke všem sledovaným entitám.  

---

#### **10. Doporučené postupy**  

- Používejte **asynchronní metody** pro neblokující volání.  
- Omezte počet volání `SaveChanges` pro snížení režije.  
- Testujte **konkurenční scénáře** a transakce.  

---

**Shrnutí**:  
- **CRUD operace** se provádí přes `Add`, `Remove`, a změny vlastností, následované `SaveChanges`.  
- **Transakce** zajišťují atomičnost operací.  
- **Optimistická souběžnost** řeší konflikty pomocí concurrency tokenů.  
- **Validace** a **správa výjimek** jsou klíčové pro robustní aplikace.  

**Zdroje**:  
- [EF Core Dokumentace – Ukládání dat](https://learn.microsoft.com/en-us/ef/core/saving/)  
- [EF Core – Concurrency Tokens](https://learn.microsoft.com/en-us/ef/core/modeling/concurrency)
