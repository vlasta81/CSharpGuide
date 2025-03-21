
## **Vykreslení Razor komponent mimo ASP.NET Core**  

Razor komponenty, primárně spojované s Blazorem a ASP.NET Core, lze využít i v jiných typech aplikací a prostředí. Tato flexibilita umožňuje integraci dynamického UI do desktopových, mobilních aplikací nebo generování obsahu bez nutnosti webového serveru.

---

### **1. Možnosti využití mimo ASP.NET Core**  

#### **a) Blazor Hybrid**  

- **Cíl**: Integrace Razor komponent do desktopových a mobilních aplikací.  
- **Podporované platformy**:  
  - **WPF/WinForms**: Pomocí `BlazorWebView` komponenty.  
  - **.NET MAUI**: Nativní podpora pro mobilní a desktopové aplikace.  
- **Příklad (WPF)**:  
  ```xml  
  <!-- XAML -->  
  <blazor:BlazorWebView HostPage="wwwroot/index.html">  
      <blazor:BlazorWebView.RootComponents>  
          <blazor:RootComponent Selector="#app" ComponentType="{x:Type local:MainComponent}" />  
      </blazor:BlazorWebView.RootComponents>  
  </blazor:BlazorWebView>  
  ```  

#### **b) Konzolové aplikace**  

- **Generování HTML/PDF**: Využití Razor šablon pro vytváření statického obsahu (např. reporty, e-maily).  
- **Nástroje**:  
  - **RazorLight**: Knihovna pro kompilaci Razor šablon bez ASP.NET Core.  
  - **Příklad**:  
    ```csharp  
    var engine = new RazorLightEngineBuilder().UseFileSystemProject("Templates").Build();  
    string html = await engine.CompileRenderAsync("template.cshtml", data);  
    ```  

#### **c) Jiné webové frameworky**  

- **Integrace s JavaScriptovými SPA** (React, Angular):  
  - Blazor komponenty lze hostovat jako **webové komponenty** (Custom Elements).  
  - Komunikace přes JavaScript Interop.  

---

### **2. Nástroje a knihovny**  

- **Balíčky NuGet**:  
  - `Microsoft.AspNetCore.Components.WebView`: Pro integraci do WPF/WinForms/MAUI.  
  - `RazorLight`/`RazorEngine`: Pro generování obsahu z Razor šablon.  
  - `Microsoft.Maui.Controls`: Pro .NET MAUI aplikace.  
- **Razor Class Library (RCL)**: Sdílení komponent mezi projekty.  

---

### **3. Postup pro vykreslení v desktopové aplikaci (WPF)**  

1. Vytvořte WPF projekt.  
2. Přidejte balíček:  
   ```bash  
   dotnet add package Microsoft.AspNetCore.Components.WebView.WindowsForms  
   ```  
3. Definujte Blazor komponentu v RCL.  
4. Hostujte komponentu v `BlazorWebView` (viz příklad výše).  

---

### **4. Generování statického obsahu** 

- **Scénáře**: E-mailové šablony, PDF reporty, HTML dokumentace.  
- **Workflow**:  
  1. Vytvořte Razor šablonu (`template.cshtml`).  
  2. Naplňte ji daty (model).  
  3. Vygenerujte HTML pomocí RazorLight.  

---

### **5. Omezení**  

- **Žádný serverový kontext**: Komponenty nemají přístup k HTTP kontextu nebo session.  
- **Interaktivita**: V konzolových aplikacích nelze využít Blazorovou interaktivitu (chybí `IJSRuntime`).  
- **CSS/JS izolace**: V non-Blazor prostředí je nutné řešit ručně.  

---

### **6. Výhody**  

- **Jednotný kódový základ**: Sdílení logiky a UI mezi webovými a desktopovými aplikacemi.  
- **Flexibilita**: Razor šablony jsou univerzální pro různé výstupní formáty (HTML, PDF, XML).  

---

### **7. Příklady scénářů**  

- **Desktopový dashboard**: WPF aplikace s Blazorovými grafickými komponentami.  
- **Generování faktur**: Konzolová aplikace vytvářející PDF z Razor šablon.  
- **Mobilní aplikace**: .NET MAUI aplikace s interaktivními formuláři v Blazoru.  

---

### **Shrnutí**  

| **Aspekt**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Blazor Hybrid**         | Integrace do WPF, WinForms, .NET MAUI.                                    |  
| **Generování obsahu**     | Razor šablony pro e-maily, reporty (RazorLight).                          |  
| **Webové komponenty**     | Použití v JavaScriptových SPA (React, Angular).                           |  
| **Omezení**              | Chybí podpora pro některé Blazor API (např. `NavigationManager`).        |  

**Užitečné zdroje**:  
- [Dokumentace: Blazor Hybrid](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/hybrid/)  
- [RazorLight na GitHubu](https://github.com/toddams/RazorLight)
