
## **Externí zprostředkovatelé v ASP.NET Core Identity**  

---

### **1. Úvod**  

Externí zprostředkovatelé (např. Google, Facebook, Microsoft, Twitter) umožňují uživatelům přihlašovat se do aplikace pomocí existujících účtů z těchto služeb. ASP.NET Core Identity podporuje integraci těchto poskytovatelů pomocí standardů **OAuth 2.0** a **OpenID Connect**. Tím odpadá nutnost správy hesel a zvyšuje se uživatelská přívětivost.

---

### **2. Klíčoví Zprostředkovatelé**  

- **Google**  
- **Facebook**  
- **Microsoft (Azure AD)**  
- **Twitter (X)**  
- **GitHub**  
- **LinkedIn**  
- **Vlastní poskytovatelé** (např. firemní SSO).  

---

### **3. Princip Fungování**  

1. Uživatel klikne na tlačítko "Přihlásit se přes [Poskytovatele]".  
2. Aplikace přesměruje uživatele na přihlašovací stránku poskytovatele.  
3. Po úspěšném přihlášení poskytovatel vrátí **autorizační kód** nebo **ID token**.  
4. Aplikace ověří token a získá informace o uživateli (např. e-mail, jméno).  
5. Uživatel je přihlášen nebo vytvořen v lokální databázi.  

---

### **4. Nastavení Externího Přihlašování**  

#### **Krok 1: Registrace aplikace u poskytovatele**  

- Získejte **Client ID** a **Client Secret** z vývojářského portálu poskytovatele (např. [Google Cloud Console](https://console.cloud.google.com/)).  

#### **Krok 2: Konfigurace v ASP.NET Core**  

V `Program.cs` přidejte službu pro externí přihlášení (příklad pro Google):  
```csharp
builder.Services.AddAuthentication()
    .AddGoogle(options => {
        options.ClientId = builder.Configuration["Google:ClientId"];
        options.ClientSecret = builder.Configuration["Google:ClientSecret"];
        options.CallbackPath = "/signin-google"; // Výchozí cesta pro callback
    });
```

#### **Krok 3: Přidání tlačítek do UI**  

V Razor stránce (např. `Login.cshtml`):  
```html
<form method="post" action="/signin-google">
    <button type="submit">Přihlásit se přes Google</button>
</form>
```

---

### **5. Zpracování Uživatelských Dat**  

- **Claimy**: Externí poskytovatelé vracejí informace o uživateli jako **claims** (např. `ClaimTypes.Email`).  
- **Mapování claimů**: Přizpůsobte mapování v konfiguraci:  
  ```csharp
  .AddGoogle(options => {
      // ...
      options.ClaimActions.MapJsonKey("urn:google:profile", "link"); // Vlastní claim
  });
  ```
- **Uložení uživatele**: Pokud uživatel neexistuje, vytvořte ho v databázi pomocí `UserManager`:  
  ```csharp
  var info = await _signInManager.GetExternalLoginInfoAsync();
  var user = await _userManager.FindByEmailAsync(info.Principal.FindFirstValue(ClaimTypes.Email));
  if (user == null) {
      user = new IdentityUser { UserName = info.Principal.FindFirstValue(ClaimTypes.Email) };
      await _userManager.CreateAsync(user);
  }
  await _userManager.AddLoginAsync(user, info);
  ```

---

### **6. Důležité Bezpečnostní Aspekty**  

- **HTTPS**: Povinné v produkčním prostředí (tokeny jsou citlivá data).  
- **Ověření e-mailu**: Někteří poskytovatelé (např. Google) ověřený e-mail garantují.  
- **Omezení přístupu**: Používejte autorizační politiky (např. `[Authorize]`).  
- **Validace tokenů**: ASP.NET Core automaticky ověřuje podpis tokenů.  

---

### **7. Časté Problémy**  

| **Problém** | **Řešení** |  
|-------------|------------|  
| Neplatné `ClientId` nebo `ClientSecret` | Ověřte hodnoty v konfiguraci. |  
| Chybějící přesměrovací URL (CallbackPath) | Přidejte URL do seznamu povolených u poskytovatele. |  
| Nezískání e-mailu | Požadujte odpovídající scope (např. `options.Scope.Add("email")`). |  

---

### **8. Scopes a Oprávnění**  

- **Scopes** definují, jaká data aplikace požaduje od poskytovatele.  
- Příklad pro Google:  
  ```csharp
  options.Scope.Add("https://www.googleapis.com/auth/user.birthday.read"); // Požadavek na datum narození
  ```

---

### **9. Výhody a Výzvy**  

| **Výhody** | **Výzvy** |  
|------------|------------|  
| Uživatelé nemusí vytvářet nové účty | Nutnost registrace u poskytovatelů |  
| Snížení rizika úniku hesel | Různá úroveň kvality dat (např. neověřené e-maily) |  
| Rychlejší registrace | Složitější ladění toku přihlášení |  

---

### **10. Užitečné Zdroje**  

- **Dokumentace**:  
  [Microsoft Learn – Externí přihlášení v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/social/)  
- **Návody**:  
  - [Přihlášení přes Google krok za krokem](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/social/google-logins)  
  - [Integrace Facebooku](https://code-maze.com/asp-net-core-facebook-authentication/)  

---

**Shrnutí**:  
Externí zprostředkovatelé zjednodušují přihlašování a zvyšují bezpečnost aplikace. ASP.NET Core Identity nabízí integrovanou podporu pro hlavní poskytovatele pomocí OAuth 2.0/OpenID Connect. Klíčové je správné nastavení `ClientId`, `ClientSecret` a callback URL, spolu s mapováním uživatelských dat z claimů.
