
### Layout v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Co je Layout?**  
  Layout (rozložení) je šablona, která definuje **společnou strukturu** pro více stránek (např. hlavičku, zápatí, navigační menu).  
  - **Účel**: Zajistit konzistentní vzhled aplikace bez opakování kódu.  
  - **Princip**: Každá stránka dědí layout a doplní do něj svůj unikátní obsah.  

---

#### **2. Základní struktura**  

- **Soubor `_Layout.cshtml`**:  
  - Ukládá se do složky `Pages/Shared/`.  
  - Obsahuje společné HTML, CSS a Razor direktivy.  
  - Místo pro obsah stránky se označí pomocí `@RenderBody()`.  

- **Příklad základního layoutu**:  
  ```html
  <!DOCTYPE html>
  <html>
  <head>
      <title>@ViewData["Title"] – Moje aplikace</title>
      <link rel="stylesheet" href="~/css/site.css" />
  </head>
  <body>
      <header>
          <h1>Můj web</h1>
          <nav>
              <a asp-page="/Index">Domů</a>
              <a asp-page="/Contact">Kontakt</a>
          </nav>
      </header>

      <main>
          @RenderBody()
      </main>

      <footer>
          &copy; @DateTime.Now.Year
      </footer>

      @await RenderSectionAsync("Scripts", required: false)
  </body>
  </html>
  ```

---

#### **3. Použití layoutu ve stránkách**  

- **Výchozí layout**:  
  - Nastaví se v `_ViewStart.cshtml` (soubor v kořenové složce `Pages/`):  
    ```html
    @{
        Layout = "_Layout";
    }
    ```

- **Vlastní layout pro konkrétní stránku**:  
  - Přepsání vazby přímo ve stránce:  
    ```html
    @page
    @model IndexModel
    @{
        Layout = "/Pages/Shared/_CustomLayout.cshtml";
    }
    ```

---

#### **4. Sekce (Sections)**  

- **Co jsou sekce?**  
  Volitelné bloky kódu, které layout umožňuje stránkám doplnit (např. skripty nebo specifické CSS).  

- **Definice sekce v layoutu**:  
  ```html
  @RenderSection("NázevSekce", required: false)
  ```

- **Přidání sekce ve stránce**:  
  ```html
  @section Scripts {
      <script>
          alert("Ahoj z Index stránky!");
      </script>
  }
  ```

---

#### **5. Předávání dat do layoutu**  

- **Pomocí `ViewData` nebo `ViewBag`**:  
  - V layoutu:  
    ```html
    <title>@ViewData["Title"] – Moje aplikace</title>
    ```
  - Ve stránce:  
    ```csharp
    public class IndexModel : PageModel
    {
        public void OnGet()
        {
            ViewData["Title"] = "Domovská stránka";
        }
    }
    ```

---

#### **6. Vnořené layouty**  

- **Vytvoření hierarchie**:  
  Layout může sám dědit z jiného layoutu.  
  - Příklad (`_AdminLayout.cshtml`):  
    ```html
    @{
        Layout = "_Layout";
    }

    <div class="admin-panel">
        @RenderBody()
    </div>
    ```

---

#### **7. Doporučené postupy**  

- **Udržujte layout jednoduchý**: Společné prvky (menu, zápatí) by měly být statické.  
- **Používejte sekce pro skripty a styly**: Zabráníte duplikaci kódu.  
- **Oddělte logiku od prezentace**: Data pro layout předávejte přes `ViewData`, ne přímo v `PageModel`.  
- **Využívejte `_ViewImports.cshtml`**: Pro globální direktivy (např. `@using`, `@addTagHelper`).  

---

#### **8. Časté problémy**  

- **Zapomenuté `@RenderBody()`**: Obsah stránky se nezobrazí.  
- **Nesprávná cesta k layoutu**: Chyba typu "The layout view '_Layout' could not be found".  
- **Neexistující sekce**: Pokud je `required: true` a sekce není definována, vyvolá se výjimka.  

---

#### **9. Shrnutí**  

- **Layout** zajišťuje jednotný vzhled napříč stránkami.  
- **`@RenderBody()`** vloží obsah konkrétní stránky.  
- **Sekce** umožňují rozšíření layoutu o specifické části (skripty, CSS).  
- **`ViewData`** slouží k dynamickému předávání dat (např. titulek stránky).  

---

#### **10. Užitečné zdroje**  

- Dokumentace: [Layout v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/mvc/views/layout)  
- Tutoriál: [Vytvoření layoutu](https://learn.microsoft.com/cs-cz/aspnet/core/tutorials/razor-pages/page)  
