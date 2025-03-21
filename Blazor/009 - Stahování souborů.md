
## **Stahování souborů v ASP.NET Core Blazor**

---

### **1. Základní metody stahování souborů**  

- **Stažení ze serveru (Blazor Server)**:  
  ```csharp  
  // Controller  
  public IActionResult Download(string fileName)  
  {  
      var path = Path.Combine(Environment.WebRootPath, "files", fileName);  
      return PhysicalFile(path, "application/octet-stream", fileName);  
  }  
  ```  
  ```razor  
  <!-- Komponenta -->  
  <a href="/api/download?fileName=@fileName" download>Stáhnout</a>  
  ```  

- **Stažení přes API (Blazor WebAssembly)**:  
  ```csharp  
  var response = await HttpClient.GetAsync($"/api/download?fileName={fileName}");  
  var fileStream = await response.Content.ReadAsStreamAsync();  
  await JSRuntime.InvokeVoidAsync("downloadFile", fileName, fileStream);  
  ```  

---

### **2. Bezpečnostní aspekty** 

- **Autorizace**:  
  - Použití atributu `[Authorize]` na API endpointy nebo kontrolery.  
  ```csharp  
  [Authorize]  
  public IActionResult Download(string fileName) { ... }  
  ```  
- **Validace vstupů**:  
  - Ověření, zda `fileName` neobsahuje nebezpečné znaky (např. `../`).  
  ```csharp  
  if (fileName.Contains("..")) throw new SecurityException();  
  ```  
- **HTTPS**: Zabezpečení přenosu pomocí HTTPS.  

---

### **3. Stažení ze streamu** 

- **Soubory z databáze (EF Core)**:  
  ```csharp  
  public async Task<IActionResult> Download(int fileId)  
  {  
      var file = await _context.Files.FindAsync(fileId);  
      return File(file.Content, "application/octet-stream", file.Name);  
  }  
  ```  
- **Streamování velkých souborů**:  
  ```csharp  
  var stream = new FileStream(path, FileMode.Open, FileAccess.Read);  
  return new FileStreamResult(stream, "application/octet-stream");  
  ```  

---

### **4. Stažení z externí URL** 

- **Použití `HttpClient`**:  
  ```csharp  
  var response = await HttpClient.GetAsync("https://example.com/file.zip");  
  var content = await response.Content.ReadAsByteArrayAsync();  
  await JSRuntime.InvokeVoidAsync("downloadFromUrl", "file.zip", content);  
  ```  
- **JavaScript Interop pro stažení**:  
  ```javascript  
  function downloadFromUrl(fileName, content) {  
      const blob = new Blob([content]);  
      const link = document.createElement('a');  
      link.href = URL.createObjectURL(blob);  
      link.download = fileName;  
      link.click();  
  }  
  ```  

---

### **5. Stahování velkých souborů**  

- **Chunkování (rozdělení na části)**:  
  ```csharp  
  var buffer = new byte[8192];  
  while (await stream.ReadAsync(buffer) > 0)  
  {  
      // Odeslat část klientovi  
  }  
  ```  
- **Progress indikátor**:  
  ```csharp  
  var totalBytes = fileStream.Length;  
  var bytesRead = 0;  
  while ((bytesRead = await stream.ReadAsync(buffer)) > 0)  
  {  
      var progress = (double)bytesRead / totalBytes * 100;  
      // Aktualizovat UI  
  }  
  ```  

---

### **6. Cross-Origin Resource Sharing (CORS)**  

- **Konfigurace na serveru**:  
  ```csharp  
  // Program.cs  
  builder.Services.AddCors(options =>  
  {  
      options.AddPolicy("AllowBlazorClient", policy =>  
          policy.WithOrigins("https://mujweb.com")  
              .AllowAnyMethod()  
              .AllowAnyHeader());  
  });  
  app.UseCors("AllowBlazorClient");  
  ```  
- **Chyby CORS**:  
  - Řešte přidáním hlaviček `Access-Control-Allow-Origin` na serveru.  

---

### **7. Omezení a doporučení** 

- **Maximální velikost souboru**:  
  ```csharp  
  // Startup.cs (Blazor Server)  
  services.Configure<BlazorServerLimits>(options =>  
  {  
      options.MaxRequestBodySize = 100_000_000; // 100 MB  
  });  
  ```  
- **Názvy souborů**: Sanitizujte názvy (např. odstranění mezer a speciálních znaků).  

---

### **8. Odstraňování potíží**  

| **Problém**                | **Řešení**                                  |  
|----------------------------|--------------------------------------------|  
| **CORS chyby**             | Přidejte správnou politiku CORS na serveru. |  
| **Soubor nenalezen**       | Ověřte cestu a oprávnění k souboru.         |  
| **Pomalé stahování**       | Optimalizujte velikost bufferu nebo použijte streamování. |  

---

### **Shrnutí**  

| **Funkce**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Bezpečnost**           | Validace vstupů, autorizace, HTTPS.                                       |  
| **Streamování**          | Efektivní pro velké soubory a data z databáze.                            |  
| **CORS**                 | Povolte zdroje v serverové konfiguraci.                                   |  
| **JavaScript Interop**   | Nutný pro spuštění stahování v prohlížeči (Blazor WebAssembly).           |  

**Doporučené postupy**:  
- Pro Blazor WebAssembly používejte API endpoints pro stahování souborů.  
- Pro velké soubory implementujte progresivní načítání a streamování.  
- Vždy sanitizujte názvy souborů a validujte uživatelské vstupy.  

**Užitečné zdroje**:  
- [Dokumentace: Stahování souborů v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/mvc/models/file-uploads)  
- [Konfigurace CORS](https://learn.microsoft.com/cs-cz/aspnet/core/security/cors)
