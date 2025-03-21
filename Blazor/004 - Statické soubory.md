
## **Souhrn: Statické soubory v ASP.NET Core Blazor**  

Statické soubory jsou klíčové pro fungování webových aplikací, protože obsahují zdroje, které se načítají na straně klienta (prohlížeče) bez potřeby serverového zpracování. V Blazor se tyto soubory používají pro stylování, skriptování, obrázky a další prostředky.

---

### **1. Co jsou statické soubory?**  

- **Definice**: Soubory, které se servírují klientům přímo bez modifikace serverem (např. CSS, JavaScript, obrázky, fonty).  
- **Účel**:  
  - Stylování komponent (CSS).  
  - Interaktivita (JavaScript).  
  - Zobrazení obrázků nebo ikon.  
  - Konfigurace (např. `appsettings.json` v Blazor WebAssembly).  

---

### **2. Umístění statických souborů** 

- **Hlavní složka**: `wwwroot` (v kořenovém adresáři projektu).  
  - Všechny soubory v této složce jsou veřejně přístupné.  
  - **Příklad struktury**:  
    ```plaintext
    wwwroot/
    ├─ css/
    │  └─ styles.css  
    ├─ js/
    │  └─ scripts.js  
    ├─ images/
    │  └─ logo.png  
    └─ appsettings.json  
    ```  
- **Důležité**:  
  - V Blazor WebAssembly se `appsettings.json` nachází v `wwwroot`, protože se stahuje do prohlížeče.  
  - V Blazor Server je `appsettings.json` na serveru a není součástí `wwwroot`.

---

### **3. Konfigurace middleware pro statické soubory**  

- **Middleware**: `UseStaticFiles()` – povolí servírování souborů z `wwwroot`.  
- **Nastavení v `Program.cs`**:  
  ```csharp
  var app = builder.Build();
  app.UseStaticFiles(); // Povolí statické soubory
  app.Run();
  ```  
- **Pořadí middleware**: `UseStaticFiles` musí být před voláním `UseRouting` nebo `MapBlazorHub`.  

---

### **4. Přístup ke statickým souborům v komponentách**  

- **Cesty**: Relativní cesty z `wwwroot` (např. `/css/styles.css`).  
- **Příklad v komponentě**:  
  ```razor
  <!-- Obrázek -->
  <img src="/images/logo.png" alt="Logo" />

  <!-- Odkaz na CSS -->
  <link href="/css/styles.css" rel="stylesheet" />

  <!-- Odkaz na JavaScript -->
  <script src="/js/scripts.js"></script>
  ```  

---

### **5. Cacheování statických souborů**  

- **Výchozí chování**: Statické soubory jsou cacheovány prohlížečem (pro lepší výkon).  
- **Konfigurace cache hlaviček**:  
  ```csharp
  app.UseStaticFiles(new StaticFileOptions
  {
      OnPrepareResponse = ctx =>
      {
          ctx.Context.Response.Headers.Append("Cache-Control", "public,max-age=604800"); // 7 dní
      }
  });
  ```  

---

### **6. Bezpečnostní aspekty**  

- **Veřejný přístup**: Vše v `wwwroot` je přístupné komukoli (např. `https://example.com/images/logo.png`).  
- **Rizika**:  
  - Citlivá data (např. `appsettings.json` v Blazor WebAssembly) jsou viditelná v prohlížeči.  
  - **Řešení**:  
    - Nepoužívat `wwwroot` pro tajné klíče.  
    - Citlivá data ukládat na serveru (v Blazor Server) nebo použít šifrované API komunikace (v Blazor WASM).  

---

### **7. Hostování v různých prostředích**  

- **Vývojové prostředí (Development)**:  
  - Statické soubory se načítají přímo z `wwwroot`.  
- **Produkční prostředí (Production)**:  
  - Pro optimalizaci se používají nástroje jako **Bundler** nebo **CDN** (Content Delivery Network).  
  - **Příklad CDN**:  
    ```html
    <link href="https://cdn.example.com/css/styles.min.css" rel="stylesheet" />
    ```  

---

### **8. Časté problémy a řešení**  

- **Soubor nenalezen**:  
  - **Příčina**: Chybná cesta nebo soubor není v `wwwroot`.  
  - **Řešení**: Ověřit cestu a umístění souboru.  
- **Neaktualizované změny**:  
  - **Příčina**: Prohlížeč cacheuje starou verzi.  
  - **Řešení**: Přidat verzi k URL (např. `/css/styles.css?v=2`) nebo vyčistit cache.  
- **Blokovaný JavaScript/CSS**:  
  - **Příčina**: Content Security Policy (CSP) nebo chybějící MIME typ.  
  - **Řešení**: Konfigurovat CSP hlavičky na serveru.  

---

### **9. Integrace s Blazor komponentami**  

- **CSS izolace**:  
  - Každá komponenta může mít vlastní CSS soubor (např. `Counter.razor.css`), který se automaticky scopeuje.  
- **JavaScript Interop**:  
  - Volání funkcí ze statických JS souborů přes `IJSRuntime`:  
    ```csharp
    await JSRuntime.InvokeVoidAsync("mojeFunkce");
    ```  

---

### **Shrnutí**  

- Statické soubory jsou uloženy ve složce `wwwroot` a slouží pro stylování, skriptování a zobrazení prostředků.  
- **Klíčové kroky**:  
  - Povolit middleware `UseStaticFiles()`.  
  - Dodržovat správnou strukturu adresářů.  
  - Ošetřit cacheování a bezpečnost.  
- **Pozor**: V Blazor WebAssembly jsou konfigurační soubory (jako `appsettings.json`) součástí `wwwroot` a jsou veřejné.  

**Užitečné zdroje**:  
- Oficiální dokumentace: [Static files in ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/fundamentals/static-files).  
