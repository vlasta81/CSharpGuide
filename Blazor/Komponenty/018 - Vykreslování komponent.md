
## **Vykreslování komponent v ASP.NET Core Blazor**

---

### **1. Konvence vykreslování pro `ComponentBase`**  

- **Základní třída**: Všechny komponenty dědí z `ComponentBase`, která definuje životní cyklus a logiku vykreslování.  
- **Životní cyklus**:  
  - **`OnInitialized{Async}`**: Volá se při vytvoření komponenty (inicializace dat).  
  - **`OnParametersSet{Async}`**: Reakce na změnu parametrů od rodiče.  
  - **`OnAfterRender{Async}`**: Spustí se po vykreslení UI (práce s DOM/JS).  
- **Automatické vykreslování**: Blazor detekuje změny stavu (data, parametry) a automaticky překreslí komponentu.  

---

### **2. Řízení toku vykreslování**  

- **Automatické aktualizace**: Blazor vyvolá překreslení při změně parametrů, stavu nebo událostech (kliknutí, input).  
- **Manuální kontrola**:  
  - **`StateHasChanged()`**: Vynutí překreslení komponenty (např. po asynchronní operaci).  
  - **`ShouldRender()`**: Metoda vracející `bool`, která určuje, zda má komponenta překreslit.  
    ```csharp  
    protected override bool ShouldRender()  
    {  
        return _needsUpdate; // Překreslí se pouze pokud je true  
    }  
    ```  

---

### **3. Vykreslování streamování (Streaming Rendering)**  

- **Princip**: Postupné odesílání obsahu klientovi, uživatel vidí částečný výsledek během načítání.  
- **Použití**:  
  - Serverové aplikace (Blazor Server) pro zobrazení průběžného stavu.  
  - **Atribut `[StreamRendering]`**: Povolí streamování pro komponentu nebo celou aplikaci.  
  ```csharp  
  [StreamRendering]  
  public class App : ComponentBase { ... }  
  ```  
- **Příklad**:  
  ```razor  
  <StreamingComponent>  
      <LoadingTemplate>Načítám...</LoadingTemplate>  
      <LoadedTemplate>@content</LoadedTemplate>  
  </StreamingComponent>  
  ```  

---

### **4. Potlačení aktualizace UI (`ShouldRender`)** 

- **Optimalizace výkonu**: Překreslení se vyhne, pokud není potřeba.  
- **Implementace**:  
  ```csharp  
  private bool _dataChanged = false;  

  protected override bool ShouldRender()  
  {  
      if (!_dataChanged)  
          return false;  
      _dataChanged = false;  
      return true;  
  }  
  ```  

---

### **5. `StateHasChanged()`**  

- **Účel**: Signalizuje, že komponenta má překreslit svůj UI (např. po změně dat mimo standardní události).  
- **Použití**:  
  ```csharp  
  private async Task LoadData()  
  {  
      data = await HttpClient.GetAsync(...);  
      StateHasChanged(); // Vynutí překreslení po načtení  
  }  
  ```  

---

### **6. Indikátor průběhu načítání WebAssembly**  

- **Problém**: Blazor WebAssembly vyžaduje stáhnout .NET runtime a binárky → uživatel vidí prázdnou stránku.  
- **Řešení**:  
  1. V `index.html` přidejte indikátor načítání:  
  ```html  
  <div id="loading">  
      <div class="spinner"></div>  
      <p>Načítám aplikaci...</p>  
  </div>  
  ```  
  2. Skryjte indikátor po načtení:  
  ```javascript  
  Blazor.start().then(() => {  
      document.getElementById("loading").style.display = "none";  
  });  
  ```  

---

### **7. Shrnutí**  

| **Funkce**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **`ComponentBase`**      | Základní třída s životním cyklem pro vykreslování.                        |  
| **Streamování**          | Postupné zobrazování obsahu během načítání (pro lepší UX).                |  
| **`ShouldRender`**       | Optimalizace výkonu přeskočením zbytečných překreslení.                   |  
| **`StateHasChanged`**    | Manuální vyvolání překreslení.                                            |  
| **Indikátor načítání**   | Vizualizace průběhu inicializace WebAssembly.                             |  

**Klíčové body**:  
- Blazor kombinuje automatické a manuální řízení vykreslování pro flexibilitu.  
- Streamování a indikátory načítání zlepšují uživatelskou zkušenost.  
- Optimalizace pomocí `ShouldRender` je klíčová pro výkon u komplexních komponent.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Rendering](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/rendering).  
