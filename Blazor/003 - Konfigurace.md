
## **Souhrn: Konfigurace ASP.NET Core Blazor**  

ASP.NET Core Blazor využívá konfigurační systém .NET Core, který je flexibilní a umožňuje přizpůsobení aplikace pro různé prostředí (vývoj, produkce). Zde jsou klíčové aspekty konfigurace:

---

### **1. Zdroje konfigurace**  

- **appsettings.json**: Hlavní konfigurační soubor pro nastavení aplikace (např. připojení k databázi, API klíče).  
  - Pro environment-specific nastavení: `appsettings.{Prostředí}.json` (např. `appsettings.Production.json`).  
- **Proměnné prostředí**: Přepisují hodnoty z `appsettings.json` (užitečné pro CI/CD nebo Docker).  
- **Argumenty příkazové řádky**: Dynamické nastavení parametrů při spuštění aplikace.  
- **Uživatelské tajné klíče (Secret Manager)**: Pro citlivá data během vývoje (např. připojovací řetězce).  

**Příklad struktury appsettings.json**:  
```json
{
  "ConnectionStrings": {
    "Default": "Server=...;Database=..."
  },
  "ApiSettings": {
    "Url": "https://api.example.com"
  }
}
```

---

### **2. Přístup ke konfiguraci**  

- **Dependency Injection (DI)**: Služba `IConfiguration` poskytuje přístup ke konfiguračním hodnotám.  
  - **V komponentách Blazor**:  
    ```csharp
    @inject IConfiguration Configuration

    <p>API URL: @Configuration["ApiSettings:Url"]</p>
    ```  
  - **V kódu služeb**:  
    ```csharp
    public class MyService
    {
        private readonly string _apiUrl;
        
        public MyService(IConfiguration config)
        {
            _apiUrl = config["ApiSettings:Url"];
        }
    }
    ```  

---

### **3. Konfigurace prostředí**  

- **Nastavení prostředí**:  
  - Proměnná prostředí `ASPNETCORE_ENVIRONMENT` určuje aktuální prostředí (Development, Production, atd.).  
  - Výchozí prostředí: **Development** (lze změnit v `launchSettings.json`).  
- **Podmíněná konfigurace**:  
  ```csharp
  if (app.Environment.IsDevelopment())
  {
      app.UseDeveloperExceptionPage();
  }
  ```

---

### **4. Konfigurace hostování (Blazor Server vs WebAssembly)**  

- **Blazor Server**:  
  - Konfigurace se řeší na serveru (standardní .NET Core konfigurace v `Program.cs` nebo `Startup.cs`).  
  - Příklady: Nastavení SignalR timeoutu, velikosti bufferů.  
    ```csharp
    services.AddServerSideBlazor(options =>
    {
        options.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(5);
    });
    ```  

- **Blazor WebAssembly**:  
  - Konfigurace klienta se nachází v `wwwroot/appsettings.json` (soubory se stáhnou do prohlížeče).  
  - Pro environment-specific nastavení: Dynamické načítání konfigurace při buildu (např. `appsettings.Production.json`).  

---

### **5. Konfigurace závislostí (Dependency Injection)**  

- **Registrace služeb**:  
  - V `Program.cs` (Blazor WebAssembly) nebo `Startup.cs` (Blazor Server):  
    ```csharp
    builder.Services.AddSingleton<IMyService, MyService>();
    builder.Services.AddScoped<IApiClient, ApiClient>();
    ```  
- **Konfigurace HttpClient (WebAssembly)**:  
  ```csharp
  builder.Services.AddHttpClient("MyApi", client =>
  {
      client.BaseAddress = new Uri(builder.Configuration["ApiSettings:Url"]);
  });
  ```

---

### **6. Bezpečnostní konfigurace**  

- **Autentizace a autorizace**:  
  - Nastavení v `Program.cs` pomocí `AddAuthentication` a `AddAuthorization`.  
  - Podpora providerů: Azure AD, IdentityServer, JWT.  
  ```csharp
  builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
      .AddJwtBearer(options =>
      {
          options.Authority = "https://auth.example.com";
      });
  ```  
- **CORS (Cross-Origin Resource Sharing)**:  
  - Konfigurace pro WebAssembly, pokud komunikuje s externími API.  
  ```csharp
  builder.Services.AddCors(options =>
  {
      options.AddPolicy("AllowAll", policy => policy.AllowAnyOrigin().AllowAnyMethod());
  });
  ```

---

### **7. Konfigurace routování**  

- **Výchozí routa**: Definována v `App.razor`.  
- **Vlastní cesty**: Použití direktivy `@page` v komponentách.  
  ```razor
  @page "/users"
  @page "/users/{Id:int}"
  ```  
- **Nastavení parametrů routy**:  
  ```csharp
  [Parameter]
  public int Id { get; set; }
  ```

---

### **8. Logování**  

- **Integrované logování**:  
  ```csharp
  builder.Logging.AddConsole();
  builder.Logging.AddDebug();
  ```  
- **Konfigurace úrovně logování**:  
  ```json
  {
    "Logging": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft.AspNetCore": "Error"
      }
    }
  }
  ```

---

### **Shrnutí**  

- Blazor využívá standardní .NET Core konfigurační systém s podporou JSON, proměnných prostředí a argumentů příkazové řádky.  
- **Blazor Server**: Konfigurace se řeší na serveru (např. SignalR, DI).  
- **Blazor WebAssembly**: Konfigurace klienta je v `wwwroot/appsettings.json`.  
- Klíčové oblasti: Bezpečnost, DI, prostředí, logování.  

**Užitečné zdroje**:  
- Oficiální dokumentace: [Konfigurace v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/configuration).  
