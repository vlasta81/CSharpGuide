
## **Antiforgery v ASP.NET Core MVC**  

Ochrana před Cross-Site Request Forgery (CSRF) útoky.

---

### **1. Co je Antiforgery?**  

- **Bezpečnostní mechanismus** proti CSRF útokům, kdy útočník zneužije přihlášenou relaci uživatele k provedení neoprávněné akce.  
- **Princip**: Generuje unikátní token, který ověřuje legitimitu požadavku.  

---

### **2. Jak funguje Antiforgery Token?**  

1. **Generování tokenu**:  
   - Server vytvoří token při zobrazení formuláře (např. pomocí `@Html.AntiForgeryToken()` nebo Tag Helperu).  
2. **Odeslání tokenu**:  
   - Token je součástí formuláře (skryté pole) nebo hlavičky HTTP požadavku.  
3. **Ověření tokenu**:  
   - Server porovná token z požadavku s tokenem uloženým pro daného uživatele.  

---

### **3. Implementace v ASP.NET Core MVC**  

#### **a) Automatické generování tokenu**  

- **Formuláře s Tag Helpers**:  
  ```html  
  <form asp-action="Ulozit" method="post">  
      <!-- Formulářová pole -->  
      <input type="submit" value="Odeslat" />  
  </form>  
  ```  
  - Token je přidán automaticky jako skryté pole (`<input name="__RequestVerificationToken" ...>`).  

#### **b) Ověření tokenu na serveru**  

- Použijte atribut `[ValidateAntiForgeryToken]` na akci kontroleru:  
  ```csharp  
  [HttpPost]  
  [ValidateAntiForgeryToken]  
  public IActionResult Ulozit(Uzivatel model)  
  {  
      // Zpracování dat  
  }  
  ```  

#### **c) Globální ověření**  

- Nastavte globální filtr v `Program.cs`:  
  ```csharp  
  builder.Services.AddControllersWithViews(options =>  
  {  
      options.Filters.Add(new AutoValidateAntiforgeryTokenAttribute());  
  });  
  ```  
  - Ověřuje token u všech POST požadavků.  

---

### **4. Práce s AJAX požadavky**  

- **Přenos tokenu v hlavičce**:  
  ```javascript  
  fetch("/api/ulozit", {  
      method: "POST",  
      headers: {  
          "RequestVerificationToken": document.querySelector('input[name="__RequestVerificationToken"]').value  
      },  
      body: JSON.stringify(data)  
  });  
  ```  
- **Konfigurace služby**:  
  ```csharp  
  builder.Services.AddAntiforgery(options =>  
  {  
      options.HeaderName = "RequestVerificationToken";  
  });  
  ```  

---

### **5. Konfigurace Antiforgery**  

- **Nastavení cookie**:  
  ```csharp  
  builder.Services.AddAntiforgery(options =>  
  {  
      options.Cookie.Name = "AntiforgeryCookie";  
      options.Cookie.SecurePolicy = CookieSecurePolicy.Always;  
  });  
  ```  
- **Vlastní validace**:  
  - Implementace rozhraní `IAntiforgeryAdditionalDataProvider`.  

---

### **6. Časté chyby a řešení**  

| **Problém**                          | **Řešení**                                   |  
|---------------------------------------|----------------------------------------------|  
| Chybějící token ve formuláři          | Použijte Tag Helper `<form>` nebo přidejte `@Html.AntiForgeryToken()`. |  
| Neověřený AJAX požadavek              | Přeneste token v hlavičce a nakonfigurujte `HeaderName`. |  
| Neplatný token po resetu session      | Zajistěte synchronizaci tokenu a session.     |  

---

### **7. Bezpečnostní doporučení**  

- **Vždy ověřujte token** u všech POST, PUT, DELETE požadavků.  
- **Kombinujte s dalšími opatřeními**: Autorizace, HTTPS, CORS politiky.  
- **Nepoužívejte GET pro změnu stavu** (GET by neměl měnit data).  

---

### **8. Ukázka kódu**  

#### **Formulář s explicitním tokenem**  

```html  
<form method="post" action="/Home/Ulozit">  
    @Html.AntiForgeryToken()  
    <input type="text" name="Jmeno" />  
    <button type="submit">Odeslat</button>  
</form>  
```  

#### **Ověření tokenu v API kontroleru**  

```csharp  
[HttpPost]  
[ValidateAntiForgeryToken]  
public IActionResult UpdateSettings([FromBody] SettingsModel model)  
{  
    // Zpracování dat  
}  
```  

---

### **9. Shrnutí**  

- Antiforgery token **chrání před CSRF útoky** ověřením legitimity požadavku.  
- **Automatické generování** pomocí Tag Helperů zjednodušuje implementaci.  
- **Globální nebo lokální ověření** zajišťuje flexibilitu.  
- **AJAX požadavky** vyžadují explicitní přenos tokenu v hlavičce.  

---

**Doporučené zdroje**:  
- Oficiální dokumentace: *Prevent Cross-Site Request Forgery (XSRF/CSRF) attacks in ASP.NET Core*  
