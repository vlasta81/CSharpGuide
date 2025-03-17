
### Připojovací řetězec (Connection String) v Entity Framework Core

---

#### **Co je připojovací řetězec?**  

- **Definice**: Textový řetězec obsahující informace potřebné pro připojení k databázi (server, název databáze, autentizace atd.).  
- **Účel**: EF Core jej používá k navázání komunikace s databázovým serverem.  

---

#### **Struktura připojovacího řetězce** 

Základní prvky (pro SQL Server):  
```text
Server=[název_serveru];  
Database=[název_databáze];  
User Id=[uživatelské_jméno];  
Password=[heslo];  
Trusted_Connection=[true/false];  
Encrypt=[true/false];  
```  
- **Příklady klíčových parametrů**:  
  - `Server`: Adresa serveru (např. `localhost`, `mydbserver.database.windows.net`).  
  - `Database`: Název databáze.  
  - `Trusted_Connection`: Použití Windows autentizace (`true`/`false`).  
  - `Encrypt`: Šifrování spojení (vyžadováno např. u Azure SQL).  

---

#### **Příklady pro různé databáze**  

1. **SQL Server**:  
   ```text
   Server=localhost;Database=SchoolDB;Trusted_Connection=True;  
   ```  
   nebo s SQL autentizací:  
   ```text
   Server=localhost;Database=SchoolDB;User Id=sa;Password=Heslo123;  
   ```  

2. **SQLite**:  
   ```text
   Data Source=app.db;  
   ```  

3. **PostgreSQL**:  
   ```text
   Host=localhost;Port=5432;Database=SchoolDB;Username=postgres;Password=Heslo123;  
   ```  

4. **MySQL**:  
   ```text
   Server=localhost;Database=SchoolDB;User=root;Password=Heslo123;  
   ```  

---

#### **Kam ukládat připojovací řetězec?**  

1. **ASP.NET Core**:  
   - V konfiguračním souboru `appsettings.json`:  
     ```json
     {
       "ConnectionStrings": {
         "DefaultConnection": "Server=localhost;Database=SchoolDB;Trusted_Connection=True;"
       }
     }
     ```  
   - Načtení v kódu:  
     ```csharp
     var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");  
     ```  

2. **Konzolové aplikace**:  
   - Použijte `ConfigurationBuilder` nebo proměnné prostředí (**doporučeno pro bezpečnost**).  

3. **Vývoj vs. Produkce**:  
   - **Vývoj**: Ukládejte do `appsettings.Development.json`.  
   - **Produkce**: Používejte proměnné prostředí (např. na Azure: `Application Settings`).  

---

#### **Bezpečnostní doporučení**  

- **Nikdy neukládejte hesla v kódu** nebo ve veřejných repozitářích!  
- **Citlivá data**:  
  - Používejte nástroj **Secret Manager** pro lokální vývoj:  
    ```bash
    dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Server=..."
    ```  
  - V produkci: Azure Key Vault, AWS Secrets Manager, nebo proměnné prostředí.  
- **Šifrování**: Aktivujte `Encrypt=True` pro cloudové databáze (např. Azure SQL).  

---

#### **Konfigurace v DbContextu**  

- **Příklad pro SQL Server**:  
  ```csharp
  protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
  {
      optionsBuilder.UseSqlServer("Server=...;Database=SchoolDB;Trusted_Connection=True;");
  }
  ```  
- **Použití dependency injection v ASP.NET Core**:  
  ```csharp
  builder.Services.AddDbContext<AppDbContext>(options =>
      options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
  ```  

---

#### **Časté chyby a řešení**  

- **„Cannot open database“**:  
  - Zkontrolujte název databáze a oprávnění uživatele.  
  - Ověřte, zda databázový server běží.  
- **„Login failed for user“**:  
  - Špatné heslo nebo neexistující uživatel.  
- **„A network-related error occurred“**:  
  - Špatná adresa serveru nebo problém se sítí/firewallem.  

---

#### **Speciální případy**  

- **Azure SQL Database**:  
  ```text
  Server=tcp:mujserver.database.windows.net,1433;Database=SchoolDB;User Id=admin;Password=Heslo123;Encrypt=True;  
  ```  
- **Dockerizovaná databáze**:  
  ```text
  Server=host.docker.internal;Database=SchoolDB;User=sa;Password=Heslo123;  
  ```  

---

#### **Doporučené zdroje**  

- Dokumentace EF Core: [Connection Strings](https://learn.microsoft.com/en-us/ef/core/miscellaneous/connection-strings)  
- Generátor connection stringů: [ConnectionStrings.com](https://www.connectionstrings.com/)  
- Azure SQL konfigurace: [Azure SQL Security Best Practices](https://learn.microsoft.com/en-us/azure/azure-sql/database/secure-database-tutorial)  

---

**Praktický tip**: Pro testování používejte **SQLite** s `Data Source=:memory:` pro dočasnou databázi v paměti.
