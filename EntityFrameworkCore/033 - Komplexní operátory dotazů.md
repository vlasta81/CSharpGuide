
### Komplexní operátory dotazů v Entity Framework Core

---

#### **1. Spojení (Joins)**  

**Účel**: Kombinace dat z více tabulek na základě společného klíče.  
**Typy**:  
- **Vnitřní spojení (INNER JOIN)**:  
  ```csharp  
  var dotaz = from s in context.Studenti  
              join k in context.Kurzy on s.KurzId equals k.Id  
              select new { s.Jmeno, k.Nazev };  
  ```  
- **Levé spojení (LEFT JOIN)**:  
  ```csharp  
  var dotaz = from s in context.Studenti  
              join k in context.Kurzy on s.KurzId equals k.Id into skupina  
              from k in skupina.DefaultIfEmpty()  
              select new { s.Jmeno, Kurz = k.Nazev ?? "Nenavštěvuje" };  
  ```  

---

#### **2. Seskupování (Grouping)**  

**Účel**: Agregace dat podle společných hodnot.  
**Příklad**:  
```csharp  
var skupiny = context.Objednavky  
    .GroupBy(o => o.ZakaznikId)  
    .Select(g => new {  
        ZakaznikId = g.Key,  
        Celkem = g.Sum(o => o.Cena)  
    }).ToList();  
```  
**Poznámka**: EF Core podporuje základní agregace (`Sum`, `Average`, `Count`), ale ne všechny funkce SQL.  

---

#### **3. Poddotazy (Subqueries)**  

**Účel**: Vnořené dotazy pro filtrování nebo transformaci dat.  
**Příklad v klauzuli `WHERE`**:  
```csharp  
var studenti = context.Studenti  
    .Where(s => s.Vek > context.Studenti.Average(s => s.Vek))  
    .ToList();  
```  
**Příklad v klauzuli `SELECT`**:  
```csharp  
var dotaz = context.Studenti  
    .Select(s => new {  
        Jmeno = s.Jmeno,  
        PocetKurzu = s.Kurzy.Count()  
    }).ToList();  
```  

---

#### **4. Množinové operace (Set Operations)**  

**Účel**: Kombinace výsledků více dotazů.  
**Typy**:  
- **Sjednocení (UNION)**:  
  ```csharp  
  var dotaz = context.Studenti.Where(s => s.Vek > 18)  
      .Union(context.Studenti.Where(s => s.Mesto == "Praha"));  
  ```  
- **Průnik (INTERSECT)**:  
  ```csharp  
  var dotaz = context.Studenti.Where(s => s.Vek > 20)  
      .Intersect(context.Studenti.Where(s => s.Pohlaví == "Muž"));  
  ```  
- **Rozdíl (EXCEPT)**:  
  ```csharp  
  var dotaz = context.Studenti  
      .Except(context.Studenti.Where(s => s.Mesto == "Brno"));  
  ```  

---

#### **5. Okénkové funkce (Window Functions)**  

**Účel**: Výpočty nad "okny" dat (např. pořadí, kumulativní součty).  
**Příklad (ROW_NUMBER)**:  
```csharp  
var dotaz = context.Studenti  
    .Select(s => new {  
        s.Jmeno,  
        Poradi = EF.Functions.RowNumber(EF.Functions.OrderBy(s.Vek))  
    }).ToList();  
```  
**Podporované funkce**: `RowNumber`, `Rank`, `Sum` over partitions (EF Core 8+).  

---

#### **6. Raw SQL v dotazech**  

**Účel**: Vložení vlastního SQL kódu pro komplexní logiku.  
**Příklad**:  
```csharp  
var studenti = context.Studenti  
    .FromSqlRaw("SELECT * FROM Studenti WHERE Mesto = {0}", "Praha")  
    .ToList();  
```  
**Omezení**: Není plně kompatibilní se všemi LINQ operátory.  

---

#### **7. Podmíněná logika (Case Statements)**  

**Účel**: Dynamické výpočty na základě podmínek.  
**Příklad**:  
```csharp  
var dotaz = context.Studenti  
    .Select(s => new {  
        s.Jmeno,  
        Kategorie = s.Vek >= 18 ? "Dospělý" : "Nezletilý"  
    }).ToList();  
```  

---

#### **8. Výkonnostní tipy**  

- **Používejte `AsNoTracking()`**: Pro read-only operace.  
- **Vyhněte se N+1 problému**: Používejte `Include` nebo explicitní načítání.  
- **Optimalizujte SQL**: Analýzou generovaného SQL (např. pomocí `ToQueryString()`).  

---

#### **9. Shrnutí operátorů**  

| **Operátor**         | **Použití**                                 | **Příklad**                          |  
|-----------------------|--------------------------------------------|--------------------------------------|  
| **JOIN**              | Spojení tabulek                            | `Join`, `GroupJoin`                  |  
| **GROUP BY**          | Agregace dat                               | `GroupBy`, `Sum`                     |  
| **Subquery**          | Vnořené dotazy                             | `Where(x => x > prumer)`             |  
| **UNION/INTERSECT**   | Kombinace výsledků                         | `Union`, `Intersect`                 |  
| **Window Functions**  | Výpočty nad částmi dat                     | `RowNumber`, `Rank`                  |  

---

**Zdroje**:  
- [EF Core Dokumentace – Pokročilé dotazy](https://learn.microsoft.com/en-us/ef/core/querying/)  
- [EF Core – Window Functions](https://learn.microsoft.com/en-us/ef/core/what-is-new/ef-core-8.0/whatsnew#window-functions)  
