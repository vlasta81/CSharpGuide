
## **Souhrn: Interoperabilita JavaScriptu s ASP.NET Core Blazor**  

Interoperabilita JavaScriptu (JS Interop) umožňuje komunikaci mezi kódem C# v Blazor a JavaScriptem. To je nezbytné pro využití funkcí prohlížeče, existujících JS knihoven nebo manipulaci s DOM, které Blazor sám neposkytuje.

---

### **1. Proč používat JS Interop?**  

- **Integrace s JS knihovnami** (např. charting, mapy).  
- **Práce s API prohlížeče** (localStorage, geolokace, kamera).  
- **Manipulace s DOM** (práce s elementy, které Blazor komponenty neřeší).  

---

### **2. Základní mechanismy**  

#### **a) Volání JavaScriptu z C#**  

- **Rozhraní `IJSRuntime`**: Služba pro asynchronní komunikaci s JS.  
  - **Metody**:  
    - `InvokeVoidAsync("funkce", args)`: Volá JS funkci bez návratové hodnoty.  
    - `InvokeAsync<T>("funkce", args)`: Volá JS funkci a vrací výsledek typu `T`.  

- **Příklad**:  
  ```csharp
  @inject IJSRuntime JSRuntime

  private async Task ZobrazAlert()
  {
      await JSRuntime.InvokeVoidAsync("alert", "Ahoj z Blazor!");
  }
  ```  

#### **b) Volání C# z JavaScriptu**  

- **Metody označené `[JSInvokable]`**:  
  - Lze volat z JS pomocí `DotNet.invokeMethodAsync()`.  
  - **Příklad**:  
    ```csharp
    [JSInvokable]
    public static Task<string> VratAhoj(string jmeno)
    {
        return Task.FromResult($"Ahoj, {jmeno}!");
    }
    ```  
  - **Volání z JS**:  
    ```javascript
    async function pozdrav() {
        const odpoved = await DotNet.invokeMethodAsync("MáAplikace", "VratAhoj", "Jan");
        console.log(odpoved); // Výstup: "Ahoj, Jan!"
    }
    ```  

---

### **3. JavaScript Isolation (moduly ES6)**  

- **Cíl**: Načítat JS kód dynamicky a izolovat ho od globálního scope.  
- **Kroky**:  
  1. Vytvořte JS soubor v `wwwroot/js` (např. `mujModul.js`):  
     ```javascript
     export function showAlert(zprava) {
         alert(zprava);
     }
     ```  
  2. Načtěte modul v C#:  
     ```csharp
     var module = await JSRuntime.InvokeAsync<IJSObjectReference>("import", "/js/mujModul.js");
     await module.InvokeVoidAsync("showAlert", "Modul funguje!");
     ```  

---

### **4. Práce s DOM elementy**  

- **Reference na elementy**:  
  ```razor
  <input @ref="mujInput" />
  @code {
      private ElementReference mujInput;
      
      private async Task FocusniInput()
      {
          await JSRuntime.InvokeVoidAsync("focusElement", mujInput);
      }
  }
  ```  
- **JavaScript pro manipulaci**:  
  ```javascript
  function focusElement(element) {
      element.focus();
  }
  ```  

---

### **5. Ošetření chyb**  

- **Zachycení výjimek**:  
  ```csharp
  try
  {
      await JSRuntime.InvokeVoidAsync("neexistujiciFunkce");
  }
  catch (JSException ex)
  {
      Console.WriteLine($"Chyba JS: {ex.Message}");
  }
  ```  

---

### **6. Uvolňování prostředků**  

- **Dispose pattern** pro JS objekty:  
  ```csharp
  private IJSObjectReference? module;

  protected override async Task OnAfterRenderAsync(bool firstRender)
  {
      module = await JSRuntime.InvokeAsync<IJSObjectReference>("import", "/js/mujModul.js");
  }

  public void Dispose()
  {
      module?.Dispose();
  }
  ```  

---

### **7. Časté use cases**  

- **Integrace s třetími knihovnami** (např. Chart.js, Leaflet).  
- **Práce s localStorage/sessionStorage**:  
  ```javascript
  function ulozDoStorage(klic, hodnota) {
      localStorage.setItem(klic, hodnota);
  }
  ```  
- **Animace nebo komplexní UI efekty**.  

---

### **8. Omezení a doporučení**  

- **Výkon**: Časté volání JS Interop může zpomalit aplikaci.  
- **Bezpečnost**: Nevystavujte citlivou logiku v JS.  
- **Alternativy**: Preferujte čisté Blazor řešení, pokud existuje (např. Blazorova `LocalStorage služba` místo JS Interop).  

---

### **Shrnutí**  

| **Scénář**               | **C# → JS**                          | **JS → C#**                          |  
|--------------------------|--------------------------------------|---------------------------------------|  
| **Syntaxe**              | `IJSRuntime.InvokeVoidAsync()`       | `[JSInvokable]` + `DotNet.invokeMethodAsync()` |  
| **Použití**              | Práce s DOM, knihovnami, prohlížečem | Zpětná volání, aktualizace stavu z JS |  
| **Moduly**               | `import` + `IJSObjectReference`      | –                                     |  

**Klíčové body**:  
- JS Interop je most mezi Blazor a světem JavaScriptu.  
- Pro složité interakce použijte izolované JS moduly.  
- Vždy ošetřujte výjimky a uvolňujte prostředky.  

**Užitečné zdroje**:  
- Oficiální dokumentace: [JS Interop v Blazor](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/javascript-interoperability/).  
