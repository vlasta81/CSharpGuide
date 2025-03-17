
### Razor Pages v ASP.NET Core

#### **1. Úvod**  

- **Co jsou Razor Pages?**  
  Razor Pages je stránkově orientovaný framework v ASP.NET Core určený pro tvorbu webových UI.  
  Kombinuje logiku a prezentaci do jedné stránky (soubor `.cshtml` a třída `PageModel`), což zjednodušuje vývoj.  
  Vhodné pro aplikace s jednoduchou až střední složitostí, kde je důraz na stránkovou navigaci (např. formuláře, blogy, administrativní rozhraní).

---

#### **2. Hlavní vlastnosti**  

- **Page-based model**: Každá stránka má vlastní třídu (`PageModel`) s obslužnými metodami (např. `OnGet`, `OnPost`).  
- **Razor Syntax**: Kombinace HTML a C# pomocí Razor markupu (např. `@Model.Property`).  
- **Oddělení logiky a prezentace**: Kód se píše v `PageModel`, zatímco `.cshtml` soubor obsahuje HTML.  
- **Dependency Injection**: Integrovaná podpora pro vkládání závislostí.  
- **Routing**: Automatické mapování URL na fyzické soubory (např. `/Contact` → `Pages/Contact.cshtml`).  
- **Model Binding**: Automatické mapování dat z HTTP požadavků na vlastnosti v `PageModel`.  
- **Validace**: Podpora atributů z `System.ComponentModel.DataAnnotations` (např. `[Required]`).  
- **Integrace s ASP.NET Core**: Middleware, autentizace, konfigurace, atd.

---

#### **3. Struktura Razor Page**  

- **Soubory**:  
  - `NázevStránky.cshtml`: Obsahuje HTML a Razor syntax.  
  - `NázevStránky.cshtml.cs`: Obsahuje třídu `PageModel` s logikou.  

- **Příklad kódu**:  
  ```csharp
  // Soubor Contact.cshtml.cs
  public class ContactModel : PageModel
  {
      [BindProperty]
      public string Message { get; set; }

      public void OnGet() { } // Zpracování GET požadavku

      public IActionResult OnPost()
      {
          if (ModelState.IsValid)
              return RedirectToPage("/Success");
          return Page();
      }
  }
  ```

  ```html
  <!-- Soubor Contact.cshtml -->
  @page
  @model ContactModel

  <form method="post">
      <input asp-for="Message" />
      <button type="submit">Odeslat</button>
  </form>
  ```

---

#### **4. Výhody oproti MVC**  

- **Jednodušší architektura**: Pro stránkově orientované aplikace bez nutnosti složitého MVC rozdělení (Controllers/Views).  
- **Méně boilerplate kódu**: Logika přímo vázaná na stránku.  
- **Čitelnější struktura**: Všechny součásti stránky jsou v jedné složce.  
- **Vhodné pro menší projekty**: Rychlejší vývoj pro CRUD operace nebo statické stránky.

---

#### **5. Jak začít**  

1. Nainstalujte [.NET SDK](https://dotnet.microsoft.com/download).  
2. Vytvořte projekt:  
   ```bash
   dotnet new webapp -o MyRazorApp
   ```  
3. Razor Pages jsou ve složce `Pages/`.  
4. Spusťte aplikaci: `dotnet run`.

---

#### **6. Doporučené postupy**  

- Udržujte logiku v `PageModel`, ne v `.cshtml` souborech.  
- Pro komplexní komponenty použijte **Partial Views** nebo **View Components**.  
- Využívejte asynchronní metody (`OnGetAsync`, `OnPostAsync`).  
- Validujte vstupy pomocí atributů (např. `[Required]`).  
- Pro sdílenou logiku použijte služby a Dependency Injection.

---

#### **7. Nevýhody a omezení** 

- Méně vhodné pro SPA (Single-Page Applications) nebo REST API.  
- Omezená podpora pro velmi komplexní scénáře (větší projekty mohou vyžadovat MVC).  

---

#### **8. Užitečné zdroje**  

- Oficiální dokumentace: [Microsoft Docs – Razor Pages](https://learn.microsoft.com/cs-cz/aspnet/core/razor-pages/)  
- Tutoriál: [Vytvoření první Razor Page](https://learn.microsoft.com/cs-cz/aspnet/core/tutorials/razor-pages/razor-pages-start)  
- GitHub: [ASP.NET Core Repository](https://github.com/dotnet/aspnetcore)  

---

#### **Shrnutí**  

Razor Pages je efektivní nástroj pro rychlý vývoj stránkově orientovaných aplikací v ASP.NET Core. Díky kombinaci Razor syntaxe a jednoduché architektuře je ideální pro začátečníky i pro projekty, kde není nutné plné MVC.
