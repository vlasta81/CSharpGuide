
## **`IHttpContextAccessor` a `HttpContext` v ASP.NET Core Blazor komponentách**  

V ASP.NET Core Blazor je práce s `HttpContext` (informace o HTTP požadavku) a `IHttpContextAccessor` (přístup k `HttpContext`) **specifická v závislosti na hostingovém modelu** (Server vs WebAssembly). Zde je přehled klíčových informací:

---

### **1. K čemu slouží `HttpContext`?**  

- Obsahuje metadata o aktuálním HTTP požadavku:  
  - Hlavičky (`Request.Headers`), cookies (`Request.Cookies`).  
  - Autentizace uživatele (`User.Identity`).  
  - Cesta požadavku (`Request.Path`), IP adresa (`Connection.RemoteIpAddress`).  

---

### **2. Blazor Server vs WebAssembly**  

#### **a) Blazor Server**  

- **HttpContext je dostupný pouze během prerenderingu** (předvykreslování na serveru).  
- **Po navázání SignalR spojení** (interaktivní režim) už `HttpContext` **není dostupný** (nelze použít v běžných komponentách).  
- **Poznámka**: `IHttpContextAccessor` se v Blazor Server **nedoporučuje** používat pro přístup k aktuálnímu požadavku v interaktivních komponentách (může způsobit chyby).  

#### **b) Blazor WebAssembly**  

- **HttpContext neexistuje**, protože aplikace běží v prohlížeči.  
- Data z HTTP požadavku (např. cookies) lze získat **pouze přes API** (volání serveru).  

---

### **3. Jak získat data z HTTP požadavku v Blazor?**  

#### **a) Během prerenderingu v Blazor Server**  

- **Krok 1**: Zaregistrujte `IHttpContextAccessor` v `Program.cs`:  
  ```csharp
  builder.Services.AddHttpContextAccessor();
  ```  
- **Krok 2**: Injektujte `IHttpContextAccessor` do komponenty a použijte `HttpContext`:  
  ```razor
  @inject IHttpContextAccessor HttpContextAccessor

  <p>User Agent: @userAgent</p>

  @code {
      private string? userAgent;

      protected override void OnInitialized()
      {
          // HttpContext je dostupný pouze během prerenderingu
          if (HttpContextAccessor.HttpContext != null)
          {
              userAgent = HttpContextAccessor.HttpContext.Request.Headers["User-Agent"];
          }
      }
  }
  ```  

#### **b) Předání dat z serveru do Blazor WebAssembly**  

- Při prvním načtení aplikace předávejte potřebná data přes **API endpoint** nebo **embedování do HTML**:  
  ```csharp
  // Serverový endpoint (např. v Controlleru)
  [HttpGet("initial-data")]
  public IActionResult GetInitialData()
  {
      var data = new 
      {
          UserAgent = HttpContext.Request.Headers["User-Agent"]
      };
      return Ok(data);
  }
  ```  
  ```csharp
  // V Blazor WebAssembly komponentě
  protected override async Task OnInitializedAsync()
  {
      var response = await HttpClient.GetFromJsonAsync<InitialData>("initial-data");
      userAgent = response.UserAgent;
  }
  ```

---

### **4. Omezení a doporučení**  

- **Blazor Server**:  
  - `HttpContext` je **pouze během prerenderingu** – po přechodu do interaktivního režimu je `null`.  
  - Pro přístup k uživatelským datům použijte `AuthenticationStateProvider` místo `HttpContext.User`.  
- **Blazor WebAssembly**:  
  - Data jako cookies nebo hlavičky musí procházet přes **explicitní API volání**.  
  - Bezpečnost: Nikdy neukládejte citlivá data (jako JWT tokeny) přímo v kódu klienta.  

---

### **5. Příklady použití**  

#### **a) Získání IP adresy během prerenderingu**  

```csharp
protected override void OnInitialized()
{
    if (HttpContextAccessor.HttpContext != null)
    {
        var ip = HttpContextAccessor.HttpContext.Connection.RemoteIpAddress?.ToString();
        Console.WriteLine($"IP: {ip}");
    }
}
```  

#### **b) Čtení cookies v Blazor WebAssembly**  

```csharp
// Na serveru (API endpoint)
[HttpGet("get-cookie")]
public IActionResult GetCookie()
{
    var cookieValue = HttpContext.Request.Cookies["CookieName"];
    return Ok(cookieValue);
}

// V Blazor komponentě
var cookie = await HttpClient.GetStringAsync("get-cookie");
```  

---

### **6. Bezpečnostní upozornění** 

- **Neukládejte citlivé informace** (např. autentizační tokeny) do `HttpContext` v Blazor Server – používejte zabezpečené metody jako `AuthenticationStateProvider`.  
- V Blazor WebAssembly **nikdy nepředávejte citlivá data přímo z serveru** bez šifrování.  

---

### **Shrnutí**  

| **Model**         | **Dostupnost HttpContext**       | **Metoda přístupu**                |  
|--------------------|----------------------------------|-------------------------------------|  
| **Blazor Server**  | Pouze během prerenderingu        | `IHttpContextAccessor` (omezeně)   |  
| **Blazor WASM**    | Nedostupné                       | API volání na server                |  

**Klíčové body**:  
- V Blazor Server `HttpContext` funguje **pouze při prvním načtení stránky** (prerendering).  
- Pro interaktivní části používejte **stavové služby** nebo **API**.  
- V Blazor WebAssembly veškerá data z HTTP požadavku řešte přes serverová API.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Server Security](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/security/server/).  
