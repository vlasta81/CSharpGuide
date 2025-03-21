
## **Integrace ASP.NET Core Blazor s MVC/Razor Pages**  

ASP.NET Core Blazor umožňuje kombinovat moderní interaktivní komponenty s tradičními MVC (Model-View-Controller) a Razor Pages aplikacemi. Tato integrace je užitečná pro postupnou migraci starších aplikací nebo pro vylepšení konkrétních částí UI o interaktivitu bez nutnosti kompletního přepisu.

---

### **1. Scénáře integrace**  

- **Interaktivní prvky v existujících stránkách**: Vložení Blazor komponent do MVC/Razor Pages pro dynamické formuláře, grafy, nebo real-time aktualizace.  
- **Postupná migrace**: Postupné nahrazování částí MVC/Razor Pages aplikace Blazorem.  
- **Kombinace serverového a klientového vykreslování**: Využití Blazor Server pro interaktivitu a MVC pro statický obsah.  

---

### **2. Základní kroky integrace**  

#### **a) Přidání Blazor do existujícího projektu**  

1. Nainstalujte balíčky:  
   ```bash  
   dotnet add package Microsoft.AspNetCore.Components.Web  
   dotnet add package Microsoft.AspNetCore.Components.WebView  
   ```  
2. V `Startup.cs` (nebo `Program.cs`) povolte Blazor služby:  
   ```csharp  
   builder.Services.AddServerSideBlazor();  
   ```  
3. Přidejte Blazor endpoint:  
   ```csharp  
   app.MapBlazorHub(); // Pro Blazor Server  
   ```  

#### **b) Vložení Blazor komponenty do Razor Pages/MVC View**  

- Použijte **Component Tag Helper**:  
  ```razor  
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers  
  <component type="typeof(Counter)" render-mode="ServerPrerendered" />  
  ```  
  - **`render-mode`**:  
    - `ServerPrerendered`: Komponenta se vyrenderuje na serveru a pak se připojí k Blazor hubu.  
    - `Server`: Komponenta se vykreslí až na klientovi po navázání spojení.  

---

### **3. Komunikace mezi Blazor a MVC/Razor Pages**  

- **Předávání parametrů**:  
  ```razor  
  <component type="typeof(UserProfile)" render-mode="ServerPrerendered"  
      param-UserId="Model.UserId" />  
  ```  
  ```csharp  
  // Blazor komponenta  
  [Parameter] public string UserId { get; set; }  
  ```  

- **Volání metod přes JavaScript Interop**:  
  ```javascript  
  // V MVC View  
  function showAlert(message) {  
      alert(message);  
  }  
  ```  
  ```csharp  
  // V Blazor komponentě  
  await JSRuntime.InvokeVoidAsync("showAlert", "Ahoj z Blazor!");  
  ```  

---

### **4. Společné prostředky a layouty** 

- **Sdílený layout**:  
  - MVC/Razor Pages může používat stejný layout jako Blazor (např. navigační menu).  
  - V `_Layout.cshtml` přidejte Blazor skripty:  
    ```razor  
    <script src="_framework/blazor.server.js"></script>  
    ```  

- **Autentizace a autorizace**:  
  - Blazor komponenty mohou využívat stejné Identity služby jako MVC (např. `[Authorize]`).  

---

### **5. Výhody integrace**  

- **Postupná migrace**: Zachovejte funkční části MVC a přidávejte interaktivitu přes Blazor.  
- **Využití existující infrastruktury**: Autentizace, API, datové vrstvy zůstávají nezměněny.  
- **SEO a počáteční výkon**: Prerendering Blazor komponent na serveru zlepšuje SEO a vnímání rychlosti.  

---

### **6. Omezení a výzvy**  

- **Routing konflikty**: Blazor a MVC/Razor Pages mají vlastní routing – je třeba řešit překrývání URL.  
- **Stavová správa**: Sdílení stavu mezi Blazorem a MVC vyžaduje explicitní přístup (např. přes služby nebo session).  
- **Nároky na prostředky**: Blazor Server vyžaduje stabilní SignalR spojení.  

---

### **7. Příklady použití**  

- **Interaktivní formulář v Razor Page**:  
  ```razor  
  <!-- Index.cshtml -->  
  <component type="typeof(RegistrationForm)" render-mode="ServerPrerendered" />  
  ```  
- **Dashboard s real-time daty v MVC View**:  
  ```razor  
  <!-- Dashboard.cshtml -->  
  <component type="typeof(RealTimeChart)" render-mode="Server" />  
  ```  

---

### **Shrnutí**  

| **Aspekt**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Kombinace technologií** | Blazor přidává interaktivitu do existujících MVC/Razor Pages aplikací.     |  
| **Komunikace**           | Parametry, JavaScript Interop, sdílené služby.                            |  
| **Výkon**                | Prerendering zlepšuje SEO a rychlost prvního načtení.                     |  
| **Migrace**              | Ideální pro postupný přechod na Blazor bez nutnosti kompletního přepisu.  |  

**Užitečné zdroje**:  
- Oficiální dokumentace: [Integrate Blazor into MVC/Razor Pages](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/integration).  
- Ukázky: [Blazor + MVC Sample Project](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/blazor/common/samples/).
