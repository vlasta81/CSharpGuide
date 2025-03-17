
### Dotazování na data v Entity Framework Core

---

#### **1. Základní dotazy pomocí LINQ**  

**LINQ (Language Integrated Query)** je základní nástroj pro dotazování v EF Core. Umožňuje psát dotazy přímo v C# s integrovanou kontrolou syntaxe.  

**Příklad**:  
```csharp  
// Všechny studenty  
var studenti = context.Studenti.ToList();  

// Student s ID 1  
var student = context.Studenti.FirstOrDefault(s => s.Id == 1);  
```  

**Klíčové metody**:  
- `ToList()`: Vrátí všechny výsledky.  
- `First()`/`FirstOrDefault()`: První prvek (vyvolá výjimku, pokud není nalezen).  
- `Single()`/`SingleOrDefault()`: Přesně jeden prvek (vyvolá výjimku pro více výsledků).  

---

#### **2. Filtrování a řazení**  

**Filtrování**: Omezení výsledků pomocí podmínek.  
```csharp  
var dospeli = context.Studenti  
    .Where(s => s.Vek >= 18)  
    .ToList();  
```  

**Řazení**:  
```csharp  
var serazeni = context.Studenti  
    .OrderBy(s => s.Prijmeni)  
    .ThenByDescending(s => s.Vek)  
    .ToList();  
```  

**Stránkování**:  
```csharp  
var stranka = context.Studenti  
    .Skip(10)  
    .Take(5)  
    .ToList();  
```  

---

#### **3. Načítání souvisejících dat**  

**Eager Loading**: Načte související data předem pomocí `Include` a `ThenInclude`.  
```csharp  
var studentiSKurzy = context.Studenti  
    .Include(s => s.Kurzy)  
    .ThenInclude(k => k.Ucitel)  
    .ToList();  
```  

**Explicitní Loading**: Načte související data na vyžádání.  
```csharp  
var student = context.Studenti.First();  
context.Entry(student).Collection(s => s.Kurzy).Load();  
```  

**Lazy Loading**: Automatické načítání při přístupu k vlastnosti (vyžaduje nastavení `UseLazyLoadingProxies`).  
```csharp  
var kurz = student.Kurzy; // Načte se až při přístupu  
```  

---

#### **4. Sledování změn vs. AsNoTracking**  

**Sledování změn (Tracking)**:  
- EF Core sleduje změny entit pro pozdější uložení (`SaveChanges`).  
- Vhodné pro editaci dat.  

**AsNoTracking**:  
- Zakáže sledování změn (lepší výkon pro read-only operace).  
```csharp  
var studenti = context.Studenti.AsNoTracking().ToList();  
```  

---

#### **5. Raw SQL dotazy**  

Pro složité dotazy nebo optimalizaci lze použít přímo SQL.  

**Dotaz s parametry (bezpečné proti SQL injection)**:  
```csharp  
var jmeno = "Jan";  
var studenti = context.Studenti  
    .FromSqlRaw("SELECT * FROM Studenti WHERE Jmeno = {0}", jmeno)  
    .ToList();  
```  

**ExecuteSqlRaw pro ne-dotazové operace**:  
```csharp  
context.Database.ExecuteSqlRaw("DELETE FROM Logy WHERE Datum < {0}", DateTime.Now.AddDays(-30));  
```  

---

#### **6. Asynchronní operace**  

Zlepšuje výkon aplikací pomocí `async/await`.  
```csharp  
var studenti = await context.Studenti  
    .Where(s => s.Vek > 18)  
    .ToListAsync();  
```  

**Metody**:  
- `ToListAsync()`, `FirstOrDefaultAsync()`, `ExecuteSqlRawAsync()`.  

---

#### **7. Projekce (Select)**  

Transformace výsledků do specifických objektů (DTO, anonymní typy).  
```csharp  
var studentiDTO = context.Studenti  
    .Select(s => new StudentDTO { Jmeno = s.Jmeno, Vek = s.Vek })  
    .ToList();  
```  

---

#### **8. Výkonnostní tipy**  

- **Omezení N+1 problému**: Používejte `Include` pro načítání souvisejících dat.  
- **Používejte AsNoTracking** pro read-only scénáře.  
- **Filtrujte na straně serveru**: Vyhněte se načítání zbytečných dat (např. `Where` před `ToList`).  
- **Používejte asynchronní metody** pro neblokující volání.  

---

#### **9. Časté chyby**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|---------------------------------------------|  
| N+1 problém (mnoho dotazů)           | Použijte `Include` nebo explicitní načítání. |  
| Načítání všech dat (`ToList` příliš brzy) | Filtrujte před materializací výsledků. |  
| SQL injection v raw dotazech         | Vždy používejte parametrizované dotazy.     |  

---

**Shrnutí**:  
- **LINQ** je základní nástroj pro dotazování s integrovanou typovou kontrolou.  
- **Eager Loading**, **Explicitní Loading** a **Lazy Loading** řeší načítání vztahů.  
- **AsNoTracking** zvyšuje výkon pro read-only operace.  
- **Raw SQL** umožňuje pokročilé optimalizace.  

**Zdroje**:  
- [EF Core Dokumentace – Dotazování](https://learn.microsoft.com/en-us/ef/core/querying/)  
- [EF Core – Raw SQL Queries](https://learn.microsoft.com/en-us/ef/core/querying/raw-sql)
