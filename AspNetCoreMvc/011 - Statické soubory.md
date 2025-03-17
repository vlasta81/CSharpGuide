
## **Přehled zobrazení statických souborů (wwwroot) v ASP.NET Core MVC**  

Správa a konfigurace statických zdrojů jako CSS, JavaScript a obrázků.

---

### **1. Co jsou statické soubory?** 

- **Neměnné zdroje** sloužící klientovi (CSS, JavaScript, obrázky, PDF atd.).  
- **Typické umístění**: Složka `wwwroot` v kořeni projektu.  
- **Příklady**:  
  - `wwwroot/css/style.css`  
  - `wwwroot/js/app.js`  
  - `wwwroot/images/logo.png`  

---

### **2. Konfigurace Middleware**  

- **Povolení služby**: Ve výchozím nastavení je middleware pro statické soubory přidán v `Program.cs`:  
  ```csharp  
  var builder = WebApplication.CreateBuilder(args);  
  var app = builder.Build();  
  
  app.UseStaticFiles(); // Povolí službu statických souborů  
  app.MapControllers();  
  app.Run();  
  ```  
- **Pořadí middleware**: `UseStaticFiles` se obvykle umisťuje před `UseRouting`.

---

### **3. Přístup k souborům**  

- **URL cesta**: Odpovídá relativní cestě v `wwwroot`.  
  - `wwwroot/css/site.css` → přístupné přes `/css/site.css`.  
- **Využití v Razor View**:  
  ```html  
  <link rel="stylesheet" href="~/css/site.css" />  
  <script src="~/js/app.js"></script>  
  <img src="~/images/logo.png" alt="Logo" />  
  ```  
  - `~` odkazuje na kořen `wwwroot`.

---

### **4. Vlastní složky a nastavení**  

#### **a) Změna výchozí složky**  

- Slouží k použití jiného adresáře než `wwwroot`:  
  ```csharp  
  app.UseStaticFiles(new StaticFileOptions  
  {  
      FileProvider = new PhysicalFileProvider(  
          Path.Combine(Directory.GetCurrentDirectory(), "moje-soubory")),  
      RequestPath = "/static"  
  });  
  ```  
  - Soubory v `moje-soubory` budou dostupné pod cestou `/static`.

#### **b) Povolení prohlížení složek**  

- **Varování**: Potenciální bezpečnostní riziko!  
  ```csharp  
  app.UseStaticFiles();  
  app.UseDirectoryBrowser(); // Povolí výpis obsahu složek  
  ```  

---

### **5. Bezpečnostní doporučení**  

- **Omezení přístupu**:  
  - Nevkládejte citlivá data do `wwwroot` (např. konfigurační soubory).  
  - Používejte autorizační pravidla pro chráněné soubory.  
- **Vlastní middleware**: Pro kontrolu přístupu k určitým souborům.  

---

### **6. Cache a výkon**  

- **Nastavení HTTP hlaviček**: Pro optimalizaci načítání:  
  ```csharp  
  app.UseStaticFiles(new StaticFileOptions  
  {  
      OnPrepareResponse = ctx =>  
      {  
          ctx.Context.Response.Headers.Append("Cache-Control", "public,max-age=604800");  
      }  
  });  
  ```  
- **Verzování souborů**: Přidání hash do názvu (např. `site.[hash].css`) pro invalidaci cache.  

---

### **7. Vývojové vs. Produkční prostředí**  

- **Development**:  
  - Lze povolit podrobné chybové zprávy.  
  ```csharp  
  if (app.Environment.IsDevelopment())  
  {  
      app.UseDeveloperExceptionPage();  
  }  
  ```  
- **Production**:  
  - Komprimované a minifikované soubory.  
  - Použití CDN pro statické zdroje.  

---

### **8. Příklady**  

#### **Výchozí struktura `wwwroot`:**  

```
wwwroot/  
├── css/  
│   └── site.css  
├── js/  
│   └── app.js  
├── images/  
│   └── logo.png  
└── lib/  
    └── bootstrap/  
```

#### **Reference na CDN s fallbackem:**  

```html  
<script src="https://cdn.example.com/jquery.min.js"></script>  
<script>  
    window.jQuery || document.write('<script src="~/lib/jquery/jquery.min.js"><\/script>');  
</script>  
```

---

### **9. Časté problémy**  

- **Soubory nejsou dostupné**:  
  - Zkontrolujte, zda je `UseStaticFiles` v `Program.cs`.  
  - Ověřte cestu a oprávnění k souborům.  
- **Špatné cache nastavení**: Problémy s aktualizovanými soubory.  

---

### **10. Shrnutí**  

- Statické soubory se ukládají do `wwwroot` a jsou dostupné přímo přes URL.  
- **Middleware `UseStaticFiles`** zajišťuje jejich zpřístupnění.  
- Bezpečnostní opatření jsou klíčová pro ochranu citlivých dat.  
- Cache a CDN zlepšují výkon aplikace.  

---

**Doporučené zdroje**:  
- Oficiální dokumentace: *Static files in ASP.NET Core*  
