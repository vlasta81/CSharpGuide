
## **Přehled metod kontroleru a zobrazení v ASP.NET Core MVC**  

Principy interakce mezi kontrolery a zobrazeními v architektuře MVC.

---

### **1. Metody kontroleru (Action Methods)**  

#### **a) Definice**  

- **Akční metody**: Veřejné metody v kontroleru, které reagují na HTTP požadavky.  
- **Mapování na URL**: Prostřednictvím routování (konvenční nebo atributové).  
- **Návratové typy**:  
  - `IActionResult` – obecný typ pro všechny odpovědi.  
  - `ViewResult` – vrací HTML (pomocí View).  
  - `JsonResult` – vrací JSON data.  
  - `RedirectResult` – přesměrování na jinou akci/URL.  

#### **b) Příklady**  

```csharp  
public class HomeController : Controller  
{  
    // GET: /Home/Index  
    public IActionResult Index()  
    {  
        return View(); // Vrátí View "Index.cshtml"  
    }  

    // POST: /Home/Ulozit  
    [HttpPost]  
    public IActionResult Ulozit(Uzivatel model)  
    {  
        if (ModelState.IsValid)  
        {  
            // Uložení dat  
            return RedirectToAction("Index");  
        }  
        return View(model); // Zobrazí formulář s chybami  
    }  

    // GET: /Home/Detaily/5  
    public IActionResult Detaily(int id)  
    {  
        var produkt = _service.ZiskejProdukt(id);  
        return View(produkt);  
    }  
}  
```  

---

### **2. Zobrazení (Views)**  

#### **a) Role zobrazení**  

- **Generují HTML obsah** na základě dat z kontroleru.  
- **Používají Razor syntax** pro kombinaci HTML a C#.  
- **Typy zobrazení**:  
  - **Silně typované** (použití `@model`).  
  - **Layouty** – společná šablona pro všechny stránky.  
  - **Partial Views** – částečná zobrazení pro opakující se prvky.  

#### **b) Předávání dat z kontroleru**  

1. **Silný model**:  
   ```csharp  
   // Kontroler  
   public IActionResult Index()  
   {  
       var model = new Uzivatel { Jmeno = "Jan" };  
       return View(model);  
   }  
   ```  
   ```html  
   <!-- View -->  
   @model Uzivatel  
   <h1>@Model.Jmeno</h1>  
   ```  

2. **ViewData/ViewBag**:  
   ```csharp  
   ViewData["Nadpis"] = "Domů";  
   ViewBag.Pocet = 10;  
   ```  
   ```html  
   <h1>@ViewData["Nadpis"]</h1>  
   <p>@ViewBag.Pocet položek</p>  
   ```  

---

### **3. Interakce mezi kontrolerem a zobrazením**  

#### **a) Formuláře a zpracování dat**  

- **Formulář ve View**:  
  ```html  
  <form asp-action="Ulozit" method="post">  
      <input asp-for="Jmeno" />  
      <button type="submit">Odeslat</button>  
  </form>  
  ```  
- **Zpracování v kontroleru**:  
  ```csharp  
  [HttpPost]  
  public IActionResult Ulozit(Uzivatel model)  
  {  
      // Validace a uložení  
  }  
  ```  

#### **b) Validace a chybové zprávy**  

- **Validace modelu**:  
  ```csharp  
  if (!ModelState.IsValid)  
  {  
      return View(model); // Zobrazí chyby ve View  
  }  
  ```  
- **Zobrazení chyb ve View**:  
  ```html  
  <span asp-validation-for="Jmeno" class="text-danger"></span>  
  ```  

---

### **4. Asynchronní metody**  

- **Zpracování dlouhých operací** (např. databázové dotazy).  
- **Příklad**:  
  ```csharp  
  public async Task<IActionResult> Index()  
  {  
      var data = await _service.NactiDataAsync();  
      return View(data);  
  }  
  ```  

---

### **5. Časté návratové typy metod kontroleru**  

| **Typ**              | **Popis**                                  | **Příklad**                          |  
|-----------------------|--------------------------------------------|---------------------------------------|  
| `View()`             | Vrátí HTML View                          | `return View(model);`                |  
| `RedirectToAction()` | Přesměruje na jinou akci                 | `return RedirectToAction("Index");`  |  
| `Json()`             | Vrátí JSON data                          | `return Json(new { success = true });` |  
| `File()`             | Vrátí soubor (PDF, obrázek)              | `return File(bytes, "image/png");`   |  
| `NotFound()`         | Vrátí HTTP 404                           | `return NotFound();`                 |  

---

### **6. Best Practices**  

- **Tenké kontrolery**: Logiku přesouvejte do služeb.  
- **Silné typování modelů**: Minimalizujte použití `ViewBag`.  
- **Asynchronní operace**: Pro I/O operace (databáze, API).  
- **Validace vstupů**: Vždy ověřujte data z formulářů.  

---

### **7. Shrnutí**  

- **Metody kontroleru** zpracovávají požadavky a vracejí odpovědi.  
- **Zobrazení** generují HTML pomocí Razor syntaxe a dat z modelu.  
- **Formuláře a validace** zajišťují interaktivitu a bezpečnost.  
- **Asynchronní metody** zlepšují výkon při dlouhých operacích.  

---

**Doporučené zdroje**:  
- Oficiální dokumentace: *Controllers and Views in ASP.NET Core MVC*  
