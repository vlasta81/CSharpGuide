
### Antiforgery v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Co je Antiforgery Token?**  
  Antiforgery token (token proti padělání požadavků) je bezpečnostní mechanismus proti **CSRF útokům** (Cross-Site Request Forgery).  
  - **Účel**: Zajistí, že požadavky POST, PUT, DELETE pocházejí z legitimního zdroje (např. z vaší aplikace, ne z podvodného formuláře).  
  - **Jak funguje**: Generuje unikátní token, který je ověřen na serveru.  

---

#### **2. Princip CSRF útoku**  

- **Scénář útoku**:  
  Útočník přesvědčí uživatele, aby odeslal požadavek na vaši aplikaci (např. změnu hesla) z jiné stránky.  
  - **Důsledky**: Neautorizovaná akce provedená bez vědomí uživatele.  

---

#### **3. Integrace Antiforgery v Razor Pages**  

- **Automatické generování tokenu**:  
  - Ve formulářích s metodou POST se token přidává automaticky pomocí **Tag Helperu** `asp-antiforgery`.  
  - Příklad:  
    ```html
    <form method="post" asp-antiforgery="true">
        <!-- Formulářová pole -->
    </form>
    ```

- **Manuální přidání tokenu**:  
  Pokud nepoužíváte Tag Helper, vložte token pomocí `@Html.AntiForgeryToken()`:  
  ```html
  <form method="post">
      @Html.AntiForgeryToken()
      <!-- Formulářová pole -->
  </form>
  ```

---

#### **4. Validace tokenu na serveru**  

- **Výchozí chování**:  
  Razor Pages automaticky validují antiforgery token pro všechny **POST požadavky**.  
  - Pokud token chybí nebo je neplatný, vrátí se chyba **400 Bad Request**.  

- **Vypnutí validace**:  
  - Použijte atribut `[IgnoreAntiforgeryToken]` na úrovni stránky nebo metody:  
    ```csharp
    [IgnoreAntiforgeryToken]
    public class UnsafePageModel : PageModel { ... }
    ```

---

#### **5. Práce s AJAX požadavky**  

- **Přenos tokenu v hlavičkách**:  
  - Token je uložen v cookie `XSRF-TOKEN` a musí být přidán do hlavičky `X-XSRF-TOKEN`.  
  - Příklad v JavaScriptu:  
    ```javascript
    fetch("/api/endpoint", {
        method: "POST",
        headers: {
            "X-XSRF-TOKEN": getCookie("XSRF-TOKEN"),
            "Content-Type": "application/json"
        },
        body: JSON.stringify(data)
    });
    ```

- **Získání tokenu v Razor View**:  
  ```javascript
  const token = document.querySelector('input[name="__RequestVerificationToken"]').value;
  ```

---

#### **6. Konfigurace Antiforgery**  

- **Vlastní nastavení**:  
  V `Program.cs` lze upravit vlastnosti tokenu (např. název cookie, délka platnosti):  
  ```csharp
  builder.Services.AddAntiforgery(options => {
      options.Cookie.Name = "Secure-CSRF-Token";
      options.FormFieldName = "Custom-CSRF-Field";
      options.HeaderName = "X-Custom-CSRF-Header";
  });
  ```

---

#### **7. Časté problémy a řešení**  

- **Chyba "Antiforgery token validation failed"**:  
  - **Příčiny**: Chybějící token, neplatný token, nesoulad mezi cookie a form field.  
  - **Řešení**:  
    - Ověřte, že formulář obsahuje `asp-antiforgery="true"`.  
    - U AJAX požadavků přidejte token do hlavičky.  

- **Problémy s cookies**:  
  - Zkontrolujte nastavení `SameSite` atributu cookie (doporučeno `Lax` nebo `Strict`).  

---

#### **8. Doporučené postupy**  

- **Nikdy nevypínejte validaci** pro akce měnící stav (POST, PUT, DELETE).  
- **Používejte HTTPS**: Chraňte token před odposlechem.  
- **Kombinujte s dalšími bezpečnostními opatřeními**: Autentizace, autorizace, validace vstupů.  

---

#### **9. Shrnutí**  

- **Antiforgery token** chrání před CSRF útoky.  
- **Automatická integrace** v Razor Pages pro POST požadavky.  
- **AJAX požadavky** vyžadují explicitní přidání tokenu do hlaviček.  
- **Konfigurovatelné** parametry pro specifické potřeby.  

---

#### **10. Užitečné zdroje**  

- Dokumentace: [Antiforgery v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/security/anti-request-forgery)  
- OWASP: [Cross-Site Request Forgery (CSRF)](https://owasp.org/www-community/attacks/csrf)  
- Ukázky: [AJAX s Antiforgery tokenem](https://learn.microsoft.com/cs-cz/aspnet/core/security/anti-request-forgery?view=aspnetcore-8.0#javascript-ajax-and-spas)
