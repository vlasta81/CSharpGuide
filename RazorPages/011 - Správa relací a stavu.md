
### Správa relací a stavu v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Proč spravovat stav?**  
  HTTP je bezestavový protokol – každý požadavek je nezávislý. Pro udržení dat mezi požadavky (např. přihlášení uživatele, nákupní košík) je nutné explicitně spravovat stav.  
  - **Typické scénáře**: Uživatelské relace, dočasná data, mezipaměť.  

---

#### **2. Metody správy stavu**  

##### **a) Cookies**  

- **Popis**: Malá data uložená v prohlížeči uživatele.  
- **Použití**:  
  ```csharp
  // Nastavení cookie
  Response.Cookies.Append("Uživatel", "Jan Novák", new CookieOptions { Expires = DateTime.Now.AddDays(1) });

  // Čtení cookie
  var uživatel = Request.Cookies["Uživatel"];
  ```
- **Omezení**: Velikost (do 4 KB), nebezpečí manipulace (používejte šifrování).

##### **b) Session (Relace)**  

- **Popis**: Data uložená na serveru spojená s ID relace (ukládáno v cookie).  
- **Konfigurace**:  
  ```csharp
  builder.Services.AddSession(options => {
      options.Cookie.Name = "MojeRelace";
      options.IdleTimeout = TimeSpan.FromMinutes(20); // Doba platnosti
  });

  app.UseSession(); // V middleware pipeline
  ```
- **Použití**:  
  ```csharp
  // Uložení hodnoty
  HttpContext.Session.SetString("Email", "jan@example.com");
  
  // Čtení hodnoty
  var email = HttpContext.Session.GetString("Email");
  ```

##### **c) TempData**  

- **Popis**: Dočasná data přežívající přes jeden požadavek (např. po přesměrování).  
- **Použití**:  
  ```csharp
  // Uložení
  TempData["Zpráva"] = "Úspěšně uloženo!";
  
  // Čtení (automaticky smazáno po přečtení)
  var zpráva = TempData["Zpráva"];
  ```

##### **d) Query String a Hidden Fields**  

- **Query String**: Data v URL (např. `?id=5`).  
  ```html
  <a asp-page="/Detail" asp-route-id="5">Detail</a>
  ```
- **Hidden Fields**: Skrytá pole ve formulářích.  
  ```html
  <input type="hidden" asp-for="UserId" />
  ```

##### **e) Aplikační stav (In-Memory Cache)** 

- **Popis**: Globální data sdílená mezi všemi uživateli (např. konfigurace).  
- **Použití**:  
  ```csharp
  builder.Services.AddMemoryCache();
  
  // Uložení
  var cache = app.Services.GetRequiredService<IMemoryCache>();
  cache.Set("Klíč", "Hodnota", TimeSpan.FromMinutes(10));
  
  // Čtení
  cache.TryGetValue("Klíč", out string hodnota);
  ```

---

#### **3. Rozdíly mezi metodami**  

| **Metoda**       | **Rozsah platnosti**       | **Typ dat**          | **Bezpečnost**               |  
|------------------|----------------------------|----------------------|------------------------------|  
| **Cookies**      | Klient (prohlížeč)         | Malá textová data    | Riziko manipulace            |  
| **Session**      | Server (pro konkrétního uživatele) | Objekty (JSON)      | Bezpečnější než cookies      |  
| **TempData**     | Přes přesměrování           | Krátkodobá data      | Závisí na implementaci       |  
| **Query String** | URL                        | Text                 | Veřejně viditelná            |  

---

#### **4. Bezpečnostní doporučení**  

- **Cookies**:  
  - Používejte `Secure` a `HttpOnly` flagy.  
  - Šifrujte citlivá data (`IDataProtector`).  
- **Session**:  
  - Ukládejte minimalizovaná data (relace využívají serverové zdroje).  
  - Pro distribované aplikace použijte `IDistributedCache` (Redis, SQL Server).  

---

#### **5. Příklady použití**  

##### **Přihlášení uživatele (Session)**  

```csharp
public IActionResult OnPostLogin()
{
    // Ověření hesla...
    HttpContext.Session.SetString("UserId", "123");
    return RedirectToPage("/Profil");
}
```

##### **Zobrazení dočasné zprávy (TempData)**  

```csharp
public IActionResult OnPostUloz()
{
    // Uložení dat...
    TempData["Status"] = "Data byla uložena.";
    return RedirectToPage("/Index");
}
```

##### **Globální nastavení (Aplikační cache)**  

```csharp
public void OnGet()
{
    var cache = HttpContext.RequestServices.GetRequiredService<IMemoryCache>();
    var nastaveni = cache.Get("Konfigurace");
}
```

---

#### **6. Časté chyby**  

- **Příliš velká data v session**: Zpomalení aplikace.  
- **Nastavení session bez middleware**: `UseSession()` chybí v pipeline.  
- **Neserializovatelné objekty v session**: Ukládejte pouze primitivní typy nebo JSON.  

---

#### **7. Shrnutí**  

- **Cookies**: Pro malá data uložená na klientovi.  
- **Session**: Pro uživatelsky specifická data s omezenou dobou platnosti.  
- **TempData**: Ideální pro jednorázové zprávy po přesměrování.  
- **Query String/Hidden Fields**: Jednoduché řešení pro předávání ID mezi stránkami.  

---

#### **8. Užitečné zdroje**  

- Dokumentace: [Správa stavu v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/fundamentals/app-state)  
- OWASP: [Bezpečnost cookies a session](https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html)  
- Tutoriál: [Použití TempData v Razor Pages](https://learn.microsoft.com/cs-cz/aspnet/core/razor-pages/filter)
