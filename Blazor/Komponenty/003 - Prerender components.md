
## **Prerendering komponent v ASP.NET Core Blazor**  

Prerendering (předvykreslování) je technika, při které se komponenta **nejprve vygeneruje na serveru jako statické HTML** a následně se na straně klienta přepne do interaktivního režimu. Tento přístup kombinuje výhody rychlého zobrazení obsahu s možností interakce po načtení stránky.

---

### **1. Co je Prerendering?**  

- **Definice**:  
  - Server vygeneruje HTML komponenty včetně počátečního stavu a odešle jej klientovi.  
  - Po načtení stránky se komponenta "oživí" a přepne do interaktivního režimu (Blazor Server nebo WebAssembly).  
- **Cíl**:  
  - Zlepšit **SEO** (vyhledávače vidí obsah ihned).  
  - Snížit **FCP (First Contentful Paint)** – uživatel vidí obsah rychleji.  

---

### **2. Kdy použít Prerendering?**  

- **Projekty s důrazem na SEO** (veřejné weby, e-shopy).  
- **Aplikace, kde je rychlost vnímání klíčová** (např. dashboardy s daty).  
- **Kombinace statického a interaktivního obsahu** v jedné stránce.  

---

### **3. Jak funguje Prerendering?**  

1. **Požadavek na server**: Uživatel otevře URL.  
2. **Generování HTML na serveru**:  
   - Server spustí komponentu, provede inicializační logiku (např. načte data z DB).  
   - Vygeneruje HTML s počátečním stavem.  
3. **Odeslání HTML klientovi**: Uživatel okamžitě vidí obsah.  
4. **Hydratace (oživení)**:  
   - Blazor runtime stáhne potřebné prostředky (WASM nebo SignalR).  
   - Komponenta se přepne do interaktivního režimu.  

---

### **4. Nastavení Prerenderingu**  

#### **a) Blazor Server**  

- Prerendering je **výchozí** pro komponenty s direktivou `@rendermode InteractiveServer`.  
- Explicitní nastavení v `App.razor`:  
  ```razor
  <Routes @rendermode="new InteractiveServerRenderMode(prerender: true)" />
  ```

#### **b) Blazor WebAssembly**  

- Prerendering vyžaduje **hostování aplikace na serveru** (např. pomocí ASP.NET Core).  
- Konfigurace v `_Host.cshtml` (Server projekt):  
  ```razor
  <component type="typeof(App)" render-mode="ServerPrerendered" />
  ```

#### **c) Komponenta s Prerenderingem**  

```razor
@page "/data"
@rendermode InteractiveServer  
@attribute [Prerender] // Volitelné explicitní označení

<h3>@Data</h3>

@code {
    private string Data { get; set; } = "";

    protected override async Task OnInitializedAsync()
    {
        // Tento kód se spustí na serveru během prerenderingu
        Data = await NačtiDataZDatabáze();
    }
}
```

---

### **5. Životní cyklus komponenty s Prerenderingem**  

- **OnInitializedAsync()**:  
  - Spustí se **dvakrát** – nejprve na serveru (prerendering), poté na klientovi (po hydrataci).  
  - **Pozor**: Pokud načítáte data, mohou se stáhnout dvakrát (řešte pomocí cache nebo podmínek).  
- **OnAfterRenderAsync(bool firstRender)**:  
  - `firstRender = true` až po dokončení hydratace.  

---

### **6. Omezení a řešení**  

#### **a) Problémy s dvojím voláním kódu**  

- **Příklad**: Dvojí načtení dat z API.  
- **Řešení**:  
  ```csharp
  protected override async Task OnInitializedAsync()
  {
      if (!OperatingSystem.IsBrowser()) // Spustí se pouze na serveru
      {
          Data = await NačtiData();
      }
  }
  ```

#### **b) Práce s prohlížečovými API**  

- **Problém**: API jako `localStorage` nelze použít během prerenderingu na serveru.  
- **Řešení**: Spouštět takový kód až v `OnAfterRenderAsync`:  
  ```csharp
  protected override async Task OnAfterRenderAsync(bool firstRender)
  {
      if (firstRender)
      {
          await JSRuntime.InvokeVoidAsync("uložDoStorage", "klíč", "hodnota");
      }
  }
  ```

---

### **7. Výhody a nevýhody**  

| **Výhody**                          | **Nevýhody**                          |  
|-------------------------------------|----------------------------------------|  
| Rychlé zobrazení obsahu             | Složitější ladění (dvojí spuštění kódu)|  
| Lepší SEO                           | Nutnost řešit kompatibilitu server-klient |  
| Plynulý přechod do interaktivního režimu | Vyšší nároky na serverový výkon |  

---

### **8. Ukázka: Prerendering s načítáním dat**  

```razor
@page "/users"
@rendermode InteractiveServer  
@attribute [Prerender]

@if (Users == null)
{
    <p>Načítám...</p>
}
else
{
    <ul>
        @foreach (var user in Users)
        {
            <li>@user.Name</li>
        }
    </ul>
}

@code {
    private List<User>? Users { get; set; }

    protected override async Task OnInitializedAsync()
    {
        // Na serveru se načtou data, na klientovi se pouze zobrazí
        if (!OperatingSystem.IsBrowser())
        {
            Users = await UserService.GetUsersAsync();
        }
    }
}
```

---

### **Shrnutí**  

- Prerendering **zrychluje vnímání aplikace** a **zlepšuje SEO**.  
- **Klíčové kroky**:  
  - Nastavit `prerender: true` v render módu.  
  - Ošetřit dvojí spouštění kódu (server + klient).  
  - Citlivé operace (JS Interop) provádět až po hydrataci.  
- **Používejte**:  
  - Pro veřejné stránky s dynamickým obsahem.  
  - Pokud potřebujete kombinovat rychlost a interaktivitu.  

**Užitečné zdroje**:  
- Dokumentace: [Prerendering v Blazor](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/prerendering).  
