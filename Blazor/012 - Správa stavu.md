
## **Správa stavu v ASP.NET Core Blazor**

---

### **1. Úvod do správy stavu**  

Správa stavu v Blazor zahrnuje udržování dat mezi komponentami a relacemi uživatelů. Blazor podporuje různé přístupy v závislosti na hostitelském modelu (Server/WebAssembly).

---

### **2. Udržování stavu uživatele**  

- **Blazor Server**:  
  - Stav je vázán na **okruh (circuit)** – při ztrátě spojení (např. obnovení stránky) se stav ztrácí.  
  - Řešení: Ukládání do serverového úložiště nebo prohlížeče.  
- **Blazor WebAssembly**:  
  - Stav je v paměti prohlížeče, ale resetuje se při obnovení stránky.  
  - Řešení: Ukládání do `localStorage` nebo API.

---

### **3. Zachování stavu napříč okruhy (Blazor Server)**  

- **Persistentní stav**:  
  - **`PersistentComponentState`** (od .NET 6): Umožňuje serializaci stavu během prerenderingu.  
  - **Serverové úložiště**: Databáze, Redis, nebo `IMemoryCache` (s časovým omezením).  
  - **Prohlížečové úložiště**: `localStorage`/`sessionStorage` přes JS Interop.  

---

### **4. Kde zachovat stav**  

| **Metoda**                  | **Vhodné pro**                               | **Bezpečnost**                     |  
|-----------------------------|---------------------------------------------|------------------------------------|  
| **Serverové úložiště**      | Citlivá data, dlouhodobý stav.              | Vysoká (šifrování na serveru).     |  
| **URL parametry**           | Sdílení stavu mezi stránkami/komponentami.  | Nízká (data viditelná v URL).      |  
| **Browser Storage**         | Dočasný stav (WebAssembly).                 | Střední (šifrování pomocí `ProtectedLocalStorage`). |  
| **Stavový kontejner**       | Sdílení stavu mezi komponentami.            | Závisí na implementaci.            |  

---

### **5. Podrobněji k úložištím** 

#### **a) Úložiště na straně serveru**  

- **Použití `IMemoryCache`**:  
  ```csharp  
  builder.Services.AddMemoryCache();  
  ```  
  ```csharp  
  [Inject] private IMemoryCache Cache { get; set; }  
  Cache.Set("klíč", data, TimeSpan.FromMinutes(10));  
  ```  

#### **b) URL parametry**  

- **Předávání stavu v cestě**:  
  ```razor  
  @page "/produkt/{Id:int}"  
  [Parameter] public int Id { get; set; }  
  ```  

#### **c) Úložiště prohlížeče**  

- **`localStorage`/`sessionStorage`**:  
  - **JS Interop**:  
    ```javascript  
    // wwwroot/js/storage.js  
    function setItem(key, value) { localStorage.setItem(key, value); }  
    ```  
    ```csharp  
    await JSRuntime.InvokeVoidAsync("setItem", "klíč", "hodnota");  
    ```  
  - **Chráněné úložiště (`ProtectedLocalStorage`)**:  
    ```csharp  
    builder.Services.AddProtectedLocalStorage();  
    ```  
    ```csharp  
    [Inject] private ProtectedLocalStorage Storage { get; set; }  
    await Storage.SetAsync("klíč", data); // Data jsou šifrována.  
    ```  

#### **d) Služba kontejneru stavu v paměti**  

- **Vytvoření stavové služby**:  
  ```csharp  
  public class StateContainer  
  {  
      public string UserFilter { get; set; }  
      public event Action OnChange;  
      public void NotifyStateChanged() => OnChange?.Invoke();  
  }  
  ```  
- **Registrace v DI**:  
  ```csharp  
  builder.Services.AddSingleton<StateContainer>();  
  ```  
- **Použití v komponentě**:  
  ```csharp  
  [Inject] private StateContainer State { get; set; }  
  State.UserFilter = "admin";  
  State.NotifyStateChanged();  
  ```  

---

### **6. Další přístupy**  

- **Cascading Parameters**: Předávání stavu hierarchií komponent.  
- **Fluxor/Redux**: Knihovny pro správu stavu s unidirekcionálním tokem dat.  
- **Event Callbacks**: Komunikace mezi rodičem a dítětem.  

---

### **7. Odstraňování potíží** 

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|--------------------------------------------|  
| **Ztráta stavu při obnovení stránky** | Použijte `ProtectedLocalStorage` nebo API. |  
| **Neaktualizovaný stav v kontejneru** | Zajistěte volání `NotifyStateChanged()`.   |  
| **Chyby serializace**                | Ověřte, zda objekty jsou serializovatelné. |  
| **Paměťové úniky**                   | Odhlaste event handlery v `Dispose()`.     |  

---

### **8. Shrnutí**  

- **Blazor Server**: Stav je křehký – ukládejte důležitá data externě.  
- **Blazor WebAssembly**: Stav přežije pouze v rámci relace prohlížeče.  
- **Bezpečnost**: Pro citlivá data použijte šifrovaná úložiště (`ProtectedLocalStorage`) nebo server.  
- **Nástroje**: `StateContainer`, `ProtectedLocalStorage`, Fluxor, nebo URL parametry.  

**Užitečné zdroje**:  
- [Dokumentace: Správa stavu v Blazoru](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/state-management)  
- [Příklady Fluxor](https://github.com/mrpmorris/fluxor)
