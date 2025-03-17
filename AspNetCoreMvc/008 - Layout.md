
## **Přehled Layoutu (Rozložení) v ASP.NET Core MVC**  

Vytváření konzistentního vzhledu stránek pomocí layoutů.

---

### **1. Co je Layout?**  

- **Hlavní šablona** (master page), která definuje **společnou strukturu všech stránek** aplikace (např. hlavička, patička, menu).  
- Umožňuje **opakované použití kódu** a zajišťuje konzistenci UI.  
- Soubory mají příponu `.cshtml` a nacházejí se ve složce `Views/Shared` (výchozí název: `_Layout.cshtml`).  

---

### **2. Role Layoutu**  

- **Definuje základní HTML kostru** (DOCTYPE, `<head>`, `<body>`).  
- Obsahuje **sdílené zdroje**: CSS, JavaScript, logo, navigaci.  
- Určuje **místo pro dynamický obsah** (pomocí `@RenderBody()`).  
- Podporuje **sekce** (např. pro skripty specifické pro konkrétní stránku).  

---

### **3. Struktura Layoutu**  

#### **Základní příklad (`_Layout.cshtml`):**  

```html  
<!DOCTYPE html>  
<html>  
<head>  
    <meta charset="utf-8" />  
    <title>@ViewBag.Title – Moje Aplikace</title>  
    <link rel="stylesheet" href="~/css/site.css" />  
</head>  
<body>  
    <header>  
        <partial name="_Header" /> <!-- Částečné zobrazení pro hlavičku -->  
    </header>  

    <main>  
        @RenderBody() <!-- Zde se vloží obsah konkrétní View -->  
    </main>  

    <footer>  
        <partial name="_Footer" />  
    </footer>  

    @RenderSection("Scripts", required: false) <!-- Volitelná sekce pro skripty -->  
</body>  
</html>  
```  

---

### **4. Použití Layoutu ve View**  

- **Automaticky** přes `_ViewStart.cshtml`:  
  ```html  
  @{  
      Layout = "_Layout";  
  }  
  ```  
  (Tento soubor se nachází ve složce `Views` a aplikuje se na všechny Views.)  

- **Ruční přepsání** v konkrétní View:  
  ```html  
  @{  
      Layout = "~/Views/Shared/JinyLayout.cshtml";  
  }  
  ```  

---

### **5. Sekce (Sections)**  

- **Definice v Layoutu**:  
  ```html  
  @RenderSection("NazevSekce", required: false)  
  ```  
  - `required`: Určuje, zda je sekce povinná.  

- **Implementace ve View**:  
  ```html  
  @section NazevSekce {  
      <script src="~/js/muj-script.js"></script>  
  }  
  ```  

---

### **6. ViewStart a ViewImports**  

- **`_ViewStart.cshtml`**:  
  - Spustí se před každou View.  
  - Nastaví výchozí Layout nebo globální proměnné.  

- **`_ViewImports.cshtml`**:  
  - Importuje namespaces a Tag Helpers pro všechny Views.  
  - Příklad:  
    ```html  
    @using MojeAplikace.Models  
    @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers  
    ```  

---

### **7. Dynamické změny v Layoutu**  

- **Předání dat z View do Layoutu**:  
  - Pomocí `ViewBag` nebo `ViewData`:  
    ```csharp  
    // V akci kontroleru  
    ViewBag.Title = "Domovská stránka";  
    ```  
    ```html  
    <!-- V Layoutu -->  
    <title>@ViewBag.Title – Moje Aplikace</title>  
    ```  

---

### **8. Výhody a nevýhody**  

| **Výhody**                                  | **Nevýhody**                          |  
|---------------------------------------------|----------------------------------------|  
| Konzistentní vzhled napříč stránkami        | Při častých změnách nutná úprava všech Views |  
| Snadná údržba sdílených prvků               | Omezená flexibilita pro unikátní stránky |  
| Podpora sekcí pro specifické zdroje         |                                       |  

---

### **9. Doporučené postupy** 

1. **Oddělte logiku od prezentace**: Layout by neměl obsahovat obchodní logiku.  
2. **Využívejte částečná zobrazení**: Pro opakující se prvky (např. menu, patičku).  
3. **Optimalizujte načítání zdrojů**:  
   - CSS a JavaScript pro celou aplikaci vložte do Layoutu.  
   - Stránkové zdroje vkládejte přes sekce.  
4. **Respektujte hierarchii**: Vnořené Layouty jsou možné, ale komplikují údržbu.  

---

### **10. Příklady**  

#### **Vnořený Layout**  

```html  
<!-- _AdminLayout.cshtml -->  
@{  
    Layout = "_Layout";  
}  

@section Styles {  
    <link rel="stylesheet" href="~/css/admin.css" />  
}  

<div class="admin-panel">  
    @RenderBody()  
</div>  
```  

#### **View s vlastní sekcí**  

```html  
@model Produkt  
@{  
    ViewBag.Title = "Detail produktu";  
}  

<h1>@Model.Nazev</h1>  

@section Scripts {  
    <script>  
        console.log("Toto je script pro detail produktu.");  
    </script>  
}  
```  

---

### **11. Časté chyby**  

- **Zapomenuté `@RenderBody()`**: Obsah View se nezobrazí.  
- **Neexistující sekce**: Pokud je sekce označena jako `required: true`, vyvolá chybu.  
- **Špatné cesty k Layoutu**: Používejte absolutní cesty (např. `~/Views/Shared/_Layout.cshtml`).  

---

### **12. Shrnutí**  

- Layout je **základní šablonou pro konzistentní UI**.  
- **`@RenderBody()`** vkládá obsah konkrétní View.  
- **Sekce** umožňují rozšíření Layoutu o stránkové zdroje.  
- **`_ViewStart` a `_ViewImports`** zjednodušují globální konfiguraci.  
- Dodržování best practices zajišťuje **čistý a udržitelný kód**.  

--- 

**Doporučené zdroje**:  
- Oficiální dokumentace: *Layout in ASP.NET Core MVC*  
