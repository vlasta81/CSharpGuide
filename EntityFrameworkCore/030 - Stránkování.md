
### Stránkování v Entity Framework Core

---

#### **1. Úvod do stránkování**  

Stránkování (paging) je technika rozdělení velkého množství dat na menší části (stránky), které se postupně načítají.  
- **Účel**:  
  - Zlepšení výkonu aplikace (omezení přenosu velkých objemů dat).  
  - Lepší uživatelský zážitek (možnost procházet data po částech).  

---

#### **2. Základní metody pro stránkování**  

EF Core používá LINQ metody `Skip()` a `Take()`:  
- **`Skip(n)`**: Přeskočí prvních `n` záznamů.  
- **`Take(m)`**: Vrátí následujících `m` záznamů.  

**Příklad**:  
```csharp  
int pageNumber = 1;  
int pageSize = 10;  

var studenti = await context.Studenti  
    .OrderBy(s => s.Prijmeni)  
    .Skip((pageNumber - 1) * pageSize)  
    .Take(pageSize)  
    .ToListAsync();  
```  

---

#### **3. Serverové vs. klientovské stránkování**  

| **Serverové stránkování**                     | **Klientovské stránkování**                  |  
|-----------------------------------------------|----------------------------------------------|  
| Data filtruje a řadí databáze.                | Všechna data se načtou do paměti a filtrují na straně klienta. |  
| Efektivní pro velké datasety.                 | Nevhodné pro velká data (vysoká paměťová náročnost). |  
| Používá `Skip()` a `Take()` v LINQ.           | Používá např. `List.Skip()` a `List.Take()`. |  

---

#### **4. Počítání celkového počtu záznamů**  

Pro zobrazení počtu stránek je třeba znát celkový počet záznamů:  
```csharp  
int totalRecords = await context.Studenti.CountAsync();  
int totalPages = (int)Math.Ceiling(totalRecords / (double)pageSize);  
```  

**Příklad kompletního dotazu**:  
```csharp  
public async Task<PaginatedResult<Student>> GetStudenti(int pageNumber, int pageSize)  
{  
    var query = context.Studenti.OrderBy(s => s.Prijmeni);  
    int total = await query.CountAsync();  

    var items = await query  
        .Skip((pageNumber - 1) * pageSize)  
        .Take(pageSize)  
        .ToListAsync();  

    return new PaginatedResult<Student>(items, pageNumber, pageSize, total);  
}  
```  

---

#### **5. Řazení – kritická součást stránkování**  

- Bez explicitního řazení může být výsledek stránkování **nekonzistentní** (data se mohou mezi stránkami opakovat).  
- **Příklad řazení**:  
  ```csharp  
  .OrderBy(s => s.Prijmeni).ThenBy(s => s.Jmeno)  
  ```  

---

#### **6. Optimalizace stránkování**  

- **Keyset Pagination (pomocí klíčů)**:  
  - Místo `Skip()` použijte filtrování podle posledního klíče předchozí stránky.  
  - **Výhoda**: Vyhne se problémům s `OFFSET` u velmi velkých datasetů.  
  ```csharp  
  var lastId = 100; // ID posledního záznamu na předchozí stránce  
  var studenti = await context.Studenti  
      .Where(s => s.Id > lastId)  
      .Take(pageSize)  
      .ToListAsync();  
  ```  

- **Použití `AsNoTracking()`**: Zvyšuje výkon pro read-only operace.  
  ```csharp  
  .AsNoTracking().OrderBy(...).Skip(...).Take(...)  
  ```  

---

#### **7. Časté chyby a doporučení** 

| **Chyba**                          | **Řešení**                                  |  
|------------------------------------|---------------------------------------------|  
| Zapomenuté řazení                  | Vždy používejte `OrderBy`/`OrderByDescending`. |  
| Stránkování bez počítání celku     | Zahrňte `CountAsync()` pro zobrazení počtu stránek. |  
| Pomalé stránkování u velkých dat   | Použijte **keyset pagination** místo `Skip()`. |  

---

#### **8. Shrnutí** 

- **Základní metody**: `Skip()` a `Take()` s explicitním řazením.  
- **Serverové stránkování** je efektivní, **klientovské** jen pro malá data.  
- **Počítání celkového počtu** záznamů umožňuje zobrazit rozsah stránek.  
- **Optimalizace**: Keyset pagination a `AsNoTracking()`.  

**Doporučení**:  
- Pro velké datasety preferujte **keyset pagination** nad `Skip()`.  
- Testujte výkon – `OFFSET` může být pomalý u milionů záznamů.  

---

**Zdroje**:  
- [EF Core Dokumentace – Stránkování](https://learn.microsoft.com/en-us/ef/core/querying/pagination)  
- [Keyset Pagination v EF Core](https://www.npgsql.org/efcore/misc/other.html#keyset-pagination)
