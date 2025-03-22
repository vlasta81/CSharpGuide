
## **Konfigurace ASP.NET Core Identity**  

ASP.NET Core Identity je framework pro správu uživatelů, autentizaci a autorizaci. Jeho konfigurace zahrnuje nastavení služeb, pravidel pro hesla, přihlašování, rolí a integraci s databází. Zde je přehled klíčových kroků a možností:

---

### **1. Základní Nastavení**  

#### **Instalace balíčků**  

```bash
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
```

#### **Registrace služeb v `Program.cs`**  

```csharp
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));

builder.Services.AddDefaultIdentity<IdentityUser>(options => {
    options.SignIn.RequireConfirmedAccount = true; // Vyžaduje potvrzení e-mailu
})
.AddEntityFrameworkStores<ApplicationDbContext>();
```

#### **Middleware**  

```csharp
app.UseAuthentication(); // Zpracovává autentizaci
app.UseAuthorization();  // Zpracovává autorizaci
app.MapRazorPages();     // Nutné pro Razor Pages (registrace, přihlášení)
```

---

### **2. Konfigurace Pravidel pro Uživatele a Hesla**  

V `Program.cs` upravte `IdentityOptions`:  
```csharp
builder.Services.Configure<IdentityOptions>(options => {
    // Nastavení hesel
    options.Password.RequiredLength = 8;
    options.Password.RequireDigit = true;
    options.Password.RequireLowercase = true;
    options.Password.RequireUppercase = false;

    // Nastavení uživatelů
    options.User.RequireUniqueEmail = true;
    options.User.AllowedUserNameCharacters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789-._";
});
```

---

### **3. Přizpůsobení Autentizace**  

#### **Cookie nastavení**  

```csharp
builder.Services.ConfigureApplicationCookie(options => {
    options.Cookie.HttpOnly = true;
    options.ExpireTimeSpan = TimeSpan.FromMinutes(30); // Platnost cookie
    options.LoginPath = "/Identity/Account/Login";     // Cesta k přihlášení
    options.AccessDeniedPath = "/Identity/Account/AccessDenied"; // Cesta při zamítnutí přístupu
});
```

#### **JWT pro API**  

Pokud chráníte API:  
```csharp
builder.Services.AddAuthentication()
    .AddJwtBearer(options => {
        options.TokenValidationParameters = new TokenValidationParameters {
            ValidateIssuer = true,
            ValidIssuer = "https://vase-api.cz",
            ValidateAudience = true,
            ValidAudience = "vase-spa",
            IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("tajny-klic"))
        };
    });
```

---

### **4. Přidání Vlastního Uživatelského Modelu** 

1. Vytvořte třídu dědící z `IdentityUser`:  
   ```csharp
   public class ApplicationUser : IdentityUser {
       public string FullName { get; set; }
   }
   ```
2. Aktualizujte `ApplicationDbContext`:  
   ```csharp
   public class ApplicationDbContext : IdentityDbContext<ApplicationUser> { ... }
   ```
3. Změňte registraci služeb:  
   ```csharp
   builder.Services.AddIdentity<ApplicationUser, IdentityRole>()
       .AddEntityFrameworkStores<ApplicationDbContext>();
   ```

---

### **5. Externí Přihlašování (Google, Facebook)**  

```csharp
builder.Services.AddAuthentication()
    .AddGoogle(options => {
        options.ClientId = builder.Configuration["Google:ClientId"];
        options.ClientSecret = builder.Configuration["Google:ClientSecret"];
    })
    .AddFacebook(options => {
        options.AppId = builder.Configuration["Facebook:AppId"];
        options.AppSecret = builder.Configuration["Facebook:AppSecret"];
    });
```

---

### **6. Migrace Databáze**  

Po konfiguraci spusťte:  
```bash
dotnet ef migrations add "InitialIdentitySetup"
dotnet ef database update
```

---

### **7. Role a Politiky**  

#### **Vytvoření rolí**  

```csharp
public class RoleInitializer {
    public static async Task InitializeAsync(UserManager<IdentityUser> userManager, RoleManager<IdentityRole> roleManager) {
        // Vytvoření rolí
        string[] roles = { "Admin", "User" };
        foreach (var role in roles) {
            if (!await roleManager.RoleExistsAsync(role)) {
                await roleManager.CreateAsync(new IdentityRole(role));
            }
        }
    }
}
```

#### **Autorizační politiky**  

```csharp
builder.Services.AddAuthorization(options => {
    options.AddPolicy("RequireAdmin", policy => 
        policy.RequireRole("Admin"));
    options.AddPolicy("AdultOnly", policy => 
        policy.RequireClaim("Age", "18"));
});
```

---

### **8. Bezpečnostní Doporučení**  

- **HTTPS**: Vždy používejte v produkčním prostředí.  
- **Ochrana proti CSRF**: Používejte `[ValidateAntiForgeryToken]` u formulářů.  
- **Hashování hesel**: ASP.NET Core Identity automaticky hashuje hesla pomocí algoritmu PBKDF2.  

---

### **9. Časté Problémy a Řešení** 

- **Chybějící migrace**: Spusťte `dotnet ef database update`.  
- **Neplatné role**: Inicializujte role při startu aplikace.  
- **Neověřené požadavky**: Zkontrolujte, zda je `[Authorize]` atribut správně použit.  

---

### **10. Užitečné Zdroje**  

- **Dokumentace**: [Microsoft Learn – Konfigurace Identity](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/identity-configuration)  
- **Návody**:  
  - [Konfigurace rolí v ASP.NET Core](https://code-maze.com/asp-net-core-identity-roles/)  
  - [Externí přihlašování krok za krokem](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/social/)  

---

**Shrnutí**: Konfigurace ASP.NET Core Identity zahrnuje registraci služeb, úpravu pravidel pro hesla, nastavení autentizace (cookie/JWT) a integraci s externími poskytovateli. Díky flexibilitě frameworku lze snadno přidat vlastní modely uživatelů, role a politiky autorizace.
