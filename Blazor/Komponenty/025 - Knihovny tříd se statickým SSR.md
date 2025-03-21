
## **Razor knihovny tříd (RCLs) se statickým server-side renderingem (SSR) v ASP.NET Core**

---

### **1. Úvod do statického SSR**  

- **Definice**: Statické SSR vygeneruje HTML na serveru a odešle jej klientovi **bez interaktivity** (žádné Blazor WebAssembly ani SignalR spojení).  
- **Využití**:  
  - **Statický obsah** (např. dokumentace, blogy).  
  - **SEO optimalizace** (vyhledávače vidí kompletní HTML).  
  - **Rychlé zobrazení** prvního obsahu (FCP – First Contentful Paint).  

---

### **2. Možnosti a omezení statického SSR**  

#### **Možnosti**:  

- **Prerendering komponent**: Komponenty se vygenerují na serveru jako čisté HTML.  
- **Integrace s MVC/Razor Pages**: Statické Blazor komponenty lze vkládat do tradičních stránek.  
- **Streamování obsahu**: Postupné odesílání HTML při pomalém načítání dat (např. čekání na API).  

#### **Omezení**:  

- **Žádná interaktivita**: Nelze používat Blazorové události (kliknutí, vstupy) ani JS Interop.  
- **Žádný stav komponenty**: Po načtení stránky nelze aktualizovat UI bez obnovení.  
- **Omezené životní cykly**: Metody jako `OnAfterRender` nejsou volány.  

---

### **3. Možnosti pro autory komponent**  

- **Návrh univerzálních komponent**:  
  - Používejte **parametry** pro předávání dat.  
  - Vyhněte se závislosti na `IJSRuntime`, `NavigationManager`, nebo stavových službách.  
- **Podmíněný kód pro SSR**:  
  ```csharp  
  if (OperatingSystem.IsBrowser())  
  {  
      // Kód specifický pro WebAssembly  
  }  
  ```  

---

### **4. Kdy použít direktivu `@rendermode`**  

- **Účel**: Přepnout komponentu do **interaktivního režimu** (Blazor Server/WebAssembly).  
- **Použití**:  
  ```razor  
  @rendermode InteractiveServer  
  <!-- nebo -->  
  @rendermode InteractiveWebAssembly  
  ```  
- **Kdy**: Pokud komponenta vyžaduje interaktivitu (formuláře, animace, JS Interop).  

---

### **5. Vykreslování streamování (Streaming Rendering)**  

- **Princip**: Postupné odesílání HTML z serveru, zatímco se načítají data.  
- **Použití**:  
  - Pro dlouho běžící operace (např. dotazy do databáze).  
  - Aktivuje se pomocí `@attribute [StreamRendering]` v komponentě.  
  ```razor  
  @attribute [StreamRendering]  
  @if (data == null)  
  {  
      <p>Načítám data...</p>  
  }  
  else  
  {  
      <p>@data</p>  
  }  
  ```  

---

### **6. Použití odkazů a formulářů napříč režimy vykreslování**  

- **Odkazy**:  
  - Vždy používejte **relativní cesty** (např. `/kontakt` místo `https://example.com/kontakt`).  
  - Pro navigaci mezi interaktivními komponentami použijte `NavigationManager` (pouze v interaktivním režimu).  
- **Formuláře**:  
  - Statické SSR formuláře odesílají data přes HTTP POST (tradiční způsob).  
  - Pro interaktivitu použijte Blazorové `EditForm` s `@rendermode`.  

---

### **7. Vyhněte se API specifickým pro statické SSR**  

- **Zakázaná API**:  
  - `IJSRuntime` (nelze volat JavaScript).  
  - `NavigationManager.NavigateTo` (nelze měnit URL bez obnovení stránky).  
  - Stavové služby (např. `AuthenticationStateProvider` v SSR).  
- **Alternativy**:  
  - Pro dynamické aktualizace použijte **interaktivní režim**.  
  - Pro komunikaci s backendem použijte **API endpoints** (REST/gRPC).  

---

### **8. Best Practices**  

1. **Separace logiky**: Oddělte kód pro SSR a interaktivní režimy pomocí podmínek.  
2. **Testování**: Ověřte chování komponent v obou režimech.  
3. **Dokumentace**: U komponent jasně uveďte, zda podporují pouze SSR nebo i interaktivitu.  

---

### **Shrnutí**  

| **Aspekt**               | **Doporučení**                                                           |  
|--------------------------|---------------------------------------------------------------------------|  
| **Statické SSR**          | Ideální pro obsah bez interakce a SEO.                                    |  
| **Interaktivita**         | Používejte `@rendermode` pro formuláře, animace, JS Interop.              |  
| **Streamování**           | Aktivujte pro pomalé načítání dat.                                        |  
| **Kompatibilita**         | Vyhýbejte se API, která fungují pouze v interaktivních režimech.          |  

**Užitečné zdroje**:  
- [Oficiální dokumentace: Blazor Rendering Modes](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/render-modes)  
