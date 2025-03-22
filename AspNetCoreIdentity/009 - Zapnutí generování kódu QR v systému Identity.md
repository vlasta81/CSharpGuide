
## **Povolení generování kódu QR pro ověřovací aplikace TOTP v ASP.NET Core Identity**  

---

### **1. Úvod**  

**TOTP (Time-based One-Time Password)** je metoda dvoufázového ověření (2FA), která generuje časově omezené kódy pomocí synchronizace mezi serverem a ověřovací aplikací (např. Google Authenticator, Microsoft Authenticator). ASP.NET Core Identity umožňuje generovat **QR kód** pro snadné přidání účtu do těchto aplikací. QR kód obsahuje tajný klíč a metadata potřebná pro generování kódů.

---

### **2. Klíčové Komponenty**  

- **Tajný klíč (Shared Key)**: Unikátní hodnota uložená na serveru a v ověřovací aplikaci.  
- **QR kód**: Grafická reprezentace URI ve formátu `otpauth://`, která zjednodušuje přidání účtu.  
- **Ověřovací aplikace**: Generuje kódy TOTP na základě tajného klíče a aktuálního času.  

---

### **3. Nastavení TOTP v ASP.NET Core Identity**  

#### **Krok 1: Povolení 2FA v projektu** 

1. Nainstalujte potřebné balíčky:  
   ```bash
   dotnet add package Microsoft.AspNetCore.Identity.UI
   ```
2. V `Program.cs` povolte podporu 2FA:  
   ```csharp
   builder.Services.AddDefaultIdentity<IdentityUser>(options => {
       options.SignIn.RequireConfirmedAccount = true;
       options.Tokens.AuthenticatorTokenProvider = TokenOptions.DefaultAuthenticatorProvider; // Výchozí poskytovatel
   }).AddEntityFrameworkStores<ApplicationDbContext>()
     .AddTokenProvider<AuthenticatorTokenProvider<IdentityUser>>(TokenOptions.DefaultAuthenticatorProvider);
   ```

---

### **4. Generování QR Kódu**  

#### **Krok 1: Vytvoření tajného klíče a URI**  

```csharp
// Získání aktuálního uživatele
var user = await _userManager.GetUserAsync(User);

// Resetování a generování nového tajného klíče
await _userManager.ResetAuthenticatorKeyAsync(user);
var sharedKey = await _userManager.GetAuthenticatorKeyAsync(user);

// Vytvoření URI pro QR kód
var authenticatorUri = GenerateQrCodeUri(user.Email, sharedKey);

// Metoda pro generování URI
private string GenerateQrCodeUri(string email, string sharedKey)
{
    var issuer = "NázevAplikace"; // Nahraďte názvem vaší aplikace
    return $"otpauth://totp/{issuer}:{email}?secret={sharedKey}&issuer={issuer}&digits=6";
}
```

#### **Krok 2: Konverze URI na QR kód**  

Pro generování QR kódu použijte knihovnu jako **QRCoder**:  
1. Instalace balíčku:  
   ```bash
   dotnet add package QRCoder
   ```
2. Generování QR kódu:  
   ```csharp
   using QRCodeGenerator = QRCoder.QRCodeGenerator;

   var qrGenerator = new QRCodeGenerator();
   var qrCodeData = qrGenerator.CreateQrCode(authenticatorUri, QRCodeGenerator.ECCLevel.Q);
   var qrCode = new PngByteQRCode(qrCodeData);
   var qrCodeBytes = qrCode.GetGraphic(20); // Velikost pixelů
   var qrCodeBase64 = Convert.ToBase64String(qrCodeBytes);
   ```
3. Zobrazení v Razor View:  
   ```html
   <img src="data:image/png;base64, @qrCodeBase64" alt="QR kód pro 2FA" />
   ```

---

### **5. Ověření Kódu od Uživatele**  

Po naskenování QR kódu ověřte zadaný TOTP kód:  
```csharp
var verificationCode = model.Code.Replace(" ", string.Empty); // Odstranění mezer
var isCodeValid = await _userManager.VerifyTwoFactorTokenAsync(
    user, 
    _userManager.Options.Tokens.AuthenticatorTokenProvider, 
    verificationCode
);

if (isCodeValid)
{
    await _userManager.SetTwoFactorEnabledAsync(user, true);
    return RedirectToAction("2FASuccess");
}
else
{
    ModelState.AddModelError("Code", "Neplatný kód.");
    return View();
}
```

---

### **6. Bezpečnostní Doporučení**  

- **HTTPS**: Vždy přenášejte tajný klíč a QR kód přes šifrované spojení.  
- **Zálohovací kódy**: Nabídněte uživateli záložní kódy pro obnovení přístupu.  
- **Resetování klíče**: Při ztrátě zařízení umožněte reset 2FA přes ověřený e-mail.  
- **Ochrana tajného klíče**: Ukládejte ho v databázi pouze v hashované podobě (ASP.NET Core to řeší automaticky).  

---

### **7. Časté Problémy** 

- **QR kód se nenačte**:  
  - Chybný formát URI (ověřte `issuer` a `secret`).  
  - Neplatná velikost QR kódu (použijte vyšší ECCLevel).  
- **Neplatný kód**:  
  - Časová nesynchronizace mezi serverem a zařízením (max. odchylka ±2 minuty).  
  - Chyba v generování tajného klíče.  

---

### **8. Užitečné Zdroje**  

- **Dokumentace**:  
  [Microsoft Learn – Dvoufázové ověření v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/identity-enable-qrcodes)  
- **Knihovny**:  
  - [QRCoder](https://github.com/codebude/QRCoder) – Generování QR kódů.  
  - [Otp.NET](https://github.com/kspearrin/Otp.NET) – Pokročilá práce s TOTP.  

---

**Shrnutí**:  
Povolení generování QR kódu pro TOTP v ASP.NET Core Identity zahrnuje generování tajného klíče, vytvoření URI ve formátu `otpauth://` a konverzi na QR kód. Díky integraci s knihovnami jako QRCoder lze tuto funkcionalitu snadno implementovat a zvýšit tak bezpečnost aplikace pomocí dvoufázového ověření.
