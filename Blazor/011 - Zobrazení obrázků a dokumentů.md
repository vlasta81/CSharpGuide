
## **Zobrazení obrázků a dokumentů v ASP.NET Core Blazor**

---

### **1. Zobrazení obrázků**  

#### **a) Statické obrázky**  

- **Umístění**: Uložte obrázky ve složce `wwwroot` (např. `wwwroot/images/logo.png`).  
- **Použití v komponentě**:  
  ```razor  
  <img src="/images/logo.png" alt="Logo" />  
  ```  

#### **b) Dynamické obrázky (z databáze nebo API)**  

- **API endpoint pro obrázky**:  
  ```csharp  
  [HttpGet("image/{id}")]  
  public IActionResult GetImage(int id)  
  {  
      var imageData = _dbContext.Images.Find(id).Data; // byte[]  
      return File(imageData, "image/jpeg");  
  }  
  ```  
- **Zobrazení v komponentě**:  
  ```razor  
  <img src="/api/image/1" alt="Dynamický obrázek" />  
  ```  

#### **c) Base64 kódování**  

- **Převod byte[] na Base64**:  
  ```csharp  
  var base64 = Convert.ToBase64String(imageData);  
  var imageSrc = $"data:image/jpeg;base64,{base64}";  
  ```  
  ```razor  
  <img src="@imageSrc" alt="Base64 obrázek" />  
  ```  

---

### **2. Zobrazení dokumentů (PDF, Word, Excel)**  

#### **a) Stažení dokumentu**  

- **Generování odkazu pro stažení**:  
  ```razor  
  <a href="/api/documents/1" download="dokument.pdf">Stáhnout PDF</a>  
  ```  

#### **b) Náhled dokumentů v prohlížeči**  

- **Integrace PDF.js pro PDF**:  
  ```razor  
  <iframe src="/pdfjs/web/viewer.html?file=/api/documents/1" width="100%" height="600px" />  
  ```  
- **Google Docs Viewer (pro veřejné dokumenty)**:  
  ```razor  
  <iframe src="https://docs.google.com/viewer?url=@(Uri.EscapeDataString("https://vase-app.cz/api/documents/1"))&embedded=true" />  
  ```  

#### **c) Konverze dokumentů na HTML**  

- **Knihovny pro konverzi**:  
  - **LibreOffice** (serverová konverze).  
  - **Aspose.Words** (komerční řešení pro .NET).  

---

### **3. Bezpečnostní aspekty**  

- **Ověření přístupu**:  
  - Použijte atribut `[Authorize]` na API endpointy.  
  ```csharp  
  [Authorize]  
  [HttpGet("document/{id}")]  
  public IActionResult GetDocument(int id) { ... }  
  ```  
- **Validace vstupů**: Zabránění path traversal útokům (např. `../` v cestě).  

---

### **4. Výkon a optimalizace**  

- **Caching**:  
  - Přidejte hlavičky `Cache-Control` pro statické soubory.  
  ```csharp  
  app.UseStaticFiles(new StaticFileOptions  
  {  
      OnPrepareResponse = ctx =>  
          ctx.Context.Response.Headers.Append("Cache-Control", "public,max-age=604800")  
  });  
  ```  
- **Streamování velkých souborů**:  
  ```csharp  
  [HttpGet("large-file")]  
  public FileStreamResult GetLargeFile()  
  {  
      var stream = new FileStream("velky-soubor.zip", FileMode.Open, FileAccess.Read);  
      return new FileStreamResult(stream, "application/zip");  
  }  
  ```  

---

### **5. Chybové stavy a lokalizace**  

- **Placeholder pro chybějící obrázky**:  
  ```razor  
  <img src="@imageUrl" alt="Obrázek" @onerror="HandleImageError" />  
  @code {  
      private void HandleImageError()  
      {  
          imageUrl = "/images/placeholder.png";  
      }  
  }  
  ```  
- **Lokalizované texty v dokumentech**:  
  - Ukládejte dokumenty v různých jazykových verzích a přiřazujte je dle kultury uživatele.  

---

### **6. Třetí knihovny a nástroje**  

- **Blazor.PDF**: Komponenta pro zobrazení PDF přímo v Blazoru.  
- **Syncfusion Blazor PDF Viewer**: Komerční řešení pro interaktivní prohlížení PDF.  
- **Radzen.Blazor**: Otevřená knihovna s podporou zobrazení dokumentů.  

---

### **7. Příklady kódu**  

#### **a) Kontroller pro stažení dokumentu**  

```csharp  
[HttpGet("document/{id}")]  
public IActionResult DownloadDocument(int id)  
{  
    var document = _dbContext.Documents.Find(id);  
    return File(document.Content, document.MimeType, document.FileName);  
}  
```  

#### **b) Komponenta pro prohlížení PDF**  

```razor  
@inject IJSRuntime JSRuntime  

<button @onclick="ShowPdf">Zobrazit PDF</button>  

@code {  
    private async Task ShowPdf()  
    {  
        await JSRuntime.InvokeVoidAsync("openPdf", "/api/document/1");  
    }  
}  
```  
```javascript  
// V wwwroot/js/pdf.js  
function openPdf(url) {  
    window.open(url, "_blank");  
}  
```  

---

### **Shrnutí**  

| **Funkce**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Obrázky**              | Statické (wwwroot), dynamické (API), Base64.                              |  
| **Dokumenty**            | Stažení, náhled (PDF.js), konverze na HTML.                               |  
| **Bezpečnost**           | Autorizace, validace vstupů.                                              |  
| **Výkon**                | Caching, streamování.                                                     |  
| **Knihovny**             | Syncfusion, Radzen, Aspose.Words.                                         |  

**Doporučení**:  
- Pro složité dokumenty použijte specializované knihovny.  
- Omezte přístup k citlivým souborům pomocí autentizace.  

**Užitečné zdroje**:  
- [Dokumentace: Práce se soubory v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/mvc/models/file-uploads)  
- [PDF.js integrace](https://mozilla.github.io/pdf.js/)
