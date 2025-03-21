
## **Integrované komponenty ASP.NET Core Razor**  

---

### **1. Úvod do Razor komponent**  

- **Definice**: Razor komponenty jsou základní stavební jednotky v Blazoru pro tvorbu interaktivního webového UI pomocí C#.  
- **Struktura**: Soubory `.razor` kombinující HTML markup s C# kódem.  
- **Vlastnosti**:  
  - **Znovupoužitelnost**: Komponenty lze vkládat do sebe a sdílet mezi projekty.  
  - **Interaktivita**: Podpora událostí (kliknutí, změny hodnot) a datových vazeb.  
  - **Životní cyklus**: Metody jako `OnInitialized`, `OnParametersSet`, `OnAfterRender`.  

---

### **2. Integrace v rámci ASP.NET Core** 

#### **a) Hostovací modely**  

- **Blazor Server**:  
  - UI se renderuje na serveru, aktualizace přes SignalR.  
  - Vhodné pro rychlou interaktivitu a starší prohlížeče.  
- **Blazor WebAssembly**:  
  - Aplikace běží v prohlížeči jako WebAssembly.  
  - Offline funkčnost, ale delší inicializace.  

#### **b) Spolupráce s MVC/Razor Pages**  

- **Vložení komponenty do MVC/Razor Pages**:  
  ```razor  
  <component type="typeof(Counter)" render-mode="ServerPrerendered" />  
  ```  
  - **`render-mode`**:  
    - `ServerPrerendered`: Statické HTML + interaktivita po načtení.  
    - `Server`: Čistě interaktivní režim.  

#### **c) Sdílení prostředků**  

- **Razor Class Library (RCL)**:  
  - Knihovny pro sdílení komponent, stylů a skriptů mezi projekty.  
  - Statické soubory (CSS/JS) dostupné přes `_content/{Knihovna}/soubor.css`.  

---

### **3. Klíčové funkce integrace**  

#### **a) Směrování (Routing)**  

- **Direktiva `@page`**: Definice URL cesty pro komponentu.  
  ```razor  
  @page "/counter"  
  ```  
- **Parametry routy**:  
  ```razor  
  @page "/user/{Id:int}"  
  [Parameter] public int Id { get; set; }  
  ```  

#### **b) Dependency Injection (DI)**  

- **Integrace služeb**:  
  - Registrace služeb v `Program.cs` a injektování do komponent přes `@inject`.  
  ```csharp  
  @inject IDataService DataService  
  ```  

#### **c) Formuláře a validace**  

- **`EditForm` komponenta**:  
  - Vazba na model s podporou validace (DataAnnotations).  
  ```razor  
  <EditForm Model="@model" OnValidSubmit="HandleSubmit">  
    <DataAnnotationsValidator />  
    <ValidationSummary />  
  </EditForm>  
  ```  

---

### **4. Interoperabilita s JavaScriptem**  

- **Volání JS z C#**:  
  ```csharp  
  await JSRuntime.InvokeVoidAsync("alert", "Ahoj!");  
  ```  
- **Volání C# z JS**:  
  ```javascript  
  DotNet.invokeMethodAsync("MojeKnihovna", "MetodaVCsharp", data);  
  ```  

---

### **5. Správa stavu**  

- **Stavové služby**:  
  - Vytvoření singletonu/scoped služby pro sdílení stavu mezi komponentami.  
- **Parametry a události**:  
  - Předávání dat z rodiče na dítě přes `[Parameter]`.  
  - Komunikace zpět přes `EventCallback`.  

---

### **6. Výkon a optimalizace**  

- **Prerendering**: Statické vygenerování HTML pro lepší SEO a rychlost.  
- **Virtualizace**: Efektivní renderování dlouhých seznamů (`<Virtualize>`).  
- **Lazy Loading**: Načítání komponent až při potřebě.  

---

### **7. Bezpečnost**  

- **Autentizace a autorizace**:  
  - Atribut `[Authorize]` pro omezení přístupu.  
  - Integrace s ASP.NET Core Identity.  
- **Ochrana proti XSS**: Automatické escapování výstupů.  

---

### **8. Nasazení**  

- **Blazor Server**: Hostování na IIS, Azure App Service.  
- **Blazor WebAssembly**: Statické hostování (Azure Static Web Apps, CDN).  

---

### **9. Omezení a výzvy** 

- **Velikost WebAssembly**: Optimalizace pomocí AOT kompilace.  
- **Kompatibilita JS**: Omezení při práci s některými knihovnami.  

---

**Shrnutí**:  
- Razor komponenty umožňují **jednotný vývojový model** pro server i klienta.  
- Integrace s ASP.NET Core zajišťuje **pružnost** (MVC, Razor Pages, API).  
- Klíčové výhody: **interaktivita, sdílení kódu, výkon**.  

**Užitečné zdroje**:  
- [Oficiální dokumentace](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/)  
