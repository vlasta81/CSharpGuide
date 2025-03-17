
### Instalace Entity Framework Core

---

#### **Předpoklady**

1. **.NET SDK**: Nainstalovaná verze .NET 6.0 nebo novější ([stáhnout zde](https://dotnet.microsoft.com/download)).
2. **IDE/Editor**: Visual Studio, Visual Studio Code, JetBrains Rider, nebo jiný editor s podporou .NET.
3. **Databáze**: SQL Server, SQLite, PostgreSQL, MySQL nebo jiná podporovaná databáze (dle potřeby).

---

#### **Kroky instalace**

1. **Vytvoření projektu**  
   - Vytvořte nový .NET projekt (Console, Web API, MVC apod.):  
     ```bash
     dotnet new console -n MyEfProject
     ```

2. **Přidání EF Core balíčků přes NuGet**  
   - **Základní balíček**:  
     ```bash
     dotnet add package Microsoft.EntityFrameworkCore
     ```
   - **Databázový provider** (např. pro SQL Server):  
     ```bash
     dotnet add package Microsoft.EntityFrameworkCore.SqlServer
     ```
   - **Nástroje pro migrace** (pro CLI):  
     ```bash
     dotnet add package Microsoft.EntityFrameworkCore.Design
     ```

3. **Instalace EF Core Tools (pro migrace)**  
   - Globální instalace nástrojů pro práci s migracemi:  
     ```bash
     dotnet tool install --global dotnet-ef
     ```

---

#### **Konfigurace projektu**

1. **Vytvoření DbContext třídy**  
   - Příklad `AppDbContext.cs`:  
     ```csharp
     using Microsoft.EntityFrameworkCore;

     public class AppDbContext : DbContext
     {
         public DbSet<Student> Students { get; set; }

         protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
         {
             optionsBuilder.UseSqlServer("Server=localhost;Database=MyDatabase;Trusted_Connection=True;");
         }
     }
     ```

2. **Nastavení connection stringu**  
   - Upravte řetězec připojení v metodě `OnConfiguring` podle vaší databáze (např. pro SQLite: `UseSqlite("Data Source=mydatabase.db")`).

---

#### **Ověření instalace**

1. **Vytvořte migraci**  
   - Spusťte v příkazovém řádku (v adresáři projektu):  
     ```bash
     dotnet ef migrations add InitialCreate
     ```
   - Pokud se vytvoří složka `Migrations`, instalace je úspěšná.

2. **Aplikujte migraci na databázi**  
   ```bash
   dotnet ef database update
   ```

---

#### **Časté problémy a řešení**

- **Chyba „No project was found“**:  
  Spusťte příkazy v adresáři projektu (kde je `.csproj` soubor).
- **Chyba „Missing provider“**:  
  Ověřte, zda je nainstalován správný databázový balíček (např. `Microsoft.EntityFrameworkCore.SqlServer`).
- **Chyba připojení k databázi**:  
  Zkontrolujte **connection string** a dostupnost databázového serveru.

---

#### **Volitelné nástroje**

- **Visual Studio NuGet GUI**: Správa balíčků přes GUI (klikněte pravým na projekt → **Manage NuGet Packages**).  
- **Scaffolding (pro Database-First)**:  
  Generování kódu z existující databáze:  
  ```bash
  dotnet ef dbcontext scaffold "Server=..." Microsoft.EntityFrameworkCore.SqlServer
  ```

---

#### **Doporučené zdroje**

1. **Oficiální dokumentace**: [EF Core Installation](https://learn.microsoft.com/en-us/ef/core/get-started/overview/install)
2. **Seznam providerů**: [EF Core Database Providers](https://learn.microsoft.com/en-us/ef/core/providers/)
3. **Tutoriál pro začátečníky**: [EF Core Code-First](https://learn.microsoft.com/en-us/ef/core/get-started/overview/first-app)
