
### Rozdělené dotazy (Split Queries) v Entity Framework Core

---

#### **1. Co jsou rozdělené dotazy?**  

- **Definice**: Mechanismus, který rozděluje jeden komplexní dotaz s `JOIN` na **více samostatných dotazů** pro načítání souvisejících dat.  
- **Účel**: Vyhnout se tzv. **karteziánskému výbuchu** (cartesian explosion), který vzniká při spojování tabulek s více kolekcemi.  

---

#### **2. Problém karteziánského výbuchu**  

- **Příklad**:  
  - Entita `Student` má kolekci `Kurzy` a `Knihy`.  
  - Dotaz s `Include(k => k.Kurzy).Include(k => k.Knihy)` generuje jeden `JOIN`, který vrací duplicitní data.  
  - Výsledek: Počet řádků = počet kurzů × počet knih pro každého studenta.  

**Řešení**: Rozdělení dotazu na tři samostatné dotazy:  
1. Načtení studentů.  
2. Načtení kurzů pro studenty.  
3. Načtení knih pro studenty.  

---

#### **3. Jak rozdělené dotazy fungují?**  

- **Krok 1**: Hlavní dotaz načte základní entity (např. `Studenti`).  
- **Krok 2**: Samostatné dotazy načtou každou související kolekci nebo entitu.  
- **Výsledek**: Méně duplicitních dat, ale více dotazů do databáze.  

**Příklad kódu**:  
```csharp  
var studenti = context.Studenti  
    .Include(s => s.Kurzy)  
    .Include(s => s.Knihy)  
    .AsSplitQuery() // Aktivace rozdělených dotazů  
    .ToList();  
```  

---

#### **4. Výhody rozdělených dotazů**  

- **Menší objem dat**: Eliminuje duplicity způsobené `JOIN`.  
- **Optimalizace výkonu**: Vhodné pro načítání entit s **více kolekcemi**.  
- **Kompatibilita**: Podporováno všemi hlavními databázemi (SQL Server, PostgreSQL, SQLite).  

---

#### **5. Nevýhody rozdělených dotazů**  

- **Více dotazů**: Zvyšuje počet komunikací s databází (tzv. **round-trips**).  
- **Konzistence dat**: Pokud se data změní mezi dotazy, může být výsledek nekonzistentní.  
- **Složitější ladění**: Více dotazů může komplikovat analýzu logů.  

---

#### **6. Kdy použít rozdělené dotazy?**  

- **Scénáře**:  
  - Entita má **více kolekcí** (např. `Kurzy`, `Knihy`, `Hodnocení`).  
  - Hlavní dotaz vrací velké množství řádků kvůli `JOIN`.  
  - Databáze má problémy s výkonem u složitých `JOIN`.  

**Doporučení**: Testujte výkon s `AsSplitQuery()` a bez něj!  

---

#### **7. Globální konfigurace**  

Můžete nastavit rozdělené dotazy jako výchozí chování pro celý kontext:  
```csharp  
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)  
{  
    optionsBuilder.UseSqlServer(connectionString,  
        options => options.UseQuerySplittingBehavior(QuerySplittingBehavior.SplitQuery));  
}  
```  

---

#### **8. Omezení**  

- **Ruční správa transakcí**: Více dotazů může vyžadovat explicitní transakce pro konzistenci.  
- **Komplexní filtrace**: Podmínky napříč více kolekcemi mohou být složité (např. `WHERE` u `Kurzy` a `Knihy`).  

---

#### **9. Srovnání s výchozím chováním (Single Query)**  

| **Parametr**         | **Single Query**                  | **Split Query**                  |  
|-----------------------|-----------------------------------|-----------------------------------|  
| **Počet dotazů**      | 1                                 | Více (podle kolekcí)             |  
| **Duplicity dat**     | Vysoké (karteziánský výbuch)      | Žádné                             |  
| **Vhodné pro**        | Entity s 1–2 kolekcemi           | Entity s více kolekcemi          |  

---

**Shrnutí**:  
- Rozdělené dotazy řeší problém karteziánského výbuchu **dělením dotazů**.  
- **Výhody**: Méně duplicit, lepší výkon u komplexních entit.  
- **Nevýhody**: Více dotazů, riziko nekonzistence.  

**Zdroje**:  
- [EF Core Dokumentace – Rozdělené dotazy](https://learn.microsoft.com/en-us/ef/core/querying/single-split-queries)  
- [EF Core – Karteziánský výbuch](https://learn.microsoft.com/en-us/ef/core/querying/single-split-queries#cartesian-explosion)
