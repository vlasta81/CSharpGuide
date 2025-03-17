
### Page (stránka) v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Co je "Page" v Razor Pages?**  
  Stránka (Page) je základní stavební jednotka aplikace v Razor Pages. Skládá se ze dvou souborů:  
  - **`.cshtml`**: Obsahuje HTML a Razor syntax pro UI (prezentaci).  
  - **`.cshtml.cs`**: Obsahuje třídu `PageModel` s logikou stránky (code-behind).  
  - Každá stránka odpovídá jedné URL cestě a zpracovává HTTP požadavky (GET, POST).  

---

#### **2. Struktura stránky** 

1. **Soubor `.cshtml`**  
   - **HTML + Razor syntax**: Kombinace HTML a C# pomocí `@` symbolu (např. `@Model.Name`).  
   - **`@page` direktiva**: Povinná direktiva na začátku souboru. Aktivuje funkci stránky a definuje routování.  
     ```html
     @page
     @model IndexModel
     <h1>@Model.Title</h1>
     ```

2. **Soubor `.cshtml.cs`**  
   - **Třída `PageModel`**: Obsahuje metody pro obsluhu HTTP požadavků a vlastnosti pro data.  
     ```csharp
     public class IndexModel : PageModel
     {
         public string Title { get; set; } = "Vítejte!";

         public void OnGet()
         {
             // Logika pro GET požadavek
         }
     }
     ```

---

#### **3. Klíčové prvky stránky**  

- **`@page` direktiva**:  
  - Nastavuje, že soubor je Razor Page (ne pouze View).  
  - Lze přidat parametry routy: `@page "{id}"` → URL jako `/page/5`.  

- **Handlers (obslužné metody)**:  
  - Metody v `PageModel` pojmenované jako `On[HTTP metoda]` (např. `OnGet`, `OnPost`, `OnPostDelete`).  
  - Příklady:  
    ```csharp
    public void OnGet() { } // GET bez parametrů  
    public void OnGet(int id) { } // GET s parametrem  
    public IActionResult OnPost() { } // POST  
    ```

- **Vazba dat**:  
  - Atribut `[BindProperty]` pro automatické mapování dat z formulářů do vlastností.  
    ```csharp
    [BindProperty]
    public string Username { get; set; }
    ```

- **Razor syntax v UI**:  
  - Zobrazení dat: `@Model.Username`.  
  - Formuláře: `<form method="post">`.  
  - Tag Helpers: `asp-page`, `asp-for` (např. `<input asp-for="Username">`).  

---

#### **4. Životní cyklus stránky**  

1. **Požadavek na URL** → ASP.NET Core najde odpovídající `.cshtml` soubor.  
2. **Vytvoření instance `PageModel`** (pokud existuje).  
3. **Volání handleru**: Podle HTTP metody (např. `OnGet` pro GET).  
4. **Zpracování dat**: Validace, práce se službami (DI).  
5. **Generování HTML**: Razor View vykreslí výsledek.  
6. **Návratový typ**:  
   - `Page()`: Zobrazí aktuální stránku.  
   - `RedirectToPage("/NováStránka")`: Přesměruje.  
   - `Json(data)`: Vrátí JSON.  

---

#### **5. Příklad kompletní stránky** 

**Soubor `Login.cshtml`**  
```html
@page
@model LoginModel

<h2>Přihlášení</h2>
<form method="post">
    <input asp-for="Username" placeholder="Uživatelské jméno" />
    <input asp-for="Password" type="password" />
    <button type="submit">Přihlásit</button>
</form>
```

**Soubor `Login.cshtml.cs`**  
```csharp
public class LoginModel : PageModel
{
    [BindProperty]
    public string Username { get; set; }

    [BindProperty]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    public void OnGet() { }

    public IActionResult OnPost()
    {
        if (Username == "admin" && Password == "1234")
            return RedirectToPage("/Admin");
        ModelState.AddModelError("", "Neplatné přihlašovací údaje");
        return Page();
    }
}
```

---

#### **6. Routování**  

- **Výchozí konvence**:  
  - Cesta `/Login` mapuje na soubor `/Pages/Login.cshtml`.  
  - Složky v `/Pages` se promítnou do URL (např. `/Pages/Admin/Users.cshtml` → `/Admin/Users`).  

- **Vlastní routa**:  
  - Definice pomocí `@page` direktivy:  
    ```html
    @page "/prihlaseni"
    ```  
    → URL bude `/prihlaseni` místo `/Login`.  

---

#### **7. Výhody stránky**  

- **Samostatná entita**: Vše pro danou funkcionalitu je v jedné složce (soubor `.cshtml`, `.cshtml.cs`, statické soubory).  
- **Jednoduchá navigace**: Přímé mapování URL na stránky.  
- **Testovatelnost**: `PageModel` lze testovat jako běžnou třídu.  

---

#### **8. Časté problémy**  

- **Chybějící `@page` direktiva**: Stránka nebude fungovat jako Razor Page.  
- **Nevalidovaná data**: Riziko bezpečnostních chyb (např. SQL injection).  
- **Příliš složitý `PageModel`**: Rozdělte logiku do služeb.  

---

#### **9. Shrnutí**  

- **Stránka = UI + logika**: `.cshtml` (HTML) a `.cshtml.cs` (`PageModel`).  
- **Handlers**: `OnGet`, `OnPost` pro zpracování požadavků.  
- **Routing**: Automatický nebo vlastní přes `@page`.  
- **Vhodné pro**: Formuláře, statické stránky, CRUD operace.  

---

#### **10. Užitečné zdroje**  

- Dokumentace: [Microsoft – Razor Pages](https://learn.microsoft.com/cs-cz/aspnet/core/razor-pages/)  
- Tutoriál: [Vytvoření formuláře](https://learn.microsoft.com/cs-cz/aspnet/core/tutorials/razor-pages/forms)  
