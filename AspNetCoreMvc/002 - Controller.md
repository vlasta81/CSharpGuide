
## **Přehled kontrolerů v ASP.NET Core MVC**  

Základní principy, struktura a funkce kontrolerů.

---

### **1. Co je kontroler?**  

- **Třída** odvozená od `ControllerBase` nebo `Controller`, která zpracovává HTTP požadavky.  
- Hlavní komponenta architektury MVC: **řídí tok aplikace**, komunikuje s modelem a vrací odpověď (např. View, JSON, redirect).  
- Každá veřejná metoda v kontroleru se nazývá **akční metoda** (action method) a mapuje na konkrétní URL.

---

### **2. Role kontroleru**  

- **Zpracování vstupů**: Čte data z požadavku (parametry URL, formuláře, cookies).  
- **Volání obchodní logiky**: Interakce s modelem (např. načtení dat z databáze).  
- **Výběr view**: Rozhoduje, které UI se zobrazí, a předává mu data.  
- **HTTP odpověď**: Vrací výsledek (HTML, JSON, redirect, status code).

---

### **3. Struktura kontroleru**  

- **Název třídy**: Končí příponou `Controller` (např. `HomeController`).  
- **Akční metody**: Veřejné metody s libovolným názvem (např. `Index()`, `Create()`).  
- **Atributy**: Upřesňují chování (např. `[HttpGet]`, `[Authorize]`).  
- **Závislosti**: Injektované přes konstruktor (Dependency Injection).

---

### **4. Vytvoření kontroleru**  

#### **a) Ruční vytvoření**  

```csharp  
public class UzivateleController : Controller  
{  
    public IActionResult Index()  
    {  
        return View();  
    }  
}  
```  

#### **b) Scaffolding**  

- Generování kódu přes Visual Studio nebo CLI pomocí šablon (např. pro CRUD operace).

---

### **5. Routing a akční metody**  

- **Konvenční routing**: Definován v `Program.cs` (např. `"{controller=Home}/{action=Index}/{id?}"`).  
- **Attribute routing**: Přesné určení cesty pomocí atributů:  
  ```csharp  
  [Route("produkty")]  
  public class ProduktyController : Controller  
  {  
      [HttpGet("seznam")]  
      public IActionResult ZobrazSeznam() { ... }  
  }  
  ```  

---

### **6. Typy návratových hodnot akčních metod**  

- **ViewResult**: Vrátí HTML (např. `return View();`).  
- **JsonResult**: Vrátí JSON data (`return Json(data);`).  
- **RedirectResult**: Přesměruje na jinou URL (`return RedirectToAction("Index");`).  
- **StatusCodeResult**: Vrátí HTTP status kód (`return NotFound();`).  

---

### **7. Práce s parametry**  

- Data lze předávat přes:  
  - **Route parametry** (např. `/uzivatele/5` → `public IActionResult Detail(int id)`).  
  - **Query string** (např. `?jmeno=Jan` → `public IActionResult Hledat(string jmeno)`).  
  - **Tělo požadavku** (např. formuláře → `[FromBody] Uzivatel uzivatel`).  

#### Příklad:  
```csharp  
[HttpPost]  
public IActionResult Vytvor([FromForm] Uzivatel novyUzivatel)  
{  
    // Zpracování dat z formuláře  
}  
```  

---

### **8. Dependency Injection v kontrolerech**  

- **Konstruktorová injekce**:  
  ```csharp  
  public class UzivateleController : Controller  
  {  
      private readonly IUzivatelService _service;  

      public UzivateleController(IUzivatelService service)  
      {  
          _service = service;  
      }  

      public IActionResult Index()  
      {  
          var uzivatele = _service.ZiskejVsechny();  
          return View(uzivatele);  
      }  
  }  
  ```  

---

### **9. Filtry (Filters)**  

- Atributy pro přidání funkcionality před/po provedení akční metody:  
  - `[Authorize]`: Omezení přístupu pro přihlášené uživatele.  
  - `[ValidateAntiForgeryToken]`: Ochrana proti CSRF útokům.  
  - `[HandleError]`: Zachytávání výjimek.  

---

### **10. Testování kontrolerů**  

- **Unit testy**: Testování akčních metod pomocí frameworků (xUnit, NUnit).  
- **Mockování služeb**: Použití knihoven jako Moq pro simulaci závislostí.  

#### Příklad testu:  

```csharp  
[Fact]  
public void Index_Vraci_View_S_SeznamemUzivatelu()  
{  
    var mockService = new Mock<IUzivatelService>();  
    var controller = new UzivateleController(mockService.Object);  

    var result = controller.Index();  
    Assert.IsType<ViewResult>(result);  
}  
```  

---

### **11. Omezení a doporučení**  

- **Tenké kontrolery**: Logiku přesouvejte do služeb nebo modelů.  
- **Asynchronní metody**: Pro dlouhé operace používejte `async`/`await`.  
- **Validace vstupů**: Vždy ověřujte data z požadavku.  

---

### **12. Controller vs. ControllerBase**  

- **`Controller`**: Používá se pro MVC (podpora View, Razor).  
- **`ControllerBase`**: Základ pro API kontrolery (bez podpory View).  

---

### **13. Ukázkový kód**  

```csharp  
[Authorize]  
public class ObjednavkyController : Controller  
{  
    private readonly IObjednavkyService _service;  

    public ObjednavkyController(IObjednavkyService service)  
    {  
        _service = service;  
    }  

    [HttpGet("moje-objednavky")]  
    public async Task<IActionResult> MojeObjednavky()  
    {  
        var objednavky = await _service.ZiskejMojeObjednavky(User.Identity.Name);  
        return View(objednavky);  
    }  

    [HttpPost]  
    [ValidateAntiForgeryToken]  
    public IActionResult Zrusit(int id)  
    {  
        _service.ZrusitObjednavku(id);  
        return RedirectToAction("MojeObjednavky");  
    }  
}  
```  

---

### **14. Shrnutí**  

- Kontrolery jsou **jádrem řízení toku** v ASP.NET Core MVC.  
- Akční metody mapují na URL a vracejí odpovědi.  
- Dependency Injection zajišťuje **modularitu a testovatelnost**.  
- Atributy a filtry rozšiřují funkcionalitu (bezpečnost, validace).  
- Dodržování best practices zlepšuje údržbu kódu (tenké kontrolery, async operace).  

--- 

**Doporučené zdroje**:  
- Oficiální dokumentace Microsoft: *Controllers in ASP.NET Core MVC*  
