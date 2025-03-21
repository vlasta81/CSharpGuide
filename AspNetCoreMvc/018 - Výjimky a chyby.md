
## **Výjimky a chyby v ASP.NET Core MVC**  

Principy zpracování chyb, zachycení výjimek a konfigurace chybových stránek.

---

### **1. Úvod do zpracování chyb**  

- **Cíl**: Poskytnout uživateli srozumitelnou zpětnou vazbu při chybách a zabránit úniku citlivých informací.  
- **Typy chyb**:  
  - **HTTP chyby** (404, 500).  
  - **Výjimky** (neošetřené chyby v kódu).  

---

### **2. Výchozí zpracování výjimek**  

- **Middleware `UseExceptionHandler`**:  
  - Zachytává neošetřené výjimky a přesměruje na chybovou stránku.  
  - Konfigurace v `Program.cs`:  
    ```csharp  
    app.UseExceptionHandler("/Error"); // Přesměrování na route "/Error"  
    ```  

- **Rozdíly mezi prostředími**:  
  | **Prostředí**   | **Chování**                              |  
  |------------------|------------------------------------------|  
  | **Development**  | Zobrazí podrobné informace (včetně stack trace). |  
  | **Production**   | Zobrazí obecnou chybovou stránku.        |  

---

### **3. Vlastní chybové stránky**  

#### **a) Konfigurace pro HTTP status kódy**  

```csharp  
app.UseStatusCodePagesWithReExecute("/Chyba/{0}"); // {0} = status kód (např. 404)  
```  
- **Controller pro chyby**:  
  ```csharp  
  public class ChybaController : Controller  
  {  
      [Route("/Chyba/{statusCode}")]  
      public IActionResult Index(int statusCode)  
      {  
          ViewBag.StatusCode = statusCode;  
          return View();  
      }  
  }  
  ```  

#### **b) Chybové view**  

- Vytvořte soubor `Views/Chyba/Index.cshtml`:  
  ```html  
  @{  
      ViewData["Title"] = "Chyba " + ViewBag.StatusCode;  
  }  
  <h1>Chyba @ViewBag.StatusCode</h1>  
  <p>Došlo k neočekávané chybě.</p>  
  ```  

---

### **4. Zachycení výjimek ve filtrech**  

- **Vlastní filtr výjimek**:  
  ```csharp  
  public class VlastniExceptionFilter : IExceptionFilter  
  {  
      public void OnException(ExceptionContext context)  
      {  
          // Logování chyby  
          Console.WriteLine($"Výjimka: {context.Exception.Message}");  

          // Nastavení výsledku  
          context.Result = new ViewResult { ViewName = "CustomError" };  
          context.ExceptionHandled = true;  
      }  
  }  
  ```  

- **Registrace filtru**:  
  ```csharp  
  builder.Services.AddControllersWithViews(options =>  
      options.Filters.Add(new VlastniExceptionFilter()));  
  ```  

---

### **5. Logování chyb**  

- **Integrace s `ILogger`**:  
  ```csharp  
  public class HomeController : Controller  
  {  
      private readonly ILogger<HomeController> _logger;  

      public HomeController(ILogger<HomeController> logger)  
      {  
          _logger = logger;  
      }  

      public IActionResult Index()  
      {  
          try  
          {  
              // Kód, který může vyvolat výjimku  
          }  
          catch (Exception ex)  
          {  
              _logger.LogError(ex, "Chyba v akci Index");  
              return View("Error");  
          }  
      }  
  }  
  ```  

---

### **6. HTTP status kódy a odpovědi** 

- **Manuální vrácení chyb**:  
  ```csharp  
  public IActionResult Detail(int id)  
  {  
      var produkt = _service.ZiskejProdukt(id);  
      if (produkt == null)  
      {  
          return NotFound(); // 404  
      }  
      return View(produkt);  
  }  
  ```  

- **Vlastní status kódy**:  
  ```csharp  
  return StatusCode(403, "Přístup zamítnut");  
  ```  

---

### **7. Zpracování chyb v API**  

- **Middleware `UseProblemDetails`** (RFC 7807):  
  - Vrací standardizované JSON odpovědi pro API.  
  - Konfigurace:  
    ```csharp  
    app.UseExceptionHandler(exceptionHandlerApp =>  
        exceptionHandlerApp.Run(async context =>  
        {  
            context.Response.ContentType = "application/problem+json";  
            var problem = new ProblemDetails  
            {  
                Title = "Došlo k chybě",  
                Status = StatusCodes.Status500InternalServerError  
            };  
            await context.Response.WriteAsJsonAsync(problem);  
        }));  
    ```  

---

### **8. Časté chyby a řešení**  

| **Problém**                          | **Řešení**                                   |  
|---------------------------------------|----------------------------------------------|  
| Nezachycená výjimka v asynchronním kódu | Obalte kód v `try-catch` s `await`.          |  
| Zobrazení stack trace v produkci      | Nastavte `ASPNETCORE_ENVIRONMENT=Production`.|  
| Chybějící chybové stránky             | Ověřte cestu k View a routu v kontroleru.    |  

---

### **9. Best Practices**  

- **Vždy logujte výjimky**: Pro pozdější analýzu.  
- **Nezobrazujte technické detaily v produkci**: Chraňte citlivé informace.  
- **Testujte chybové scénáře**: Simulujte 404, 500 atd.  
- **Používejte HTTPS**: Zabezpečte komunikaci.  

---

### **10. Shrnutí**  

- **Middleware `UseExceptionHandler`** je základ pro zachycení neošetřených výjimek.  
- **Vlastní chybové stránky** zlepšují uživatelský zážitek.  
- **Logování** je klíčové pro diagnostiku problémů.  
- **HTTP status kódy** poskytují strukturovanou odpověď klientovi.  
- **API by měla vracet standardizované chybové odpovědi** (Problem Details).  

---

**Doporučené zdroje**:  
- Oficiální dokumentace: *Handle errors in ASP.NET Core*  
