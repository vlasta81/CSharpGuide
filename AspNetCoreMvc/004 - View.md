
## **Přehled View v ASP.NET Core MVC**  

Role, struktura a funkce zobrazení v architektuře MVC.

---

### **1. Co je View?** 

- **Prezentační vrstva** aplikace – generuje HTML obsah pro uživatele.  
- Používá **Razor syntax** (kombinace HTML a C#) pro dynamické zobrazení dat.  
- **Nezahrnuje obchodní logiku** – pouze zobrazuje data předaná z kontroleru.  
- Soubory mají příponu **`.cshtml`** a nacházejí se ve složce `Views`.

---

### **2. Role View**  

- **Vizuální reprezentace dat** z modelu.  
- **Interakce s uživatelem**: Formuláře, tlačítka, odkazy.  
- **Použití layoutů**: Konzistentní vzhled napříč stránkami (např. hlavička, patička).  
- **Validace uživatelských vstupů**: Zobrazení chybových zpráv.  

---

### **3. Struktura a konvence**  

- **Složka `Views`**:  
  - Podsložky podle názvů kontrolerů (např. `Views/Home/Index.cshtml`).  
  - `Shared`: Sdílená zobrazení (layouty, partial views).  
- **Konvence pojmenování**:  
  - View pro akci `Index` kontroleru `HomeController` → `Views/Home/Index.cshtml`.  
- **Layouty**: Definují společnou strukturu stránky (master page) → `_Layout.cshtml`.  

---

### **4. Razor Syntax**  

- **Kombinace HTML a C#**:  
  - `@` pro vložení C# kódu (např. `@Model.Jmeno`).  
  - Bloky kódu: `@{ ... }`.  
- **Direktivy**:  
  - `@model` – určuje typ předaného modelu (např. `@model Uzivatel`).  
  - `@using` – importuje namespaces.  
- **Podmínky a cykly**:  
  ```html  
  @if (Model.Pocet > 0)  
  {  
      <p>Počet: @Model.Pocet</p>  
  }  
  ```  

---

### **5. Předávání dat do View**  

1. **Silně typovaný model** (doporučeno):  
   ```csharp  
   // Kontroler  
   public IActionResult Detail(int id)  
   {  
       var produkt = _service.ZiskejProdukt(id);  
       return View(produkt);  
   }  
   ```  
   ```html  
   @model Produkt  
   <h1>@Model.Nazev</h1>  
   ```  

2. **ViewData/ViewBag**:  
   - `ViewData["Nazev"] = "Vitajte";` (přístup přes `@ViewData["Nazev"]`).  
   - `ViewBag.Nazev = "Vitajte";` (dynamický přístup přes `@ViewBag.Nazev`).  

---

### **6. Layouty a sdílené komponenty**  

- **Layout**:  
  ```html  
  <!-- _Layout.cshtml -->  
  <!DOCTYPE html>  
  <html>  
  <head>  
      <title>@ViewBag.Title</title>  
  </head>  
  <body>  
      @RenderBody()  
  </body>  
  </html>  
  ```  
  - Použití v View: `@{ Layout = "_Layout"; }`.  

- **Partial View**:  
  - Částečné zobrazení pro opakující se prvky (např. formulář pro komentáře).  
  - Vložení: `@await Html.PartialAsync("_Komentare.cshtml")`.  

- **View Components**:  
  - Složitější komponenty s vlastní logikou (např. menu, košík).  
  - Volání: `@await Component.InvokeAsync("Kosik")`.  

---

### **7. Formuláře a validace**  

- **Tag Helpers**: Zjednodušují tvorbu formulářů:  
  ```html  
  <form asp-action="Vytvor" method="post">  
      <input asp-for="Jmeno" />  
      <span asp-validation-for="Jmeno"></span>  
      <button type="submit">Odeslat</button>  
  </form>  
  ```  
- **Validace**:  
  - Chyby se zobrazí pomocí `asp-validation-for`.  
  - Stav validace: `@Html.ValidationSummary()`.

---

### **8. Příklady kódu**  

#### **Silně typované View**  

```html  
@model List<Produkt>  
<h2>Seznam produktů</h2>  
<ul>  
@foreach (var produkt in Model)  
{  
    <li>@produkt.Nazev – @produkt.Cena Kč</li>  
}  
</ul>  
```  

#### **Partial View (`_InfoPanel.cshtml`)**  

```html  
<div class="info-panel">  
    <h3>@ViewData["Nadpis"]</h3>  
    <p>@ViewData["Obsah"]</p>  
</div>  
```  

---

### **9. Výhody a nevýhody**  

| **Výhody**                                  | **Nevýhody**                          |  
|---------------------------------------------|----------------------------------------|  
| Oddělení logiky a prezentace                | Omezená interaktivita bez JavaScriptu  |  
| Razor syntax je čitelná a flexibilní        | Náročnější debugování                 |  
| Podpora moderních komponent (View Components)|                                       |  

---

### **10. Doporučené postupy**  

- **Minimalizujte logiku v View**: Používejte služby nebo View Components.  
- **Využívejte Layouty**: Pro konzistentní design.  
- **Silné typování**: Vyhněte se `ViewBag` pro komplexní data.  
- **Cacheování**: Urychlení pomocí `@Html.Cache()` pro statické části.  

---

### **11. Shrnutí**  

- View je **prezentační vrstva** pro zobrazení dat uživateli.  
- **Razor syntax** umožňuje kombinovat HTML s C#.  
- **Layouty a partial views** zajišťují opakovatelnost kódu.  
- **Tag Helpers** zjednodušují tvorbu formulářů a validaci.  
- Dodržování best practices zlepšuje **udržovatelnost a výkon**.  

--- 

**Doporučené zdroje**:  
- Oficiální dokumentace: *Views in ASP.NET Core MVC*  
