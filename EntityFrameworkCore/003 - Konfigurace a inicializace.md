
### Konfigurace a inicializace Entity Framework Core

---

#### **1. Základní konfigurace DbContext**

- **DbContext**: Hlavní třída pro komunikaci s databází. Slouží k definici entit, konfiguraci připojení a správě transakcí.
- **Příklad základní konfigurace**:
  ```csharp
  public class AppDbContext : DbContext
  {
      public DbSet<Student> Students { get; set; }

      protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
      {
          // Použití SQL Serveru
          optionsBuilder.UseSqlServer("Server=localhost;Database=SchoolDB;Trusted_Connection=True;");
          
          // Pro SQLite:
          // optionsBuilder.UseSqlite("Data Source=app.db");
      }
  }
  ```

---

#### **2. Konfigurace pomocí Dependency Injection (ASP.NET Core)**

- **Startup.cs / Program.cs**:
  ```csharp
  builder.Services.AddDbContext<AppDbContext>(options =>
  {
      options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"));
  });
  ```
- **Connection string v appsettings.json**:
  ```json
  {
    "ConnectionStrings": {
      "DefaultConnection": "Server=localhost;Database=SchoolDB;Trusted_Connection=True;"
    }
  }
  ```

---

#### **3. Konfigurace entit a vztahů**

- **Metody konfigurace**:
  - **Data Annotations**: Atributy přímo v entitách (např. `[Key]`, `[Required]`).
    ```csharp
    public class Student
    {
        [Key]
        public int Id { get; set; }
        
        [Required]
        public string Name { get; set; }
    }
    ```
  - **Fluent API**: Konfigurace v `OnModelCreating` metodě DbContextu.
    ```csharp
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Student>()
            .HasKey(s => s.Id);
            
        modelBuilder.Entity<Student>()
            .Property(s => s.Name)
            .IsRequired();
            
        // 1:N vztah
        modelBuilder.Entity<Student>()
            .HasOne(s => s.Třída)
            .WithMany(t => t.Studenti)
            .HasForeignKey(s => s.TřídaId);
    }
    ```

---

#### **4. Migrace a inicializace databáze**

1. **Vytvoření migrace**:
   ```bash
   dotnet ef migrations add InitialCreate
   ```
2. **Aplikace migrace**:
   ```bash
   dotnet ef database update
   ```
3. **Automatická aplikace migrací při spuštění aplikace** (např. v ASP.NET Core):
   ```csharp
   using (var scope = app.Services.CreateScope())
   {
       var dbContext = scope.ServiceProvider.GetRequiredService<AppDbContext>();
       dbContext.Database.Migrate();
   }
   ```

---

#### **5. Seeding dat (inicializace testovacích dat)**

- **Metoda `OnModelCreating` nebo samostatná migrace**:
  ```csharp
  protected override void OnModelCreating(ModelBuilder modelBuilder)
  {
      modelBuilder.Entity<Student>().HasData(
          new Student { Id = 1, Name = "Jan Novák" },
          new Student { Id = 2, Name = "Eva Dvořáková" }
      );
  }
  ```
- **Spuštění seedování**:
  ```csharp
  if (context.Students.Count() == 0)
  {
      context.Students.AddRange(/* data */);
      context.SaveChanges();
  }
  ```

---

#### **6. Konfigurace pro různé prostředí (Development/Production)**

- **Použití proměnných prostředí**:
  ```csharp
  optionsBuilder.UseSqlServer(Environment.GetEnvironmentVariable("DB_CONNECTION_STRING"));
  ```
- **Rozdělení konfigurace v ASP.NET Core**:
  - `appsettings.Development.json` vs `appsettings.Production.json`.

---

#### **7. Pokročilá konfigurace**

- **Logování dotazů**:
  ```csharp
  optionsBuilder.UseLoggerFactory(LoggerFactory.Create(builder => builder.AddConsole()));
  ```
- **Vypnutí sledování změn**:
  ```csharp
  optionsBuilder.UseQueryTrackingBehavior(QueryTrackingBehavior.NoTracking);
  ```
- **Nastavení timeoutu**:
  ```csharp
  optionsBuilder.UseSqlServer(connectionString, options => options.CommandTimeout(60));
  ```

---

#### **Časté chyby a řešení**

- **„Invalid column name“**: Zkontrolujte migrace a aktualizujte databázi.
- **„No database provider configured“**: Přidejte databázový balíček (např. `Microsoft.EntityFrameworkCore.SqlServer`).
- **„Multiple cascade paths“**: Upravte chování smazání v konfiguraci vztahu:
  ```csharp
  .OnDelete(DeleteBehavior.Restrict);
  ```

---

#### **Doporučené postupy**

- **Fluent API vs Data Annotations**: Pro komplexní konfiguraci použijte Fluent API.
- **Connection stringy nikdy necommitovat**: Ukládejte je do proměnných prostředí nebo `appsettings.json` (přidejte do `.gitignore`).
- **Používejte migrace**: Neupravujte databázi manuálně.

---

**Zdroj**: [Oficiální dokumentace EF Core](https://learn.microsoft.com/en-us/ef/core/)  
