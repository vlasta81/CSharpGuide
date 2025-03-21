
## **Nahrání souborů v ASP.NET Core Blazor**  

---

### **1. Nahrání souborů na server**  

#### **a) S vykreslováním na straně serveru (SSR)**  

- **Komponenta `InputFile`**:  
  ```razor  
  <InputFile OnChange="HandleFileUpload" multiple />  
  ```  
  ```csharp  
  private async Task HandleFileUpload(InputFileChangeEventArgs e)  
  {  
      var file = e.File;  
      using var stream = file.OpenReadStream(maxAllowedSize: 10 * 1024 * 1024); // Max 10 MB  
      var path = Path.Combine(Environment.WebRootPath, "uploads", file.Name);  
      await using var fileStream = new FileStream(path, FileMode.Create);  
      await stream.CopyToAsync(fileStream);  
  }  
  ```  
- **Omezení velikosti**: Nastavte `maxAllowedSize` v `OpenReadStream()`.  

#### **b) S vykreslováním na straně klienta (CSR – Blazor WebAssembly)**  

- **Použití `HttpClient` s `MultipartFormDataContent`**:  
  ```csharp  
  var content = new MultipartFormDataContent();  
  var fileContent = new StreamContent(file.OpenReadStream());  
  content.Add(fileContent, "file", file.Name);  
  await HttpClient.PostAsync("/api/upload", content);  
  ```  

---

### **2. Zrušení nahrání souboru**  

- **Použití `CancellationTokenSource`**:  
  ```csharp  
  private CancellationTokenSource _cts = new();  

  private async Task UploadFile(IBrowserFile file)  
  {  
      try  
      {  
          await file.OpenReadStream().CopyToAsync(..., _cts.Token);  
      }  
      catch (OperationCanceledException)  
      {  
          // Zrušeno uživatelem  
      }  
  }  

  private void CancelUpload() => _cts.Cancel();  
  ```  

---

### **3. Nahrání s průběhem**  

- **Vlastní implementace**:  
  ```csharp  
  var buffer = new byte[4096];  
  int bytesRead;  
  long totalRead = 0;  

  while ((bytesRead = await stream.ReadAsync(buffer)) != 0)  
  {  
      totalRead += bytesRead;  
      var progress = (double)totalRead / file.Size * 100;  
      await InvokeAsync(() => StateHasChanged()); // Aktualizace UI  
  }  
  ```  

---

### **4. Nahrání náhledu obrázku**  

- **JavaScript Interop pro náhled**:  
  ```javascript  
  function createPreview(fileInput, previewId) {  
      const file = fileInput.files[0];  
      const reader = new FileReader();  
      reader.onload = (e) => {  
          document.getElementById(previewId).src = e.target.result;  
      };  
      reader.readAsDataURL(file);  
  }  
  ```  
  ```csharp  
  await JSRuntime.InvokeVoidAsync("createPreview", inputFileElement, "previewImage");  
  ```  

---

### **5. Ukládání malých souborů do databáze (EF Core)**  

- **Model s polem typu `byte[]`**:  
  ```csharp  
  public class FileEntity  
  {  
      public int Id { get; set; }  
      public byte[] Content { get; set; }  
      public string FileName { get; set; }  
  }  
  ```  
- **Uložení souboru**:  
  ```csharp  
  var fileEntity = new FileEntity  
  {  
      Content = await file.OpenReadStream().ReadAllBytesAsync(),  
      FileName = file.Name  
  };  
  await _context.SaveChangesAsync();  
  ```  

---

### **6. Nahrání do externí služby (např. Azure Blob Storage)**  

- **Použití `Azure.Storage.Blobs`**:  
  ```csharp  
  var blobClient = new BlobClient(connectionString, containerName, file.Name);  
  await blobClient.UploadAsync(file.OpenReadStream());  
  ```  

---

### **7. Omezení SignalR (Blazor Server)**  

- **Maximální velikost zprávy**:  
  ```csharp  
  services.AddSignalR(options =>  
  {  
      options.MaximumReceiveMessageSize = 10 * 1024 * 1024; // 10 MB  
  });  
  ```  
- **Maximální počet paralelních volání**:  
  ```csharp  
  services.AddServerSideBlazor().AddHubOptions(options =>  
  {  
      options.MaximumParallelInvocationsPerClient = 5;  
  });  
  ```  

---

### **8. Odstraňování potíží**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|--------------------------------------------|  
| **Překročení maximální velikosti**   | Zkontrolujte `maxAllowedSize` a SignalR nastavení. |  
| **Chyby CORS**                       | Přidejte politiku CORS pro API endpoints. |  
| **Pomalé nahrávání**                 | Optimalizujte velikost bufferu (např. 4–8 KB). |  
| **Chybějící náhled obrázku**         | Ověřte, zda je soubor čitelný (např. `image/jpeg`). |  

---

### **9. Bezpečnostní doporučení**  

- **Validace MIME typu**:  
  ```csharp  
  if (file.ContentType != "image/png")  
      throw new Exception("Neplatný typ souboru.");  
  ```  
- **Omezení přípon souborů**:  
  ```csharp  
  var allowedExtensions = new[] { ".jpg", ".png" };  
  if (!allowedExtensions.Contains(Path.GetExtension(file.Name)))  
      throw new Exception("Neplatná přípona.");  
  ```  

---

**Shrnutí**:  
- **SSR vs. CSR**: SSR je vhodné pro rychlé nahrávání, CSR pro offline scénáře.  
- **Průběh a zrušení**: Uživatelská přívětivost díky vizualizaci průběhu a možnosti zrušení.  
- **Integrace s externími službami**: Azure, AWS, nebo vlastní API.  
- **Omezení**: SignalR a velikost souborů vyžadují pečlivé nastavení.  

**Užitečné zdroje**:  
- [Dokumentace: Nahrání souborů v Blazoru](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/file-uploads)  
- [Azure Blob Storage s Blazorem](https://learn.microsoft.com/cs-cz/azure/storage/blobs/storage-blob-upload)
