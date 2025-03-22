
## **Potvrzení účtu a obnovení hesla v ASP.NET Core Identity**

---

### **1. Úvod**  

ASP.NET Core Identity poskytuje integrované řešení pro **potvrzení uživatelského účtu** (např. přes e-mail) a **obnovení zapomenutého hesla**. Tyto funkce zvyšují bezpečnost aplikace a umožňují uživatelům ověřit vlastnictví účtu nebo resetovat heslo bez zásahu administrátora.

---

### **2. Klíčové Komponenty**  

- **Generování tokenů**: Jedinečné bezpečné řetězce pro potvrzení účtu nebo reset hesla.  
- **E-mailová služba**: Odesílání tokenů uživatelům (např. přes SMTP, SendGrid).  
- **Časově omezené platnosti tokenů**: Standardně 1 den (lze upravit).  
- **Integrované Razor stránky**: Předpřipravené formuláře pro potvrzení účtu a reset hesla.  

---

### **3. Potvrzení Účtu**  

#### **Krok 1: Povolení potvrzení účtu**  

V `Program.cs` nastavte:  
```csharp
builder.Services.AddIdentity<IdentityUser, IdentityRole>(options => {
    options.SignIn.RequireConfirmedAccount = true; // Vyžaduje potvrzení e-mailu pro přihlášení
}).AddEntityFrameworkStores<ApplicationDbContext>();
```

#### **Krok 2: Generování potvrzovacího tokenu**  

Při registraci uživatele:  
```csharp
var user = new IdentityUser { UserName = model.Email, Email = model.Email };
var result = await _userManager.CreateAsync(user, model.Password);

if (result.Succeeded) {
    var token = await _userManager.GenerateEmailConfirmationTokenAsync(user);
    var confirmationLink = Url.Action("ConfirmEmail", "Account", new { userId = user.Id, token }, Request.Scheme);
    // Odeslat e-mail s odkazem (confirmationLink)
}
```

#### **Krok 3: Ověření tokenu**  

V controlleru:  
```csharp
public async Task<IActionResult> ConfirmEmail(string userId, string token) {
    var user = await _userManager.FindByIdAsync(userId);
    if (user == null) return NotFound();

    var result = await _userManager.ConfirmEmailAsync(user, token);
    if (result.Succeeded) return View("Success");
    return View("Error");
}
```

---

### **4. Obnovení Hesla**  

#### **Krok 1: Žádost o reset hesla**  

Uživatel zadá e-mail a obdrží odkaz s tokenem:  
```csharp
var user = await _userManager.FindByEmailAsync(model.Email);
if (user == null || !await _userManager.IsEmailConfirmedAsync(user)) {
    // Nezobrazovat chybu (kvůli bezpečnosti)
    return RedirectToPage("ForgotPasswordConfirmation");
}

var token = await _userManager.GeneratePasswordResetTokenAsync(user);
var resetLink = Url.Action("ResetPassword", "Account", new { email = user.Email, token }, Request.Scheme);
// Odeslat e-mail s resetLink
```

#### **Krok 2: Resetování hesla**  

Zpracování formuláře pro nové heslo:  
```csharp
public async Task<IActionResult> ResetPassword(ResetPasswordModel model) {
    var user = await _userManager.FindByEmailAsync(model.Email);
    if (user == null) return RedirectToPage("Error");

    var result = await _userManager.ResetPasswordAsync(user, model.Token, model.Password);
    if (result.Succeeded) return View("Success");
    return View("Error");
}
```

---

### **5. Nastavení E-mailové Služby**  

#### **Příklad pro SMTP (v `appsettings.json`)**  

```json
"EmailSettings": {
    "SmtpServer": "smtp.example.com",
    "Port": 587,
    "Username": "user@example.com",
    "Password": "heslo"
}
```

#### **Registrace služby v `Program.cs`**  

```csharp
builder.Services.AddTransient<IEmailSender, EmailSender>();
```

#### **Implementace `IEmailSender`**  

```csharp
public class EmailSender : IEmailSender {
    public Task SendEmailAsync(string email, string subject, string htmlMessage) {
        // Použijte SMTP, SendGrid, nebo jinou službu
        return Task.CompletedTask;
    }
}
```

---

### **6. Bezpečnostní Doporučení**  

- **HTTPS**: Vždy používejte pro komunikaci s tokeny.  
- **Platnost tokenu**: Zkraťte ji pro citlivé operace (např. na 1 hodinu):  
  ```csharp
  builder.Services.Configure<DataProtectionTokenProviderOptions>(options => 
      options.TokenLifespan = TimeSpan.FromHours(1));
  ```
- **Validace tokenů**: Nikdy neukládejte tokeny do databáze.  
- **Ochrana proti útokům**: Omezte počet pokusů o reset hesla.  

---

### **7. Časté Problémy**  

- **E-maily se neodesílají**: Zkontrolujte konfiguraci SMTP nebo použijte placeholder službu (např. logování do souboru).  
- **Neplatný token**:  
  - Token vypršel.  
  - Uživatel byl smazán nebo změněn.  
- **Chybějící migrace**: Spusťte `dotnet ef database update` po přidání nových vlastností.  

---

### **8. Užitečné Zdroje**  

- **Dokumentace**:  
  [Microsoft Learn – Potvrzení účtu a reset hesla](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/accconfirm)  
- **Návody**:  
  - [Implementace e-mailové služby s SendGrid](https://code-maze.com/aspnet-core-send-email/)  
  - [Customizace tokenů v Identity](https://www.blinkingcaret.com/2018/05/30/asp-net-core-identity-tokens/)  

---

**Shrnutí**:  
Potvrzení účtu a obnovení hesla jsou klíčové pro bezpečnost a uživatelskou přívětivost. ASP.NET Core Identity poskytuje nástroje pro generování tokenů, integraci s e-mailovou službou a předpřipravené UI. Klíčové je správné nastavení platnosti tokenů, odesílání e-mailů a zabezpečení komunikace pomocí HTTPS.
