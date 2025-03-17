
### Entity Framework Core (EF Core)

#### **Základní informace**

- **ORM framework**: Nástroj od Microsoftu pro mapování objektů na relační databáze v .NET aplikacích.
- **Účel**: Zjednodušuje práci s databází pomocí mapování .NET objektů na databázové tabulky.
- **Cross-platform**: Funguje s .NET Core, .NET 5+, Xamarin atd.
- **Open Source**: Součást .NET Foundation.

---

#### **Klíčové vlastnosti**

1. **Podpora LINQ**: Dotazování přes LINQ (Language Integrated Query).
2. **Databázové providery**: Podpora SQL Server, SQLite, PostgreSQL, MySQL, Azure Cosmos DB a dalších přes pluginy.
3. **Migrace**: Správa změn v databázovém schématu (např. `Add-Migration`, `Update-Database`).
4. **Sledování změn**: Automatické sledování změn entit pro efektivní aktualizace.
5. **Vztahy**: Podpora 1:1, 1:N a M:N vztahů (od EF Core 5+ i M:N bez join entity).
6. **Raw SQL**: Spouštění SQL dotazů nebo uložených procedur.
7. **Správa konkurence**: Optimistická konkurence pomocí tokenů (např. `[Timestamp]`).

---

#### **Architektura**

1. **DbContext**:
   - Hlavní třída pro komunikaci s databází.
   - Spravuje připojení, transakce a změny.
   - Příklad:
     ```csharp
     public class AppDbContext : DbContext { ... }
     ```
2. **DbSet<T>**:
   - Reprezentuje kolekci entit (např. `DbSet<Student> Students`).
3. **Entity**:
   - Běžné C# třídy mapované na tabulky (POCOs).
   - Konfigurace přes data annotations nebo Fluent API.

---

#### **Přístupy k databázi**

1. **Code-First**:
   - Definujte entity v kódu → generování databázového schématu.
   - Migrace pro postupnou aktualizaci databáze.
2. **Database-First**:
   - Generování entit z existující databáze (scaffolding).
   - Příkaz: `Scaffold-DbContext`.

---

#### **CRUD operace**

- **Vytvoření (Create)**:
  ```csharp
  context.Add(newStudent);
  context.SaveChanges();
  ```
- **Čtení (Read)**:
  ```csharp
  var student = context.Students.FirstOrDefault(s => s.Id == 1);
  ```
- **Aktualizace (Update)**:
  ```csharp
  student.Name = "Nové jméno";
  context.SaveChanges();
  ```
- **Smazání (Delete)**:
  ```csharp
  context.Remove(student);
  context.SaveChanges();
  ```

---

#### **Migrace**

- Sledování změn schématu v kódu:
  ```bash
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```
- **Nástroje**: Vyžaduje NuGet balíček `Microsoft.EntityFrameworkCore.Tools`.

---

#### **Vztahy mezi entitami**

- Konfigurace přes **Fluent API** nebo **data annotations**:
  ```csharp
  modelBuilder.Entity<Autor>()
      .HasMany(a => a.Knihy)
      .WithOne(k => k.Autor);
  ```
- Typy vztahů:
  - **1:N**: `HasMany()` / `WithOne()`.
  - **M:N**: Od EF Core 5+ podpora bez nutnosti join entity.

---

#### **Dotazování dat**

1. **LINQ**:
   ```csharp
   var students = context.Students
       .Where(s => s.Vek > 18)
       .ToList();
   ```
2. **Raw SQL**:
   ```csharp
   var students = context.Students
       .FromSqlRaw("SELECT * FROM Students WHERE Vek > 18")
       .ToList();
   ```

---

#### **Tipy pro výkon**

- **AsNoTracking()**: Vypne sledování změn pro dotazy pouze ke čtení.
- **Eager Loading**: Načte související data přes `.Include()`.
- **Explicit Loading**: Načte související data později přes `.Load()`.

---

#### **Výhody & Nevýhody**

| **Výhody**                      | **Nevýhody**                     |
|----------------------------------|-----------------------------------|
| Rychlý vývoj                    | Výkonnostní režie u složitých dotazů |
| Nezávislost na databázi         | Náročnější naučení pokročilých funkcí |
| Automatická správa schématu     | Méně funkcí oproti EF6           |

---

#### **Zdroje**

1. **Oficiální dokumentace**: [Microsoft EF Core Documentation](https://learn.microsoft.com/en-us/ef/core/)
2. **Tutoriály**: EF Core Začínáme, Code-First/Migrace.
3. **GitHub**: [github.com/dotnet/efcore](https://github.com/dotnet/efcore)
