
### Router v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Co je router?**  
  Router (směrovač) v ASP.NET Core mapuje **URL adresy** na konkrétní stránky (Razor Pages).  
  - **Účel**: Určit, která stránka se zobrazí na základě URL a parametrů.  
  - **Typy routování**:  
    - **Konvenční routování**: Automatické mapování podle struktury složek `Pages`.  
    - **Vlastní routování**: Explicitní definice pomocí direktivy `@page`.  

---

#### **2. Konvenční routování**  

- **Základní princip**:  
  - Stránky v složce `Pages` odpovídají URL cestám.  
  - Příklad:  
    - `Pages/Index.cshtml` → `/`  
    - `Pages/Products/Index.cshtml` → `/Products`  
    - `Pages/Products/Detail.cshtml` → `/Products/Detail`  

- **Výchozí chování**:  
  - Název souboru `Index.cshtml` se v URL vynechává.  
  - Podsložky se promítají do URL hierarchie.  

---

#### **3. Vlastní routování pomocí `@page`**  

- **Definice vlastní cesty**:  
  Direktiva `@page` umožňuje přepsat výchozí cestu.  
  ```html
  @page "/produkty/{id}"
  @model ProductDetailModel
  ```

- **Parametry v cestě**:  
  - Zachycení hodnot z URL do vlastností `PageModel`.  
  - Příklad:  
    ```csharp
    public void OnGet(int id)
    {
        // id se načte z URL /produkty/5
    }
    ```

---

#### **4. Parametry a omezení (Constraints)**  

- **Typy parametrů**:  
  - **Povinné**: `{id}` → URL musí obsahovat hodnotu.  
  - **Volitelné**: `{id?}` → Parametr může chybět.  
  - **Výchozí hodnota**: `{id=10}` → Pokud parametr chybí, použije se 10.  

- **Omezení datových typů**:  
  - Formát: `{parametr:typ}` (např. `{id:int}`).  
  - Podporované typy: `int`, `bool`, `datetime`, `regex(...)`, apod.  
  - Příklad:  
    ```html
    @page "/produkty/{id:int}"
    ```

---

#### **5. Generování URL pomocí Tag Helpers**  

- **Odkazy na stránky**:  
  - Tag Helper `asp-page` generuje URL podle názvu stránky.  
  - Příklad:  
    ```html
    <a asp-page="/Products/Detail" asp-route-id="5">Detail produktu</a>
    <!-- Vygeneruje: <a href="/produkty/5">... -->
    ```

- **Parametry v odkazu**:  
  - `asp-route-*` přidá parametry do URL.  
  - Příklad:  
    ```html
    <a asp-page="/Search" asp-route-query="ASP.NET">Hledat</a>
    <!-- Vygeneruje: <a href="/Hledat?query=ASP.NET">... -->
    ```

---

#### **6. Pokročilé routování**  

- **Více parametrů v cestě**:  
  ```html
  @page "/blog/{year:int}/{slug}"
  ```

- **Kombinace s query string**:  
  - Parametry v URL i query string lze kombinovat.  
  - Příklad URL: `/blog/2023/clanek?page=2`.  

- **SEO-friendly URL (Slug)**:  
  - Čitelné cesty s klíčovými slovy.  
  - Příklad: `/blog/2023/jak-na-razor-pages`.  

---

#### **7. Oblast (Areas)**  

- **Organizace rozsáhlých aplikací**:  
  - Stránky se seskupují do samostatných oblastí (např. `Admin`, `Customer`).  
  - Routování: `/Admin/Users` → `Areas/Admin/Pages/Users/Index.cshtml`.  

- **Registrace oblastí**:  
  ```csharp
  builder.Services.AddRazorPages().AddRazorPagesOptions(options =>
      options.Conventions.AddAreaFolderRouteModel("Admin", "/", "/Admin/{0}"));
  ```

---

#### **8. Ladění routování**  

- **Developer Exception Page**:  
  - Zobrazuje podrobnosti o neshodě routy.  
  - Aktivuje se v `Program.cs`:  
    ```csharp
    app.UseDeveloperExceptionPage();
    ```

---

#### **9. Časté problémy**  

- **404 Not Found**:  
  - Chybějící `@page` direktiva v souboru `.cshtml`.  
  - Nesprávný název stránky nebo složky.  
- **Konflikty rout**:  
  - Dvě stránky se stejnou cestou.  
  - Řešení: Upřesněte parametry nebo upravte direktivu `@page`.  

---

#### **10. Shrnutí**  

- **Konvenční routování** využívá strukturu složek `Pages`.  
- **Vlastní cesty** se definují přes `@page "/cesta"`.  
- **Parametry a omezení** zajišťují validaci vstupů.  
- **Tag Helpers** usnadňují generování odkazů.  

---

#### **11. Užitečné zdroje**  

- Dokumentace: [Routování v Razor Pages](https://learn.microsoft.com/cs-cz/aspnet/core/razor-pages/?view=aspnetcore-8.0&tabs=visual-studio#routing)  
- Tutoriál: [Vlastní routování](https://learn.microsoft.com/cs-cz/aspnet/core/tutorials/razor-pages/da1)  
- Ukázky: [ASP.NET Core GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/razor-pages)  
