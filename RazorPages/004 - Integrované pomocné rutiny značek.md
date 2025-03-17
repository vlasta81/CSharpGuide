
### Integrované pomocné rutiny značek (Tag Helpers) v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Co jsou Tag Helpers?**  
  Pomocné rutiny značek (Tag Helpers) jsou komponenty v ASP.NET Core, které umožňují **dynamické generování HTML** na straně serveru.  
  - **Využívají standardní HTML syntax** (např. `<input>`, `<form>`), ale rozšiřují ji o serverovou logiku (např. vazbu na model, routování).  
  - **Nahrazují tradiční HTML Helpers** (jako `@Html.TextBoxFor`), ale jsou čitelnější a intuitivnější.  
  - **Integrované Tag Helpers** jsou součástí ASP.NET Core a nevyžadují instalaci dalších balíčků.

---

#### **2. Klíčové vlastnosti**  

- **HTML-like syntax**: Připomínají standardní HTML, ale přidávají atributy s prefixem `asp-*` (např. `asp-for`, `asp-page`).  
- **IntelliSense podpora**: Fungují v editoru (Visual Studio, VS Code).  
- **Vazba na model**: Automatické mapování hodnot z modelu do HTML elementů.  
- **Generování URL**: Dynamické vytváření cest na základě routování.  
- **Validace**: Integrace s validačními atributy modelu.  

---

#### **3. Nejčastěji používané Tag Helpers**  

##### **a) Formuláře a vazba na model** 

- **`asp-for`**:  
  - Váže vlastnost modelu na HTML element (např. `<input>`).  
  - Příklad:  
    ```html
    <input asp-for="User.Email" /> <!-- Generuje input s name="User.Email" a validačními atributy -->
    ```

- **`asp-validation-for`**:  
  - Zobrazí validační chybu pro konkrétní vlastnost.  
  - Příklad:  
    ```html
    <span asp-validation-for="User.Email" class="text-danger"></span>
    ```

- **`asp-antiforgery`**:  
  - Automaticky přidá antiforgery token do formuláře (pro ochranu proti CSRF útokům).  

##### **b) Routování a odkazy**  

- **`asp-page`**:  
  - Generuje URL k Razor Page.  
  - Příklad:  
    ```html
    <a asp-page="/Contact">Kontakt</a> <!-- Odkaz na stránku Contact.cshtml -->
    ```

- **`asp-page-handler`**:  
  - Určuje handler metody v `PageModel` (např. `OnPostDelete`).  
  - Příklad:  
    ```html
    <form method="post" asp-page-handler="Delete">
        <button type="submit">Smazat</button>
    </form>
    ```

##### **c) Ostatní**  

- **`asp-route-*`**:  
  - Přidá parametry do URL (např. `asp-route-id="5"` → `/page/5`).  
- **`asp-controller`/`asp-action`**:  
  - Používá se v kombinaci s MVC, ale v Razor Pages méně časté.  
- **`asp-items`**:  
  - Generuje `<option>` prvky pro `<select>` z kolekce (např. ze seznamu rolí).  

---

#### **4. Jak používat Tag Helpers**  

1. **Povolení v projektu**:  
   Do souboru `_ViewImports.cshtml` přidejte:  
   ```html
   @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
   ```  
2. **Použití v Razor View**:  
   ```html
   <!-- Příklad formuláře s vazbou na model -->
   <form method="post">
       <div class="form-group">
           <label asp-for="User.Name"></label>
           <input asp-for="User.Name" class="form-control" />
           <span asp-validation-for="User.Name"></span>
       </div>
       <button type="submit">Uložit</button>
   </form>
   ```

---

#### **5. Výhody oproti HTML Helpers**  

- **Čitelnější kód**: Blíží se čistému HTML.  
- **Lepší podpora nástrojů**: IntelliSense a automatické dokončování.  
- **Snadná integrace s CSS/JS frameworky**: Např. Bootstrap.  
- **Více funkcionality**: Automatická validace, antiforgery tokeny.  

---

#### **6. Doporučené postupy**  

- **Vždy používejte `asp-for` pro vazbu na model** místo ručního psaní `name` a `id`.  
- **Kombinujte s validačními atributy**:  
  ```csharp
  public class UserModel : PageModel
  {
      [BindProperty]
      [Required(ErrorMessage = "Jméno je povinné")]
      public string Name { get; set; }
  }
  ```
- **Pro složité scénáře vytvářejte vlastní Tag Helpers** (např. pro specifické UI komponenty).  

---

#### **7. Příklady v praxi**  

##### **Select lista s `asp-items`**  

```html
<select asp-for="SelectedRole" 
        asp-items="Model.Roles" 
        class="form-control">
    <option value="">Vyberte roli</option>
</select>
```
##### **Odkaz s parametrem**  

```html
<a asp-page="/Details" asp-route-id="10">Detaily uživatele 10</a>
<!-- Vygeneruje: <a href="/Details/10">... -->
```

---

#### **8. Časté chyby**  

- **Zapomenutý `@addTagHelper`**: Tag Helpers nebudou fungovat.  
- **Chybějící `[BindProperty]` v `PageModel`**: Data z formuláře se nenačtou.  
- **Nesprávné pojmenování handleru**: Např. `asp-page-handler="Delete"` vyžaduje metodu `OnPostDelete` v `PageModel`.  

---

#### **9. Shrnutí**  

- **Tag Helpers zjednodušují tvorbu UI** v Razor Pages.  
- **Hlavní výhody**: Čitelnost, integrace s modelem, automatická validace.  
- **Klíčové pomocné rutiny**: `asp-for`, `asp-page`, `asp-validation-for`.  
- **Vhodné pro**: Formuláře, dynamické odkazy, generování formulářových prvků.  

---

#### **10. Užitečné zdroje**  

- Dokumentace: [Tag Helpers v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/mvc/views/tag-helpers/intro)  
- Seznam vestavěných Tag Helpers: [Built-in Tag Helpers](https://learn.microsoft.com/cs-cz/aspnet/core/mvc/views/tag-helpers/built-in/)  
- Tutoriál: [Práce s formuláři](https://learn.microsoft.com/cs-cz/aspnet/core/tutorials/razor-pages/forms)
