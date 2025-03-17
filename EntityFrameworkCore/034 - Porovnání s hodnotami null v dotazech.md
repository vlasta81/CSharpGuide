
### Porovnání s hodnotami `null` v dotazech v Entity Framework Core

---

#### **1. Základní porovnání s `null`**  

V EF Core lze porovnávat vlastnosti s `null` pomocí standardních operátorů `==` a `!=`.  
- **Příklad**:  
  ```csharp  
  // Najde studenty bez uvedeného věku  
  var studentiBezVeku = context.Studenti
      .Where(s => s.Vek == null)
      .ToList();

  // Najde studenty s uvedeným věkem  
  var studentiSVekem = context.Studenti
      .Where(s => s.Vek != null)
      .ToList();
  ```  
  **SQL ekvivalent**:  
  ```sql  
  -- s.Vek == null  
  SELECT * FROM Studenti WHERE Vek IS NULL;  

  -- s.Vek != null  
  SELECT * FROM Studenti WHERE Vek IS NOT NULL;  
  ```  

---

#### **2. Operátor `??` (null-coalescing)**  

Používá se pro definování výchozí hodnoty, pokud je vlastnost `null`.  
- **Příklad**:  
  ```csharp  
  var dotaz = context.Studenti
      .Select(s => new { 
          Vek = s.Vek ?? 0 // Pokud je Vek null, použije se 0
      }).ToList();
  ```  
  **SQL ekvivalent**:  
  ```sql  
  SELECT COALESCE(Vek, 0) AS Vek FROM Studenti;  
  ```  

---

#### **3. Porovnání navigačních vlastností**  

Pokud navigační vlastnost (např. cizí klíč) může být `null`, EF Core automaticky generuje `LEFT JOIN`.  
- **Příklad**:  
  ```csharp  
  var studentiSKurzem = context.Studenti
      .Where(s => s.Kurz != null) // Kurz je navigační vlastnost
      .ToList();
  ```  
  **SQL ekvivalent**:  
  ```sql  
  SELECT * FROM Studenti s 
  LEFT JOIN Kurzy k ON s.KurzId = k.Id 
  WHERE k.Id IS NOT NULL;  
  ```  

---

#### **4. Metody pro práci s `null`**  

- **`HasValue` pro nullable typy**:  
  ```csharp  
  var studentiSVekem = context.Studenti
      .Where(s => s.Vek.HasValue)
      .ToList();
  ```  
  **Poznámka**: Funguje pouze pro nullable typy (např. `int?`, `DateTime?`).  

- **`Value` pro přístup k hodnotě**:  
  ```csharp  
  var dospeli = context.Studenti
      .Where(s => s.Vek.HasValue && s.Vek.Value >= 18)
      .ToList();
  ```  

---

#### **5. Funkce pro práci s `null`**  

- **`DbFunctions.IsNullOrEmpty`**:  
  ```csharp  
  var studentiBezJmena = context.Studenti
      .Where(s => string.IsNullOrEmpty(s.Jmeno))
      .ToList();
  ```  
  **SQL ekvivalent**:  
  ```sql  
  SELECT * FROM Studenti WHERE Jmeno IS NULL OR Jmeno = '';  
  ```  

- **`EF.Functions` pro specifické databázové funkce**:  
  ```csharp  
  var studentiSNeznamymMestem = context.Studenti
      .Where(s => EF.Functions.Collate(s.Mesto, "SQL_Latin1_General_CP1_CI_AS") == null)
      .ToList();
  ```  

---

#### **6. Časté problémy a řešení**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|---------------------------------------------|  
| **Porovnání `== null` vs. `Equals(null)`** | Vždy používejte `== null` (EF Core ho přeloží na `IS NULL`). |  
| **Neočekávané `null` u LEFT JOIN**   | Ošetřete `null` pomocí `??` nebo podmínek. |  
| **Chybějící převod na SQL**          | Vyhněte se komplexním výrazům s `null` mimo LINQ. |  

---

#### **7. Výkonnostní tipy**  

- **Indexy na nullable sloupcích**: Umožňují rychlejší vyhledávání `IS NULL`/`IS NOT NULL`.  
- **Vyhněte se `COALESCE` v `WHERE`**: Může zabránit použití indexů.  
- **Optimalizace dotazů**: Analyzujte generované SQL pomocí `ToQueryString()`.  

---

#### **8. Shrnutí operátorů a jejich SQL ekvivalentů**  

| **C# výraz**               | **SQL ekvivalent**              |  
|----------------------------|----------------------------------|  
| `s.Vek == null`            | `Vek IS NULL`                   |  
| `s.Vek != null`            | `Vek IS NOT NULL`               |  
| `s.Vek ?? 0`               | `COALESCE(Vek, 0)`              |  
| `s.Kurz != null`           | `KurzId IS NOT NULL` (LEFT JOIN)|  

---

**Zdroje**:  
- [EF Core Dokumentace – Práce s `null`](https://learn.microsoft.com/en-us/ef/core/querying/null-comparisons)  
- [EF Core – Translace operátorů do SQL](https://learn.microsoft.com/en-us/ef/core/providers/sql-server/functions)  
