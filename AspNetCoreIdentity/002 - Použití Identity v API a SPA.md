
## **ASP.NET Core Identity pro zabezpečení back-endu API pro SPA**

---

### **1. Úvod**  

ASP.NET Core Identity umožňuje zabezpečit webové API pro Single-Page Applications (SPA) pomocí autentizace a autorizace. SPA (např. Angular, React, Vue.js) komunikují s API prostřednictvím HTTP požadavků a pro zabezpečení se často využívají **tokeny (JWT)** namísto cookies. Identity poskytuje nástroje pro generování tokenů, správu uživatelů a integraci s autorizačními politikami.

---

### **2. Klíčové Principy**  

- **Stateless autentizace**: SPA nepoužívají cookies – místo nich se využívají **JWT tokeny**.  
- **OAuth 2.0 / OpenID Connect**: Standardy pro tokenovou autentizaci.  
- **Role a claims**: Řízení přístupu k API endpointům na základě rolí nebo vlastních nároků.  
- **CORS**: Bezpečná komunikace mezi SPA (klient) a API (server).  

---

### **3. Nastavení Identity pro API** 

#### **Kroky konfigurace**  

1. **Instalace balíčků**:  
   ```bash
   dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
   dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
   ```

2. **Registrace služeb** (v `Program.cs`):  
   ```csharp
   builder.Services.AddIdentity<IdentityUser, IdentityRole>()
       .AddEntityFrameworkStores<ApplicationDbContext>();

   // Konfigurace JWT
   builder.Services.AddAuthentication(options => {
       options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
       options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
   }).AddJwtBearer(options => {
       options.TokenValidationParameters = new TokenValidationParameters {
           ValidateIssuer = true,
           ValidateAudience = true,
           ValidateLifetime = true,
           ValidateIssuerSigningKey = true,
           ValidIssuer = "https://vase-api.cz",
           ValidAudience = "vase-spa",
           IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("tajny-klic-alespon-256-bit"))
       };
   });
   ```

3. **Middleware**:  
   ```csharp
   app.UseAuthentication();
   app.UseAuthorization();
   ```

---

### **4. Generování JWT Tokenu**  

Příklad metody pro přihlášení a vrácení JWT:  
```csharp
[HttpPost("login")]
public async Task<IActionResult> Login(LoginModel model) {
    var user = await _userManager.FindByEmailAsync(model.Email);
    if (user == null || !await _userManager.CheckPasswordAsync(user, model.Password))
        return Unauthorized();

    var claims = new[] {
        new Claim(JwtRegisteredClaimNames.Sub, user.Id),
        new Claim(JwtRegisteredClaimNames.Email, user.Email),
        new Claim("custom_claim", "hodnota")
    };

    var token = new JwtSecurityToken(
        issuer: "https://vase-api.cz",
        audience: "vase-spa",
        claims: claims,
        expires: DateTime.UtcNow.AddHours(1),
        signingCredentials: new SigningCredentials(
            new SymmetricSecurityKey(Encoding.UTF8.GetBytes("tajny-klic")),
            SecurityAlgorithms.HmacSha256)
    );

    return Ok(new { token = new JwtSecurityTokenHandler().WriteToken(token) });
}
```

---

### **5. Zabezpečení Endpointů**  

- **Atribut `[Authorize]`**:  
  ```csharp
  [Authorize]
  [HttpGet("secure-data")]
  public IActionResult GetSecureData() {
      return Ok("Citlivá data");
  }
  ```

- **Autorizace pomocí rolí nebo claims**:  
  ```csharp
  [Authorize(Roles = "Admin")]
  [HttpGet("admin-data")]
  public IActionResult GetAdminData() { ... }

  // Nebo pomocí policy:
  builder.Services.AddAuthorization(options => {
      options.AddPolicy("RequireCustomClaim", policy =>
          policy.RequireClaim("custom_claim", "hodnota"));
  });

  [Authorize(Policy = "RequireCustomClaim")]
  ```

---

### **6. Integrace se SPA**  

- **Posílání tokenu v hlavičce**:  
  SPA musí přidat token do hlavičky `Authorization` jako `Bearer <token>`.  
  Příklad v JavaScriptu:  
  ```javascript
  fetch('https://api.example.com/secure-data', {
      headers: { 'Authorization': 'Bearer ' + localStorage.getItem('jwt') }
  });
  ```

- **CORS konfigurace**:  
  ```csharp
  builder.Services.AddCors(options => {
      options.AddPolicy("SpaPolicy", policy => {
          policy.WithOrigins("https://vase-spa.cz")
                .AllowAnyHeader()
                .AllowAnyMethod();
      });
  });
  ```

---

### **7. Bezpečnostní Doporučení**  

- **HTTPS**: Vždy používejte šifrovanou komunikaci.  
- **Krátká platnost tokenů**: Nastavte `expires` u JWT na 15–60 minut.  
- **Refresh tokeny**: Pro prodloužení session bez nutnosti nového přihlášení.  
- **Validace tokenů**: Vždy ověřujte `Issuer`, `Audience` a `Signature`.  
- **Ukládání tokenů v SPA**: Používejte `HttpOnly` cookies nebo `localStorage` s ochranou proti XSS.

---

### **8. Výhody a Výzvy**  

| **Výhody** | **Výzvy** |  
|------------|------------|  
| Jednoduchá integrace s ASP.NET Core | Správa životního cyklu tokenů (např. odhlášení uživatele) |  
| Podpora moderních standardů (JWT, OAuth 2.0) | Bezpečné ukládání tajného klíče pro JWT |  
| Škálovatelnost pro velké aplikace | Konfigurace CORS a ochrana proti CSRF |  

---

### **9. Příklady Scénářů**  

- **SPA s přihlašováním**: Uživatel se přihlásí, obdrží JWT a přistupuje k chráněným endpointům.  
- **Role-based dashboard**: Admin vs. běžný uživatel s různými právy.  
- **Integrace s externími poskytovateli**: Přihlášení přes Google a získání JWT pro API.  

---

### **10. Užitečné Zdroje** 

- **Dokumentace**:  
  [Microsoft Learn – Zabezpečení API v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/identity-api-authorization)  
- **Návody**:  
  - [JWT Authentication in ASP.NET Core](https://jwt.io/introduction)  
  - [Secure SPA with ASP.NET Core Identity](https://code-maze.com/aspnetcore-identity-angular/)  

---

**Shrnutí**: ASP.NET Core Identity poskytuje robustní řešení pro zabezpečení API pro SPA pomocí JWT tokenů. Klíčové je správné nastavení autentizace, generování tokenů a autorizace endpointů. Díky integraci s moderními standardy a flexibilitě je vhodné pro enterprise aplikace i menší projekty.
