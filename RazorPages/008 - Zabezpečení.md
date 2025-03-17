
### Zabezpečení v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Proč zabezpečení?**  
  Ochrana aplikace před běžnými útoky (XSS, CSRF, SQL Injection) a neoprávněným přístupem.  
  - **Klíčové oblasti**: Autentizace, autorizace, validace vstupů, šifrování dat, ochrana proti útokům.  
  - **Integrované mechanismy**: ASP.NET Core poskytuje nástroje pro zabezpečení "out-of-the-box".

---

#### **2. Autentizace (Authentication)**  

- **Co je autentizace?**  
  Ověření identity uživatele (např. přihlášení pomocí e-mailu a hesla).  
  - **ASP.NET Core Identity**:  
    - Hotové řešení pro správu uživatelů, rolí a hesel.  
    - Registrace, přihlášení, obnova hesla.  
  - **Externí poskytovatelé**: Google, Facebook, Microsoft (přes OAuth 2.0).  

- **Konfigurace v `Program.cs`**:  
  ```csharp
  builder.Services.AddDefaultIdentity<IdentityUser>(options => 
  {
      options.Password.RequireDigit = true;
      options.SignIn.RequireConfirmedAccount = true;
  }).AddEntityFrameworkStores<AppDbContext>();
  ```

---

#### **3. Autorizace (Authorization)**  

- **Co je autorizace?**  
  Určení, co smí ověřený uživatel dělat (např. přístup k administraci).  
  - **Atribut `[Authorize]`**:  
    - Omezení přístupu na stránky nebo metody.  
    ```csharp
    [Authorize(Roles = "Admin")]
    public class AdminModel : PageModel { ... }
    ```

  - **Politiky (Policies)**:  
    Vlastní pravidla pro komplexní scénáře (např. minimální věk 18 let).  
    ```csharp
    builder.Services.AddAuthorization(options =>
    {
        options.AddPolicy("AdultOnly", policy => 
            policy.RequireAssertion(context => 
                context.User.HasClaim(c => c.Type == "Age" && int.Parse(c.Value) >= 18));
    });
    ```

---

#### **4. Ochrana proti CSRF (Cross-Site Request Forgery)**  

- **Co je CSRF?**  
  Útok, kdy útočník nutí uživatele provést nechtěnou akci (např. změnu hesla).  
  - **Antiforgery Token**:  
    - Automaticky generován ve formulářích (pomocí `asp-antiforgery="true"`).  
    - Validován na serveru.  
    ```html
    <form method="post" asp-antiforgery="true">
        <!-- Obsah formuláře -->
    </form>
    ```

---

#### **5. Ochrana proti XSS (Cross-Site Scripting)**  

- **Co je XSS?**  
  Vložení škodlivého kódu (JavaScript) do stránky přes nevalidované vstupy.  
  - **Automatické kódování v Razor**:  
    - Výrazy jako `@Model.UserInput` jsou automaticky HTML-encodovány.  
  - **Vypnutí kódování**:  
    ```html
    @Html.Raw(Model.UnsafeContent) <!-- Používejte pouze pro důvěryhodná data! -->
    ```

---

#### **6. HTTPS a Bezpečné Hlavičky**  

- **Vynucení HTTPS**:  
  ```csharp
  app.UseHttpsRedirection(); // Přesměruje HTTP na HTTPS
  app.UseHsts(); // Hlavička Strict-Transport-Security
  ```

- **Bezpečné hlavičky**:  
  Přidejte middleware jako `SecurityHeadersMiddleware` pro hlavičky:  
  - `Content-Security-Policy (CSP)`: Omezení zdrojů skriptů a stylů.  
  - `X-Content-Type-Options`: Zabránění MIME sniffingu.  

---

#### **7. Validace Vstupů a SQL Injection**  

- **Validace**:  
  - Vždy používejte DataAnnotations (např. `[Required]`, `[EmailAddress]`).  
  - Ověřujte `ModelState.IsValid` v `OnPost` metodách.  

- **Ochrana před SQL Injection**:  
  - Používejte **Entity Framework Core** (parametrizované dotazy).  
  - Nikdy neskládejte dotazy ručně z řetězců!  

---

#### **8. Šifrování Citlivých Dat**  

- **ASP.NET Core Data Protection**:  
  - Šifrování cookies, tokenů a citlivých dat.  
  - Konfigurace:  
    ```csharp
    builder.Services.AddDataProtection()
        .PersistKeysToFileSystem(new DirectoryInfo("/secure-keys"))
        .SetApplicationName("MyApp");
    ```

---

#### **9. Logování a Monitorování**  

- **Logování podezřelých aktivit**:  
  ```csharp
  public class LoginModel : PageModel
  {
      private readonly ILogger<LoginModel> _logger;

      public async Task OnPostAsync()
      {
          _logger.LogWarning("Neplatný pokus o přihlášení z IP: {IP}", HttpContext.Connection.RemoteIpAddress);
      }
  }
  ```

---

#### **10. Časté Chyby a Doporučení**  

- **Nebezpečné chyby**:  
  - Vypnutá validace modelu (`ModelState.IsValid`).  
  - Ukládání hesel v plain textu (používejte `PasswordHasher`).  
  - Příliš široká CORS politika.  

- **Doporučení**:  
  - Pravidelně aktualizujte balíčky (např. `Microsoft.AspNetCore.Identity`).  
  - Používejte nástroje jako **OWASP ZAP** pro testování zabezpečení.  

---

#### **11. Shrnutí**  

- **Autentizace a autorizace**: Základ pro řízení přístupu.  
- **CSRF a XSS**: Neutralizujte pomocí antiforgery tokenů a kódování.  
- **HTTPS**: Povinné pro produkční prostředí.  
- **Validace**: Chraňte před neplatnými vstupy a SQL Injection.  

---

#### **12. Užitečné Zdroje**  

- Dokumentace: [Zabezpečení v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/security/)  
- Tutoriál: [Přidání autentizace do Razor Pages](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/identity)
