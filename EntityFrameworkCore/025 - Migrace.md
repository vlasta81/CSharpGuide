
### Migrace v Entity Framework Core

---

#### **1. Co jsou migrace?**  

Migrace jsou nástroj pro **postupné aktualizace databázového schématu** tak, aby odpovídalo změnám v modelu entit. Umožňují:  
- Vytvářet, upravovat nebo mazat tabulky a sloupce.  
- Spravovat vztahy mezi entitami.  
- Aplikovat změny bez nutnosti ručního psaní SQL skriptů.  

---

#### **2. Základní příkazy pro práci s migracemi**  

1. **Vytvoření migrace**:  
   ```bash  
   dotnet ef migrations add <NázevMigrace>  
   ```  
   *Výstup*: Generuje složku `Migrations` s kódem pro aplikování změn (metoda `Up`) a jejich vrácení (metoda `Down`).  

2. **Aplikování migrace**:  
   ```bash  
   dotnet ef database update  
   ```  
   - Aktualizuje databázi na nejnovější migraci.  
   - Lze specifikovat konkrétní verzi:  
     ```bash  
     dotnet ef database update <NázevMigrace>  
     ```  

3. **Vrácení migrace**:  
   ```bash  
   dotnet ef database update <NázevPředchozíMigrace>  
   ```  
   - Nebo úplné vrácení všech migrací:  
     ```bash  
     dotnet ef database update 0  
     ```  

4. **Odstranění poslední migrace** (pokud nebyla aplikována):  
   ```bash  
   dotnet ef migrations remove  
   ```  

---

#### **3. Struktura migračního souboru**  

- **Metoda `Up`**: Obsahuje SQL příkazy pro provedení změn (např. `CREATE TABLE`, `ALTER COLUMN`).  
- **Metoda `Down`**: Obsahuje SQL příkazy pro vrácení změn (např. `DROP TABLE`, `DROP COLUMN`).  
- **Metadata**: Informace o závislostech a verzi migrace.  

**Příklad**:  
```csharp  
public partial class InitialCreate : Migration  
{  
    protected override void Up(MigrationBuilder migrationBuilder)  
    {  
        migrationBuilder.CreateTable(/* ... */);  
    }  

    protected override void Down(MigrationBuilder migrationBuilder)  
    {  
        migrationBuilder.DropTable(/* ... */);  
    }  
}  
```  

---

#### **4. Tabulka migrační historie (`__EFMigrationsHistory`)**  

- Automaticky vytvořená tabulka v databázi.  
- Sleduje, které migrace již byly aplikovány.  
- **Obsahuje sloupce**:  
  - `MigrationId`: Název migrace a časové razítko.  
  - `ProductVersion`: Verze EF Core.  

---

#### **5. Vlastní SQL v migracích** 

Pokud automaticky generovaný kód nestačí, lze přidat vlastní SQL příkazy:  
```csharp  
protected override void Up(MigrationBuilder migrationBuilder)  
{  
    migrationBuilder.Sql("ALTER TABLE Studenti ADD CONSTRAINT CHK_Vek CHECK (Vek >= 18)");  
}  
```  

---

#### **6. Řešení konfliktů a koordinace v týmu**  

- **Problém**: Dva vývojáři současně vytvoří migrace s různými změnami.  
- **Řešení**:  
  - Sloučit migrační soubory ručně.  
  - Použít `dotnet ef migrations script` pro generování SQL skriptu a manuální kontrolu.  

---

#### **7. Doporučené postupy**  

1. **Pravidelně commitovat migrační soubory** do verzovacího systému (Git).  
2. **Pojmenovávat migrace popisně** (např. `AddEmailToStudent`).  
3. **Testovat migrace** v lokálním a testovacím prostředí před nasazením do produkce.  
4. **Nepřejmenovávat migrace** po jejich vytvoření – může způsobit nekonzistenci historie.  

---

#### **8. Časté problémy**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|---------------------------------------------|  
| Chybějící migrace v Gitu             | Obnovte migrace z repozitáře a znovu aplikujte. |  
| Neplatný stav databáze               | Použijte `dotnet ef database update --force`. |  
| Konflikty migrací v týmu             | Koordinujte se a slučujte změny ručně.       |  

---

**Shrnutí**:  
- Migrace umožňují **spravovat změny v databázovém schématu** v souladu s modelem entit.  
- Klíčové příkazy: `add`, `update`, `remove`.  
- Důležitá je **koordinace v týmu** a správná správa migračních souborů.  

**Zdroje**:  
- [EF Core Dokumentace – Migrace](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/)  
- [EF Core Migrations CLI](https://learn.microsoft.com/en-us/ef/core/cli/dotnet#migration-tools)
