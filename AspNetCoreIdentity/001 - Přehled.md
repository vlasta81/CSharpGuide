
## **ASP.NET Core Identity**

### **1. Úvod**  

ASP.NET Core Identity je framework pro správu uživatelů, autentizaci a autorizaci v aplikacích postavených na ASP.NET Core. Poskytuje hotová řešení pro registraci, přihlašování, správu hesel, rolí a integraci s externími poskytovateli (Google, Facebook, Microsoft atd.).

---

### **2. Klíčové Funkce**  

- **Správa uživatelů**: Registrace, úprava profilu, odstranění účtu.  
- **Autentizace**: Přihlašování pomocí hesel, 2FA (dvoufázové ověření).  
- **Autorizace**: Role a claims (nároky) pro řízení přístupu.  
- **Externí přihlašování**: Podpora OAuth/OpenID Connect (Google, Facebook, Twitter).  
- **Šifrování a bezpečnost**: Hashování hesel, ochrana proti útokům (CSRF, XSS).  
- **Customizace**: Možnost upravit datové modely, UI a business logiku.  

---

### **3. Hlavní Komponenty**  

- **UserManager<TUser>**: Metody pro správu uživatelů (CreateAsync, UpdateAsync).  
- **SignInManager<TUser>**: Přihlašování/odhlašování (PasswordSignInAsync, SignOutAsync).  
- **RoleManager<TRole>**: Správa rolí (CreateAsync, AddClaimAsync).  
- **Authentication Middleware**: Zajišťuje zpracování cookies nebo JWT tokenů.  
- **Datové modely**: `IdentityUser` (základní uživatel), `IdentityRole` (role).  

---

### **4. Nastavení a Konfigurace**  

- **Instalace balíčků**:  
  ```bash
  dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
  ```
- **Registrace služeb** (v `Program.cs`):  
  ```csharp
  builder.Services.AddIdentity<IdentityUser, IdentityRole>()
      .AddEntityFrameworkStores<ApplicationDbContext>();
  ```
- **Middleware**:  
  ```csharp
  app.UseAuthentication();
  app.UseAuthorization();
  ```
- **Migrace databáze**: Použití Entity Framework Core k vytvoření tabulek (např. `AspNetUsers`).

---

### **5. Použití v Controllerech**  

- **Registrace uživatele**:  
  ```csharp
  var result = await _userManager.CreateAsync(user, model.Password);
  ```
- **Přihlášení**:  
  ```csharp
  var result = await _signInManager.PasswordSignInAsync(model.Email, model.Password, isPersistent: false, lockoutOnFailure: false);
  ```
- **Autorizace pomocí atributů**:  
  ```csharp
  [Authorize(Roles = "Admin")]
  public class AdminController : Controller { ... }
  ```

---

### **6. Integrace s Externími Poskytovateli**  

- **Příklad pro Google**:  
  - Zaregistrovat aplikaci na [Google Cloud Console](https://console.cloud.google.com/).  
  - Přidat konfiguraci v `Program.cs`:  
    ```csharp
    builder.Services.AddAuthentication().AddGoogle(options => {
        options.ClientId = "ID";
        options.ClientSecret = "SECRET";
    });
    ```

---

### **7. Customizace**  

- **Rozšíření uživatelského modelu**:  
  ```csharp
  public class ApplicationUser : IdentityUser {
      public string FullName { get; set; }
  }
  ```
- **Změna pravidel pro hesla**:  
  ```csharp
  builder.Services.Configure<IdentityOptions>(options => {
      options.Password.RequiredLength = 8;
  });
  ```
- **Vlastní úložiště**: Implementace rozhraní `IUserStore<TUser>`.

---

### **8. Výhody a Nevýhody**  

| **Výhody** | **Nevýhody** |  
|------------|--------------|  
| Integrace s ASP.NET Core | Výchozí implementace závisí na Entity Framework Core |  
| Podpora moderních standardů (OAuth 2.0, OIDC) | Vyžaduje čas na pochopení pokročilých funkcí |  
| Flexibilní architektura |  |

---

### **9. Příklady Použití**  

- Enterprise aplikace s rolově řízeným přístupem.  
- E-shop s uživatelskými recenzemi a profily.  
- Interní systémy společnosti s 2FA.  

---

### **10. Doporučené Zdroje**  

- Oficiální dokumentace: [Microsoft Learn – ASP.NET Core Identity](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/identity)  
- Kurzy: YouTube kanály jako **CodeAcedemy**, **Patrick God** (v angličtině).  
- GitHub Repozitáře: Hledejte "aspnetcore-identity-samples".

---

### **11. Slovníček Pojmů**  

- **Claim**: Nárok (např. "Email: user@example.com").  
- **JWT (JSON Web Token)**: Token pro bezpečnou komunikaci.  
- **OAuth 2.0**: Standard pro delegovanou autorizaci.  
- **OpenID Connect**: Vrstva nad OAuth 2.0 pro autentizaci.

---

**Shrnutí**: ASP.NET Core Identity je komplexní řešení pro správu uživatelů a autentizaci. Jeho síla spočívá v integraci s ekosystémem .NET a flexibilitě, což z něj dělá ideální volbu pro většinu webových aplikací.
