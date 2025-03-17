
### DbContext v Entity Framework Core 

Kontext databáze, sdružování kontextů, multi-tenancy

---

#### **1. Co je DbContext?**  

- **Hlavní třída** pro komunikaci s databází v EF Core.  
- **Funkce**:  
  - Spravuje **připojení** k databázi.  
  - Poskytuje **DbSet<T>** pro přístup k entitám.  
  - Sleduje změny (**change tracking**) pro aktualizace.  
  - Zprostředkovává **transakce** a **dotazy**.  

- **Příklad základního DbContextu**:  
  ```csharp
  public class AppDbContext : DbContext
  {
      public DbSet<Student> Students { get; set; }
      
      protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
      {
          optionsBuilder.UseSqlServer("Server=...;Database=SchoolDB;...");
      }
  }
  ```

---

#### **2. Životní cyklus DbContextu**  

- **Doporučené použití**:  
  - **Scoped lifecycle** (v ASP.NET Core): Jeden kontext na HTTP požadavek.  
  - **Dispose**: Vždy zavolejte `Dispose()` nebo použijte `using` blok.  
- **Problémy při nesprávném použití**:  
  - Memory leaks při nedostatečném uvolňování.  
  - Konflikty při sdílení kontextu mezi vlákny.  

---

#### **3. Sdružování kontextů (DbContext Pooling)**  

- **Cíl**: Zvýšení výkonu pomocí **opakovaného použití instancí DbContextu**.  
- **Konfigurace v ASP.NET Core**:  
  ```csharp
  services.AddDbContextPool<AppDbContext>(options =>
  {
      options.UseSqlServer("...");
  }, poolSize: 128); // Výchozí velikost poolu = 128
  ```  
- **Kdy použít**:  
  - Aplikace s vysokou zátěží (např. webové API).  
- **Omezení**:  
  - Neukládejte stav v kontextu mezi požadavky (poolované instance se sdílí!).  

---

#### **4. Multi-Tenancy (Více tenantů)**  

- **Definice**: Aplikace slouží více nezávislým klientům (tenantům) s oddělenými daty.  
- **Strategie konfigurace DbContextu pro tenanty**:  

1. **Databáze na tenanta**:  
   - Každý tenant má vlastní databázi.  
   - **Příklad**: Dynamický connection string podle tenanta.  
     ```csharp
     public class TenantDbContext : DbContext
     {
         private readonly string _tenantConnectionString;

         public TenantDbContext(string tenantConnectionString)
         {
             _tenantConnectionString = tenantConnectionString;
         }

         protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
         {
             optionsBuilder.UseSqlServer(_tenantConnectionString);
         }
     }
     ```  

2. **Shared Database s Tenant ID**:  
   - Všechny tenanty v jedné databázi, oddělené sloupcem (např. `TenantId`).  
   - **Příklad**: Automatický filtr dotazů.  
     ```csharp
     protected override void OnModelCreating(ModelBuilder modelBuilder)
     {
         modelBuilder.Entity<Student>().HasQueryFilter(s => s.TenantId == _currentTenantId);
     }
     ```  

---

#### **5. Pokročilé techniky**  

- **Vlastní inicializace DbContextu**:  
  - Přetížení metod `SaveChanges()` nebo `OnModelCreating()`.  
- **Interceptory**:  
  - Zachytávání událostí (např. logování dotazů).  
  ```csharp
  optionsBuilder.AddInterceptors(new QueryCommandInterceptor());
  ```  

---

#### **6. Časté problémy**  

- **Memory leaks**:  
  - **Příčina**: Nestrukturované vytváření DbContextů bez `using` nebo DI.  
  - **Řešení**: Používejte DI kontejner nebo `IDisposable`.  
- **Concurrency conflicts**:  
  - **Příklad**: Dva uživatelé mění stejný záznam.  
  - **Řešení**: Optimistická konkurence (např. `[Timestamp]`).  

---

#### **7. Best Practices**  

- **Pro webové aplikace**:  
  - Vždy používejte **dependency injection** pro DbContext.  
  - Vyhněte se **singletonu** pro DbContext.  
- **Testování**:  
  - Používejte **In-Memory databázi** (EF Core provider) pro unit testy.  
- **Multi-tenancy**:  
  - Pro komplexní řešení zvažte knihovny jako **Finbuckle.MultiTenant**.  

---

**Shrnutí**:  
- DbContext je **srdce EF Core** – spravuje data, transakce a změny.  
- Sdružování kontextů zvyšuje výkon, ale vyžaduje správnou konfiguraci.  
- Multi-tenancy řešte podle potřeb aplikace (oddělené databáze vs. sdílená databáze).  

**Zdroj**: [EF Core Documentation – DbContext](https://learn.microsoft.com/en-us/ef/core/dbcontext-configuration/)
