
### Víceklientská architektura (Multi-Tenancy) v Entity Framework Core

---

#### **1. Co je víceklientská architektura?**  

- **Definice**: Aplikace slouží více nezávislým klientům (**tenantům**), kteří sdílejí stejnou instanci aplikace, ale mají **oddělená data**.  
- **Cíl**: Izolace dat a konfigurace mezi klienty při zachování sdílené infrastruktury.  
- **Příklady**: SaaS aplikace (např. CRM, účetní systémy).  

---

#### **2. Strategie implementace**  

| **Strategie**               | **Popis**                                                                 | **Výhody**                          | **Nevýhody**                          |  
|-----------------------------|---------------------------------------------------------------------------|-------------------------------------|----------------------------------------|  
| **Databáze na tenanta**      | Každý tenant má vlastní databázi.                                        | Vysoká izolace, snadné zálohování.  | Vysoká náročnost na správu.            |  
| **Sdílená databáze + TenantId** | Všechna data jsou v jedné databázi, oddělena sloupcem `TenantId`.       | Nízké náklady, jednoduchá správa.   | Riziko nechtěného přístupu mezi tenanty. |  
| **Schema na tenanta**        | Každý tenant má vlastní schéma v rámci jedné databáze.                   | Izolace na úrovni schématu.         | Omezená podpora u některých databází.  |  

---

#### **3. Implementace v EF Core**  

**a) Sdílená databáze s TenantId**  
1. **Přidání TenantId do entit**:  
   ```csharp  
   public class Order  
   {  
       public int Id { get; set; }  
       public string TenantId { get; set; } // Identifikátor tenanta  
       public decimal Amount { get; set; }  
   }  
   ```  

2. **Globální filtry pro TenantId**:  
   ```csharp  
   protected override void OnModelCreating(ModelBuilder modelBuilder)  
   {  
       modelBuilder.Entity<Order>()  
           .HasQueryFilter(o => o.TenantId == _currentTenantId);  
   }  
   ```  

3. **Nastavení TenantId v DbContextu**:  
   ```csharp  
   public class AppDbContext : DbContext  
   {  
       private readonly string _currentTenantId;  

       public AppDbContext(string tenantId)  
       {  
           _currentTenantId = tenantId;  
       }  
   }  
   ```  

**b) Databáze na tenanta**  
1. **Dynamický výběr connection stringu**:  
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

2. **Migrace pro každého tenanta**:  
   ```bash  
   dotnet ef migrations add InitialCreate --context TenantDbContext --output-dir Migrations/TenantA  
   dotnet ef database update --context TenantDbContext  
   ```  

---

#### **4. Řešení běžných problémů**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|---------------------------------------------|  
| **Izolace dat**                      | Vždy používejte globální filtry (`HasQueryFilter`). |  
| **Výkon u velkého počtu tenantů**    | Optimalizujte indexy a cachování.           |  
| **Migrace pro mnoho databází**       | Automatizujte pomocí skriptů nebo nástrojů. |  

---

#### **5. Integrace s middlewarem**  

- **Identifikace tenanta**: Např. z URL, JWT tokenu nebo cookies.  
- **Příklad middleware pro ASP.NET Core**:  
  ```csharp  
  public class TenantMiddleware  
  {  
      private readonly RequestDelegate _next;  

      public TenantMiddleware(RequestDelegate next)  
      {  
          _next = next;  
      }  

      public async Task InvokeAsync(HttpContext context, AppDbContext dbContext)  
      {  
          var tenantId = context.User.FindFirst("TenantId")?.Value;  
          dbContext.SetTenantId(tenantId);  
          await _next(context);  
      }  
  }  
  ```  

---

#### **6. Best Practices**  

1. **Automatické filtrování**: Vždy aplikujte `TenantId` ve filtrech.  
2. **Zabezpečení**: Ověřujte práva tenanta při přístupu k datům.  
3. **Testování**: Otestujte scénáře s více tenanty (např. cross-tenant data leaks).  
4. **Používejte shadow properties**: Ukládejte `TenantId` jako stínovou vlastnost, pokud nechcete měnit entity.  
   ```csharp  
   modelBuilder.Entity<Order>().Property<string>("TenantId");  
   ```  

---

#### **7. Nástroje a knihovny**  

- **Finbuckle.MultiTenant**: Knihovna pro pokročilou správu tenantů v .NET.  
- **EF Core Interceptory**: Pro automatické přidávání `TenantId` do dotazů.  

---

**Shrnutí**:  
- **Databáze na tenanta**: Maximální izolace, ale náročná správa.  
- **Sdílená databáze**: Efektivní pro malé až střední projekty.  
- **Globální filtry**: Klíčové pro zabránění přístupu k cizím datům.  

**Zdroje**:  
- [EF Core – Query Filters](https://learn.microsoft.com/en-us/ef/core/querying/filters)  
- [Finbuckle.MultiTenant Documentation](https://www.finbuckle.com/multitenant)  
- [Multi-Tenant Best Practices](https://learn.microsoft.com/en-us/azure/architecture/guide/multitenant/)
