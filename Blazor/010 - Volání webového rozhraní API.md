
## **Volání webového API z ASP.NET Core Blazor**  

---

### **1. Scénáře volání API**  

#### **a) Na straně klienta (Blazor WebAssembly)**  

- **Přímé volání API z prohlížeče** pomocí `HttpClient`.  
- **Omezení CORS**: Server API musí povolit zdroj Blazor aplikace v hlavičkách.  
- **Bezpečnost**: Ukládání tokenů (např. JWT) v paměti prohlížeče.  

#### **b) Na straně serveru (Blazor Server)**  

- **Volání API ze serveru** – žádné CORS omezení.  
- **Výhody**: Lepší kontrola přístupu a bezpečnostních tokenů.  
- **Rizika**: Zatížení serveru a latence.  

---

### **2. Abstrakce služby pro API**  

- **Vytvoření služby** pro centralizaci volání API:  
  ```csharp  
  public class ApiService  
  {  
      private readonly HttpClient _httpClient;  
      public ApiService(HttpClient httpClient) => _httpClient = httpClient;  

      public async Task<Data> GetDataAsync() =>  
          await _httpClient.GetFromJsonAsync<Data>("api/data");  
  }  
  ```  
- **Registrace služby** v DI:  
  ```csharp  
  builder.Services.AddScoped<ApiService>();  
  ```  

---

### **3. Práce s `HttpClient`**  

#### **a) Základní metody**  

- **`GetFromJsonAsync<T>`**: GET požadavek s automatickou deserializací.  
- **`PostAsJsonAsync<T>`**: POST s JSON tělem.  
- **`PutAsJsonAsync<T>`**, **`PatchAsJsonAsync<T>`**, **`DeleteAsync`**.  

#### **b) Příklad použití**  

```csharp  
var data = await HttpClient.GetFromJsonAsync<Data>("api/data");  
await HttpClient.PostAsJsonAsync("api/data", newData);  
```  

---

### **4. Pokročilá konfigurace**  

#### **a) Pojmenovaný `HttpClient` (IHttpClientFactory)**  

- **Definice v `Program.cs`**:  
  ```csharp  
  builder.Services.AddHttpClient("ExternalAPI", client =>  
  {  
      client.BaseAddress = new Uri("https://api.example.com");  
  });  
  ```  
- **Použití ve službě**:  
  ```csharp  
  public ApiService(IHttpClientFactory factory)  
  {  
      _httpClient = factory.CreateClient("ExternalAPI");  
  }  
  ```  

#### **b) Typovaný `HttpClient`**  

- **Dědění z `HttpClient`**:  
  ```csharp  
  public class TypedApiClient(HttpClient httpClient)  
  {  
      public async Task<Data> GetDataAsync() =>  
          await httpClient.GetFromJsonAsync<Data>("data");  
  }  
  ```  

---

### **5. Bezpečnost a autentizace**  

#### **a) Cookie-based přihlášení**  

- **Nastavení cookies**:  
  ```csharp  
  await HttpClient.PostAsJsonAsync("login", credentials);  
  ```  
- **Automatické přidávání cookies** v požadavcích (pouze pokud je API na stejné doméně).  

#### **b) Ochrana proti padělání (Anti-Forgery)**  

- **Požadavek na anti-forgery token** v Blazor Server:  
  ```csharp  
  [ValidateAntiForgeryToken]  
  public async Task<IActionResult> SaveData(Data data) { ... }  
  ```  

---

### **6. Streamování dat**  

- **Streamování odpovědí** (např. pro velké soubory):  
  ```csharp  
  var response = await HttpClient.GetAsync("api/large-data", HttpCompletionOption.ResponseHeadersRead);  
  var stream = await response.Content.ReadAsStreamAsync();  
  ```  

---

### **7. Řešení chyb**  

- **Ošetření chybových stavů**:  
  ```csharp  
  try  
  {  
      var response = await HttpClient.GetAsync("api/data");  
      response.EnsureSuccessStatusCode();  
  }  
  catch (HttpRequestException ex)  
  {  
      // Logování chyby  
  }  
  ```  

---

### **8. Cross-Origin Resource Sharing (CORS)**

- **Konfigurace na serveru API**:  
  ```csharp  
  builder.Services.AddCors(options =>  
  {  
      options.AddPolicy("AllowBlazor", policy =>  
          policy.WithOrigins("https://mujweb.cz")  
                .AllowAnyMethod()  
                .AllowAnyHeader());  
  });  
  ```  

---

### **9. Předvykreslování dat (Pre-rendering)**  

- **Načtení dat při inicializaci komponenty**:  
  ```csharp  
  protected override async Task OnInitializedAsync()  
  {  
      data = await ApiService.GetDataAsync();  
  }  
  ```  
- **Podpora SSR (Server-Side Rendering)**: Data se načtou na serveru před odesláním HTML.  

---

### **10. Testování komponent**  

- **Použití knihovny `bUnit`**:  
  ```csharp  
  [Fact]  
  public void Komponenta_Zobrazuje_Data()  
  {  
      var ctx = new TestContext();  
      ctx.Services.AddMockHttpClient().AddJsonResponse("api/data", new Data());  
      var component = ctx.RenderComponent<DataKomponenta>();  
      Assert.Contains("Data", component.Markup);  
  }  
  ```  

---

### **Shrnutí**  

| **Funkce**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Abstrakce služby**     | Centralizuje logiku volání API pro čistší kód.                            |  
| **HttpClient metody**    | Zjednodušují práci s JSON (`GetFromJsonAsync`, `PostAsJsonAsync`).        |  
| **CORS**                 | Nutné pro volání mezi doménami v Blazor WebAssembly.                      |  
| **Testování**            | Mockování HttpClient pro testování komponent.                             |  

**Doporučení**:  
- Pro složité API použijte **Swagger/OpenAPI** pro generování klientských tříd.  
- Implementujte **opakování požadavků** (retry policy) pomocí Polly.  

**Užitečné zdroje**:  
- [Dokumentace: HttpClient v Blazoru](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/call-web-api)  
- [Příklady CORS](https://learn.microsoft.com/cs-cz/aspnet/core/security/cors)
