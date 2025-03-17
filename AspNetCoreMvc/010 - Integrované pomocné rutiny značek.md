
## **Přehled integrovaných pomocných rutin značek (Tag Helpers) v ASP.NET Core MVC**  

Využití serverových funkcí přímo v HTML pro dynamické generování obsahu.

---

### **1. Co jsou Tag Helpers?**  

- **Rozšíření HTML elementů**, které umožňují integraci serverové logiky do HTML.  
- **Výhody oproti HTML Helpers**:  
  - Přirozenější syntaxe (podobná čistému HTML).  
  - Lepší podpora nástrojů (IntelliSense, debugování).  
  - Snadnější čitelnost a údržba kódu.  

---

### **2. Role Tag Helpers**  

- **Generování dynamického HTML** na základě modelu nebo serverové logiky.  
- **Zjednodušení tvorby formulářů**, odkazů a validací.  
- **Automatizace opakujících se úloh** (např. anti-forgery tokeny ve formulářích).  

---

### **3. Aktivace Tag Helpers**  

- Přidejte direktivu do `_ViewImports.cshtml`:  
  ```html  
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers  
  ```  
- Tím se zpřístupní všechny vestavěné Tag Helpers.

---

### **4. Přehled základních Tag Helpers**  

#### **a) Formuláře a vstupy**  

- **`<form>`**:  
  ```html  
  <form asp-controller="Home" asp-action="Ulozit" method="post">  
      <!-- Obsah formuláře -->  
  </form>  
  ```  
  - Automaticky přidá anti-forgery token.  

- **`<input>` a `<label>`**:  
  ```html  
  <label asp-for="Jmeno"></label>  
  <input asp-for="Jmeno" class="form-control" />  
  ```  
  - Generuje název a validaci podle modelu.  

- **`<select>`**:  
  ```html  
  <select asp-for="KategorieId" asp-items="Model.Kategorie"></select>  
  ```  

#### **b) Validace**  

- **`<span>` pro chybové zprávy**:  
  ```html  
  <span asp-validation-for="Email" class="text-danger"></span>  
  ```  

- **`<div>` pro souhrn chyb**:  
  ```html  
  <div asp-validation-summary="All"></div>  
  ```  

#### **c) Odkazy a navigace**  

- **`<a>`**:  
  ```html  
  <a asp-controller="Produkty" asp-action="Detail" asp-route-id="5">Detail</a>  
  ```  
  - Generuje URL podle routování.  

- **`<img>`**:  
  ```html  
  <img asp-append-version="true" src="~/images/logo.png" />  
  ```  
  - Přidá hash pro invalidaci cache (cache busting).  

#### **d) Podmíněné generování**  

- **`<environment>`**:  
  ```html  
  <environment include="Development">  
      <script src="~/js/debug.js"></script>  
  </environment>  
  <environment exclude="Development">  
      <script src="~/js/production.min.js"></script>  
  </environment>  
  ```  

---

### **5. Pokročilé Tag Helpers**  

- **`<cache>`**: Ukládá část stránky do cache.  
  ```html  
  <cache expires-after="@TimeSpan.FromMinutes(10)">  
      @DateTime.Now <!-- Zobrazí se jen jednou za 10 minut -->  
  </cache>  
  ```  

- **`<partial>`**: Vkládá částečné zobrazení.  
  ```html  
  <partial name="_LoginPartial" model="Model.User" />  
  ```  

---

### **6. Výhody a nevýhody**  

| **Výhody**                                  | **Nevýhody**                          |  
|---------------------------------------------|----------------------------------------|  
| Čitelnost podobná HTML                      | Vyžaduje znalost specifických atributů |  
| Integrace s modelem a validací              | Omezená podpora ve starších editorech  |  
| Automatizace bezpečnostních funkcí          |                                       |  

---

### **7. Porovnání s HTML Helpers**  

| **Funkce**               | **Tag Helpers**                     | **HTML Helpers**                  |  
|--------------------------|-------------------------------------|-----------------------------------|  
| **Syntaxe**              | `<form asp-action="Ulozit">`       | `@Html.BeginForm("Ulozit", ...)` |  
| **Čitelnost**            | Přirozenější (HTML-like)           | Směs C# a HTML                   |  
| **Podpora nástrojů**     | Lepší IntelliSense                 | Omezená                          |  

---

### **8. Příklady použití**  

#### **Formulář s validací**  

```html  
<form asp-action="Vytvor" method="post">  
    <div class="form-group">  
        <label asp-for="Email"></label>  
        <input asp-for="Email" class="form-control" />  
        <span asp-validation-for="Email" class="text-danger"></span>  
    </div>  
    <button type="submit">Odeslat</button>  
</form>  
```  

#### **Dynamický odkaz**  

```html  
<a asp-controller="Blog"  
   asp-action="Clanek"  
   asp-route-id="@Model.ClanekId"  
   asp-route-slug="@Model.Slug">  
    Přečíst článek  
</a>  
```  
→ Vygeneruje URL: `/Blog/Clanek/5?slug=uvod-do-mvc`.  

---

### **9. Doporučené postupy** 

- **Používejte `asp-for`**: Pro vazbu na vlastnosti modelu.  
- **Vyhněte se mixu Tag Helpers a HTML Helpers**: Zůstaňte konzistentní.  
- **Využívejte IntelliSense**: Pro objevování dostupných atributů.  

---

### **10. Shrnutí**  

- Tag Helpers **zkracují vývoj** díky intuitivní syntaxi.  
- **Automatizují běžné úlohy** (validace, routování, cache).  
- Jsou klíčové pro **moderní a udržitelný kód** v ASP.NET Core MVC.  

--- 

**Doporučené zdroje**:  
- Oficiální dokumentace: *Tag Helpers in ASP.NET Core*  
