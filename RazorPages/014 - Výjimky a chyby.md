
### Výjimky a chyby v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Proč řešit výjimky a chyby?**  
  Správné zpracování chyb zajišťuje stabilitu aplikace a poskytuje uživatelům srozumitelné informace.  
  - **Typy chyb**:  
    - **Výjimky (Exceptions)**: Neočekávané chyby (např. chyba připojení k databázi).  
    - **HTTP chyby**: Status kódy jako 404 (Stránka nenalezena) nebo 500 (Chyba serveru).  
  - **Cíle**: Zabránit pádu aplikace, logovat chyby, zobrazit uživatelsky přívětivou stránku.

---

#### **2. Zpracování výjimek**  

##### **a) Middleware pro výjimky**  

- **Výchozí chování**:  
  - `UseExceptionHandler`: Přesměruje na chybovou stránku při neošetřené výjimce.  
  - `UseDeveloperExceptionPage`: Zobrazí podrobné informace o chybě **pouze v prostředí Development**.  

- **Konfigurace v `Program.cs`**:  
  ```csharp
  if (app.Environment.IsDevelopment())
  {
      app.UseDeveloperExceptionPage();
  }
  else
  {
      app.UseExceptionHandler("/Error"); // Přesměruje na /Error
      app.UseHsts();
  }
  ```

##### **b) Vlastní chybové stránky**  

- **Vytvoření stránky `Error.cshtml`**:  
  - Umístěte do složky `Pages`.  
  - Získejte detaily chyby přes `IExceptionHandlerPathFeature`:  
    ```csharp
    public void OnGet()
    {
        var exceptionHandler = HttpContext.Features.Get<IExceptionHandlerPathFeature>();
        ViewData["ErrorMessage"] = exceptionHandler?.Error.Message;
    }
    ```

---

#### **3. Zpracování HTTP chybových kódů**  

- **Middleware pro status kódy**:  
  ```csharp
  app.UseStatusCodePagesWithReExecute("/StatusCode/{0}"); // Vlastní stránky pro 404, 500, atd.
  ```

- **Vytvoření stránky `StatusCode.cshtml`**:  
  ```csharp
  public void OnGet(int statusCode)
  {
      StatusCode = statusCode;
      ViewData["ErrorMessage"] = $"Chyba {statusCode}";
  }
  ```

---

#### **4. Logování chyb**  

- **Použití `ILogger`**:  
  - Injektujte `ILogger<T>` do `PageModel` a zaznamenávejte chyby.  
  ```csharp
  public class ContactModel : PageModel
  {
      private readonly ILogger<ContactModel> _logger;

      public ContactModel(ILogger<ContactModel> logger)
      {
          _logger = logger;
      }

      public void OnPost()
      {
          try
          {
              // Kód, který může vyvolat výjimku
          }
          catch (Exception ex)
          {
              _logger.LogError(ex, "Chyba při odesílání formuláře");
              throw; // Předá chybu middleware
          }
      }
  }
  ```

---

#### **5. Validace vstupů**  

- **DataAnnotations a ModelState**:  
  - Atributy jako `[Required]` a `[EmailAddress]` automaticky validují vstupy.  
  - Kontrola platnosti v `OnPost`:  
    ```csharp
    public IActionResult OnPost()
    {
        if (!ModelState.IsValid)
        {
            return Page(); // Zobrazí chyby
        }
        // Uložení dat
    }
    ```

---

#### **6. Doporučené postupy**  

- **V produkci skryjte detaily**: Nikdy nezobrazujte stack trace uživatelům.  
- **Logujte všechny kritické chyby**: Pro pozdější analýzu.  
- **Používejtepřívětivé zprávy**: Např. "Něco se pokazilo. Kontaktujte podporu."  
- **Testujte chybové scénáře**: Simulujte výjimky a HTTP chyby během vývoje.  

---

#### **7. Časté chyby**  

- **Zapomenutý `UseExceptionHandler` v produkci**: Uživatelé uvidí prázdnou stránku.  
- **Neošetřené výjimky v kódu**: Vždy používejte `try-catch` pro rizikové operace.  
- **Špatné pořadí Middleware**: Např. `UseExceptionHandler` musí být na začátku pipeline.  

---

#### **8. Shrnutí**  

- **Výjimky** se řeší přes Middleware a vlastní chybové stránky.  
- **HTTP chyby** (404, 500) se konfigurují pomocí `UseStatusCodePages`.  
- **Logování** je klíčové pro diagnostiku problémů.  
- **Validace** zajišťuje správnost vstupů před zpracováním.  

---

#### **9. Užitečné zdroje**  

- Dokumentace: [Zpracování chyb v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/fundamentals/error-handling)  
- Tutoriál: [Vlastní chybové stránky](https://learn.microsoft.com/cs-cz/aspnet/core/web-api/handle-errors)  
- Ukázky: [GitHub – ASP.NET Core Error Handling](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/error-handling/samples)  
