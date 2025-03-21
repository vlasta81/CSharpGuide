
## **Osvědčené postupy pro výkon ASP.NET Core Blazor**

---

### **1. Definice**  

Osvědčené postupy pro výkon v Blazor jsou techniky a strategie, které optimalizují rychlost, odezvu a efektivitu aplikace. Cílí na minimalizaci latence, snížení přenášených dat a efektivní správu zdrojů, a to jak v **Blazor WebAssembly**, tak **Blazor Server**.

---

### **2. Klíčové oblasti optimalizace**  

- **Optimalizace vykreslování komponent** (omezit zbytečné re-rendery).  
- **Správa paměti a prostředků** (uvolňování objektů, dispose pattern).  
- **Efektivní komunikace se serverem** (minimalizace HTTP požadavků, velikost dat).  
- **Optimalizace velikosti aplikace** (WebAssembly: AOT, trimming, lazy loading).  
- **Využití asynchronních operací** (neblokování hlavního vlákna).  

---

### **3. Hlavní doporučené postupy**  

#### **A. Komponentové optimalizace**  

- **Používejte `Virtualize` pro velké seznamy**:  
  ```razor
  <Virtualize Items="@data" Context="item">
      <div>@item.Name</div>
  </Virtualize>
  ```
- **Omezte re-rendery pomocí `ShouldRender`**:  
  ```csharp
  protected override bool ShouldRender() => _shouldRender;
  ```
- **Používejte `@key` pro dynamické seznamy**:  
  ```razor
  @foreach (var item in items) {
      <Component @key="item.Id" />
  }
  ```
- **Vyhněte se kaskádovým parametrům** (`[CascadingParameter]`), pokud nejsou nutné.

#### **B. Optimalizace Blazor WebAssembly** 

- **AOT (Ahead-of-Time) kompilace**:  
  Přidejte do `.csproj`:  
  ```xml
  <RunAOTCompilation>true</RunAOTCompilation>
  ```
  *Pozor: Zvýší velikost aplikace, ale zrychlí výkon.*  
- **Trimování nepoužívaného kódu**:  
  ```xml
  <PublishTrimmed>true</PublishTrimmed>
  ```
- **Opožděné načítání sestavení** (Lazy Loading) – viz [předchozí souhrn](link).  
- **Komprese prostředků**: Aktivujte GZIP/Brotli na serveru.  

#### **C. Optimalizace Blazor Server**  

- **Snižte velikost stavu okruhu (circuit state)**:  
  - Ukládejte pouze nezbytná data v paměti serveru.  
  - Používejte `PreserveComponentState` pro obnovení stavu po reconnectu.  
- **Omezte synchronní operace**:  
  ```csharp
  // Špatně: Blokuje vlákno
  var data = _service.GetData().Result;

  // Správně: Asynchronní přístup
  var data = await _service.GetDataAsync();
  ```
- **Nastavte časový limit nečinnosti**:  
  ```csharp
  services.AddServerSideBlazor(o => o.DisconnectedCircuitRetentionPeriod = TimeSpan.FromMinutes(2));
  ```

#### **D. Obecné techniky**  

- **Minimalizujte JS interop**: Častá volání JS z C# zpomalují aplikaci.  
- **Používejte caching**:  
  - **Na straně klienta**: `MemoryCache` nebo `localStorage`.  
  - **Na straně serveru**: `ResponseCache` atributy.  
- **Optimalizujte obrázky a statické soubory**:  
  - Komprese.  
  - Použití CDN.  

---

### **4. Omezení a varování**  

- **AOT kompilace**: Zvýší velikost aplikace až 2×.  
- **Blazor Server**: Latence sítí může degradovat výkon.  
- **Přílišné dělení komponent**: Může zvýšit komplexitu bez výhod.  
- **Nekompatibilita s některými knihovnami**: Např. reflexe nefunguje s trimmingem.  

---

### **5. Nástroje pro analýzu výkonu**  

- **Blazor DevTools**: Integrované v prohlížeči (komponentový životní cyklus, renderování).  
- **Lighthouse/WebPageTest**: Analýza načítání a SEO.  
- **ASP.NET Core Metrics**: Sledování využití paměti a latence na serveru.  

---

### **6. Typické chyby** 

- **Nadměrné volání `StateHasChanged()`**: Způsobuje zbytečné re-rendery.  
- **Memory leaks**: Neukládání event listenerů nebo služeb s `IDisposable`.  
- **Blokující volání v Blazor Server**: Synchronní operace blokují vlákna.  
- **Ignorování velikosti balíčků**: Nepoužití trimmingu nebo komprese.  

---

### **7. Shrnutí**  

- **Blazor WebAssembly**: Zaměřte se na velikost aplikace (AOT, trimming, lazy loading).  
- **Blazor Server**: Optimalizujte stav okruhu a asynchronní operace.  
- **Všechny modely**: Omezte re-rendery, používejte caching a efektivní správu paměti.  
- **Testujte výkon průběžně**: Identifikujte úzká místa nástroji jako Lighthouse nebo profiler.  

Výkon Blazor aplikací závisí na kombinaci správného návrhu komponent, optimalizace komunikace a využití vestavěných funkcí .NET. Klíčem je **preventivní přístup** – integrujte optimalizace již během vývoje, neaž při problémech.
