
### Model v Razor Pages ASP.NET Core

#### **1. Úvod**  

- **Co je Model v Razor Pages?**  
  Model v Razor Pages reprezentuje třídu `PageModel` v souboru `.cshtml.cs`, která obsahuje logiku stránky (např. zpracování HTTP požadavků, validaci, práci s daty).  
  Slouží jako "kód za stránkou" (code-behind), odděluje obchodní logiku od prezentace (HTML v `.cshtml` souboru).  
  Díky vazbě mezi `PageModel` a Razor View (`@model`) lze snadno předávat data z backendu do UI.

---

#### **2. Role Modelu**  

- **Obsluha HTTP požadavků**: Metody jako `OnGet()`, `OnPost()`, `OnGetAsync()`, `OnPostAsync()`.  
- **Ukládání dat**: Vlastnosti (properties) pro uchování stavu stránky.  
- **Validace**: Použití atributů z `System.ComponentModel.DataAnnotations`.  
- **Komunikace s vrstvou služeb**: Např. Dependency Injection pro přístup k databázi nebo API.  
- **Model Binding**: Automatické mapování dat z formulářů nebo URL parametrů do vlastností.

---

#### **3. Struktura Modelu**  

- **Základní třída**: Dědí z `PageModel`.  
- **Obslužné metody**:  
  ```csharp
  public class ContactModel : PageModel
  {
      // Vlastnost pro vazbu dat z formuláře
      [BindProperty]
      public string Email { get; set; }

      // Obsluha GET požadavku
      public void OnGet()
      {
          // Inicializace dat při načtení stránky
      }

      // Obsluha POST požadavku
      public IActionResult OnPost()
      {
          if (ModelState.IsValid)
              return RedirectToPage("/Success");
          return Page(); // Zůstat na stránce při chybě
      }
  }
  ```

---

#### **4. Vazba dat (Model Binding)**  

- **Atribut `[BindProperty]`**:  
  - Mapuje data z HTTP požadavku (formuláře, query string) na vlastnosti `PageModel`.  
  - Příklad:  
    ```csharp
    [BindProperty]
    public UserInput Data { get; set; } // Data se automaticky naplní z formuláře
    ```

- **Atribut `[BindNever]`**: Zabraňuje vazbě pro konkrétní vlastnost.  
- **Vazba kolekcí**: Podporuje i pole a slovníky (např. `<input name="Tags[0]">`).

---

#### **5. Validace modelu**  

- **Atributy pro validaci**:  
  ```csharp
  public class LoginModel : PageModel
  {
      [BindProperty]
      [Required(ErrorMessage = "Email je povinný")]
      [EmailAddress]
      public string Email { get; set; }

      [BindProperty]
      [DataType(DataType.Password)]
      [MinLength(8)]
      public string Password { get; set; }
  }
  ```
- **Kontrola validace v metodě**:  
  ```csharp
  public IActionResult OnPost()
  {
      if (!ModelState.IsValid)
          return Page(); // Zobrazí chyby ve view
      // ... další logika
  }
  ```

---

#### **6. Předávání dat do View**  

- **Veřejné vlastnosti**: Přístupné přes `@Model.Property` v Razor View.  
- **Metoda `TempData`**: Pro dočasná data (např. mezi redirecty).  
- **ViewData/ViewBag**: Méně časté, lepší je použít `PageModel` vlastnosti.

---

#### **7. Příklady použití**  

- **Formulář s validací**:  
  ```html
  <!-- View -->
  <form method="post">
      <input asp-for="Email" />
      <span asp-validation-for="Email"></span>
      <button type="submit">Odeslat</button>
  </form>
  ```

- **Asynchronní zpracování**:  
  ```csharp
  public async Task<IActionResult> OnPostAsync()
  {
      await _database.SaveDataAsync(...);
      return RedirectToPage("/Index");
  }
  ```

---

#### **8. Doporučené postupy**  

- **Logiku udržujte v `PageModel`**: Vyhněte se kódu v `.cshtml` souborech.  
- **Používejte Dependency Injection**: Pro přístup ke službám (databáze, API).  
  ```csharp
  public class IndexModel : PageModel
  {
      private readonly IUserService _userService;

      public IndexModel(IUserService userService)
      {
          _userService = userService; // DI
      }
  }
  ```
- **Oddělujte doménové modely od `PageModel`**: Např. `User` (doménový objekt) vs. `UserInput` (vazba na formulář).  
- **Testovatelnost**: Díky oddělení logiky lze `PageModel` testovat unit testy.

---

#### **9. Časté chyby**  

- Zapomenutý atribut `[BindProperty]` → data se nenačtou.  
- Nevalidovaná data přímá použitá v logice (např. SQL injection riziko).  
- Příliš složité `PageModel` třídy → rozdělte logiku do služeb.

---

#### **10. Shrnutí**  

Model (`PageModel`) je jádrem logiky Razor Pages. Poskytuje:  
- Metody pro obsluhu HTTP akcí.  
- Vazbu a validaci dat.  
- Integraci se službami přes DI.  
- Čisté oddělení kódu od prezentace.  

Vhodné pro: Formuláře, CRUD operace, stránky s jasnou logikou.  
Nevhodné pro: Složité scénáře, kde je lepší MVC nebo Blazor.

---

#### **Užitečné zdroje**  

- Dokumentace: [Microsoft – Razor Pages Model](https://learn.microsoft.com/cs-cz/aspnet/core/razor-pages/?view=aspnetcore-8.0&tabs=visual-studio#the-pagemodel)  
- Tutoriál: [Vazba modelu a validace](https://learn.microsoft.com/cs-cz/aspnet/core/tutorials/razor-pages/validation)
