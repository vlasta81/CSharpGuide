
### Načítání souvisejících dat v Entity Framework Core

---

#### **1. Včasné načítání (Eager Loading)**  

- **Definice**: Související data jsou načtena **ihned s hlavní entitou** v jednom dotazu.  
- **Použití**:  
  - Pomocí `Include()` a `ThenInclude()`.  
  - Ideální pro předvídatelné načítání vztahů.  

**Příklad**:  
```csharp  
var studenti = context.Studenti  
    .Include(s => s.Kurzy)          // Načte kolekci kurzů  
    .ThenInclude(k => k.Ucitel)     // Načte učitele pro každý kurz  
    .ToList();  
```  

**Výhody**:  
- Jediný dotaz do databáze.  
- Vhodné pro menší množství souvisejících dat.  

**Nevýhody**:  
- Může generovat složité SQL s `JOIN`.  

---

#### **2. Explicitní načítání (Explicitní Loading)**  

- **Definice**: Související data jsou načtena **dodatečně**, až když je potřeba.  
- **Použití**:  
  - Metody `Collection()` (pro kolekce) a `Reference()` (pro jednotlivé vztahy).  
  - Načítá se pomocí `Load()` nebo dotazováním.  

**Příklad**:  
```csharp  
var student = context.Studenti.First();  
context.Entry(student)  
    .Collection(s => s.Kurzy)  
    .Load();  

// Nebo s filtrací  
context.Entry(student)  
    .Collection(s => s.Kurzy)  
    .Query()  
    .Where(k => k.Rok == 2023)  
    .Load();  
```  

**Výhody**:  
- Flexibilita (lze filtrovat nebo řadit).  
- Šetří paměť, pokud nejsou všechna data potřeba.  

**Nevýhody**:  
- Více dotazů do databáze.  

---

#### **3. Opožděné načítání (Lazy Loading)**  

- **Definice**: Související data jsou načtena **automaticky při přístupu** k navigační vlastnosti.  
- **Nastavení**:  
  - Vyžaduje balíček `Microsoft.EntityFrameworkCore.Proxies`.  
  - V konfiguraci DbContextu:  
    ```csharp  
    optionsBuilder.UseLazyLoadingProxies();  
    ```  

**Příklad**:  
```csharp  
var student = context.Studenti.First();  
var kurzy = student.Kurzy; // Data se načtou až zde  
```  

**Výhody**:  
- Jednoduché použití (žádné explicitní `Include`).  

**Nevýhody**:  
- Riziko **N+1 problému** (mnoho dotazů pro každou entitu).  
- Vyžaduje virtuální navigační vlastnosti:  
  ```csharp  
  public virtual ICollection<Kurz> Kurzy { get; set; }  
  ```  

---

#### **4. Porovnání strategií**  

| **Parametr**          | **Včasné načítání**       | **Explicitní načítání** | **Opožděné načítání** |  
|-----------------------|---------------------------|--------------------------|------------------------|  
| **Počet dotazů**      | 1 (s JOIN)               | Více                    | Více (N+1)            |  
| **Kontrola**          | Předvídatelná            | Ruční                   | Automatická            |  
| **Vhodné pro**        | Malé datasety           | Částečné načítání      | Rychlý vývoj           |  

---

#### **5. N+1 problém u opožděného načítání**  

- **Příklad**:  
  ```csharp  
  foreach (var student in context.Studenti)  
  {  
      // Pro každého studenta se provede dotaz pro jeho kurzy  
      var kurzy = student.Kurzy.ToList();  
  }  
  ```  
- **Řešení**: Použijte včasné nebo explicitní načítání.  

---

#### **6. Doporučené postupy**  

1. **Včasné načítání**: Pro předvídatelné vztahy a menší data.  
2. **Explicitní načítání**: Pro optimalizaci výkonu u velkých datasetů.  
3. **Opožděné načítání**: Používejte opatrně, hlavně v testovacím prostředí.  

---

**Shrnutí**:  
- **Včasné načítání**: Efektivní pro malé datasety s `Include()`.  
- **Explicitní načítání**: Kontrolované načítání pomocí `Load()`.  
- **Opožděné načítání**: Automatické, ale rizikové kvůli N+1.  

**Zdroje**:  
- [EF Core Dokumentace – Načítání dat](https://learn.microsoft.com/en-us/ef/core/querying/related-data)  
- [EF Core – Lazy Loading](https://learn.microsoft.com/en-us/ef/core/querying/related-data/lazy)
