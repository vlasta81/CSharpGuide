
## **Zabezpečení v ASP.NET Core MVC – Studijní materiál**  

Klíčové principy a techniky pro ochranu webových aplikací.

---

### **1. Úvod do zabezpečení**  

- **Cíl**: Ochrana před neoprávněným přístupem, únikem dat a útoky.  
- **Hlavní oblasti**: Autentizace, autorizace, ochrana dat, prevence běžných útoků.  

---

### **2. Hlavní bezpečnostní hrozby**  

#### **a) Cross-Site Scripting (XSS)**  

- **Popis**: Útok, kdy útočník vloží škodlivý kód do stránky (např. přes formuláře).  
- **Prevence**:  
  - Enkódování výstupů pomocí `@Html.Raw()` pouze pro důvěryhodná data.  
  - Nastavení HTTP hlavičky `Content-Security-Policy (CSP)`.  

#### **b) Cross-Site Request Forgery (CSRF)**  

- **Popis**: Zneužití přihlášené relace k provedení nechtěné akce.  
- **Prevence**:  
  - Generování a ověřování Anti-Forgery tokenů (`@Html.AntiForgeryToken()`).  
  - Použití atributu `[ValidateAntiForgeryToken]` u POST akcí.  

#### **c) SQL Injection**  

- **Popis**: Vložení SQL kódu do dotazů.  
- **Prevence**:  
  - Použití **Entity Framework Core** s parametrizovanými dotazy.  
  - Nikdy neskládat dotazy z řetězců s uživatelskými vstupy.  

#### **d) Clickjacking**  

- **Prevence**:  
  - Nastavení HTTP hlavičky `X-Frame-Options: DENY`.  

---

### **3. Autentizace a autorizace**  

#### **a) Autentizace (Authentication)**  

- **Metody**:  
  - **Cookie Authentication**: `builder.Services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)`.  
  - **JWT (JSON Web Tokens)**: Pro API.  
  - **Externí poskytovatelé** (Google, Facebook): `AddGoogle()`, `AddFacebook()`.  

#### **b) Autorizace (Authorization)**  

- **Role-based**:  
  ```csharp  
  [Authorize(Roles = "Admin")]  
  public IActionResult AdminPanel() { ... }  
  ```  
- **Policy-based**:  
  ```csharp  
  builder.Services.AddAuthorization(options =>  
      options.AddPolicy("Over18", policy => policy.RequireAge(18)));  
  ```  

---

### **4. HTTPS a bezpečné hlavičky**  

#### **a) Vynucení HTTPS**  

- **Middleware**:  
  ```csharp  
  app.UseHttpsRedirection(); // Přesměrování HTTP → HTTPS  
  app.UseHsts(); // Nastaví HSTS (HTTP Strict Transport Security)  
  ```  

#### **b) Bezpečné HTTP hlavičky**  

- **Nastavení v middleware**:  
  ```csharp  
  app.Use(async (context, next) =>  
  {  
      context.Response.Headers.Add("X-Content-Type-Options", "nosniff");  
      context.Response.Headers.Add("Referrer-Policy", "strict-origin-when-cross-origin");  
      await next();  
  });  
  ```  

---

### **5. Ochrana dat**  

#### **a) Data Protection API**  

- **Šifrování citlivých dat** (např. cookies, connection stringy):  
  ```csharp  
  builder.Services.AddDataProtection()  
      .PersistKeysToFileSystem(new DirectoryInfo(@"c:\keys"))  
      .SetApplicationName("MojeAplikace");  
  ```  

#### **b) Secure Cookies**  

- **Konfigurace**:  
  ```csharp  
  builder.Services.Configure<CookiePolicyOptions>(options =>  
  {  
      options.HttpOnly = HttpOnlyPolicy.Always;  
      options.Secure = CookieSecurePolicy.Always;  
  });  
  ```  

---

### **6. Validace vstupů**  

- **Data Annotations**:  
  ```csharp  
  public class Uzivatel  
  {  
      [Required]  
      [StringLength(50)]  
      public string Jmeno { get; set; }  
  }  
  ```  
- **Manuální validace**:  
  ```csharp  
  if (!ModelState.IsValid) return View(model);  
  ```  

---

### **7. Bezpečnostní best practices**  
- **Princip nejmenšího oprávnění**: Uživatelé mají přístup jen k nezbytným zdrojům.  
- **Aktualizace knihoven**: Pravidelně aktualizujte balíčky (NuGet).  
- **Logování a monitoring**: Detekce neobvyklé aktivity.  
- **Penetrační testování**: Použití nástrojů jako OWASP ZAP.  

---

### **8. Ukázky kódu**  

#### **Globální antiforgery token**  

```csharp  
builder.Services.AddControllersWithViews(options =>  
    options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute()));  
```  

#### **Politika pro věkové omezení**  

```csharp  
builder.Services.AddAuthorization(options =>  
{  
    options.AddPolicy("Over18", policy =>  
        policy.RequireAssertion(context =>  
            context.User.HasClaim(c => c.Type == "Age" && int.Parse(c.Value) >= 18));  
});  
```  

---

### **9. Shrnutí**  

- **Autentizace a autorizace** chrání přístup k zdrojům.  
- **HTTPS a bezpečné hlavičky** zvyšují odolnost proti útokům.  
- **Validace vstupů** a **Anti-Forgery tokeny** blokují XSS a CSRF.  
- **Data Protection API** šifruje citlivé informace.  
- Pravidelné audity a testování jsou klíčové pro udržení bezpečnosti.  

---

**Doporučené zdroje**:  
- Oficiální dokumentace: *Security in ASP.NET Core*  
