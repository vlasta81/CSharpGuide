
## **Životní cyklus komponent v ASP.NET Core Blazor**  

Životní cyklus komponent definuje **pořadí metod**, které se volají během vytváření, aktualizace a zániku komponenty. Porozumění těmto fázím je klíčové pro správnou inicializaci dat, reakci na změny a úklid prostředků.

---

### **1. Hlavní fáze životního cyklu**  

#### **a) Inicializace komponenty**  

- **Metody**:  
  - **`OnInitialized()`**: Synchronní inicializace (např. nastavení výchozích hodnot).  
  - **`OnInitializedAsync()`**: Asynchronní inicializace (např. načítání dat z API).  
- **Kdy se volá**: Při prvním vytvoření komponenty.  
- **Příklad**:  
  ```csharp  
  protected override async Task OnInitializedAsync()  
  {  
      data = await HttpClient.GetFromJsonAsync("api/data");  
  }  
  ```  

#### **b) Nastavení parametrů**  

- **Metody**:  
  - **`OnParametersSet()`**: Synchronní reakce na změnu parametrů od rodiče.  
  - **`OnParametersSetAsync()`**: Asynchronní reakce na změnu parametrů.  
- **Kdy se volá**: Po inicializaci a při každé změně parametrů (např. aktualizace rodičovských dat).  
- **Příklad**:  
  ```csharp  
  protected override void OnParametersSet()  
  {  
      if (Parametr != null)  
          internalValue = Parametr.Value;  
  }  
  ```  

#### **c) Vykreslení komponenty**  

- **Metoda**: **`StateHasChanged()`**: Manuální vyvolání překreslení UI (např. po asynchronní operaci).  

#### **d) Po vykreslení**  

- **Metody**:  
  - **`OnAfterRender(bool firstRender)`**: Synchronní kód po vykreslení UI (např. práce s JavaScriptem).  
  - **`OnAfterRenderAsync(bool firstRender)`**: Asynchronní kód po vykreslení.  
- **Parametr `firstRender`**: `true` pouze při prvním vykreslení.  
- **Příklad**:  
  ```csharp  
  protected override async Task OnAfterRenderAsync(bool firstRender)  
  {  
      if (firstRender)  
          await JSRuntime.InvokeVoidAsync("inicializujKnihovnu");  
  }  
  ```  

#### **e) Uvolnění prostředků**  

- **Metoda**: **`Dispose()`**: Pro uvolnění prostředků (otevřená spojení, timery).  
- **Implementace**: Komponenta musí implementovat `IDisposable`.  
- **Příklad**:  
  ```csharp  
  @implements IDisposable  
  @code {  
      private Timer timer;  

      public void Dispose()  
      {  
          timer?.Dispose();  
      }  
  }  
  ```  

---

### **2. Posloupnost volání metod**  

1. **`OnInitialized`** → **`OnInitializedAsync`**  
2. **`OnParametersSet`** → **`OnParametersSetAsync`**  
3. **Vykreslení UI**  
4. **`OnAfterRender`** → **`OnAfterRenderAsync`**  
5. Při změně parametrů: **`OnParametersSet`** → **Vykreslení** → **`OnAfterRender`**  
6. Při zničení komponenty: **`Dispose`**.

---

### **3. Časté scénáře a doporučení**  

#### **a) Načítání dat**  

- Použijte **`OnInitializedAsync`** pro asynchronní načítání dat (např. z API).  
- **Pozor**: Pokud data závisí na parametrech, použijte **`OnParametersSetAsync`**.  

#### **b) Reakce na změnu parametrů** 

- Aktualizujte interní stav v **`OnParametersSet`** na základě nových hodnot parametrů.  

#### **c) Integrace s JavaScriptem**  

- Volání JS funkcí provádějte až v **`OnAfterRender(Async)`**, protože DOM musí být připraven.  

#### **d) Optimalizace výkonu**  

- Používejte **`StateHasChanged`** pouze při nutnosti (Blazor často aktualizuje UI automaticky).  

---

### **4. Tabulka životního cyklu**  

| **Metoda**               | **Popis**                                  | **Async verze**       |  
|--------------------------|--------------------------------------------|-----------------------|  
| `OnInitialized`          | Inicializace komponenty                    | `OnInitializedAsync`  |  
| `OnParametersSet`        | Reakce na změnu parametrů                  | `OnParametersSetAsync`|  
| `OnAfterRender`          | Kód po vykreslení UI                       | `OnAfterRenderAsync`  |  
| `Dispose`                | Úklid prostředků                           | –                     |  

---

### **5. Příklady**  

#### **a) Komponenta s timerem**  

```csharp  
@implements IDisposable  
@code {  
    private Timer timer;  
    private int count = 0;  

    protected override void OnInitialized()  
    {  
        timer = new Timer(_ => count++, null, 0, 1000);  
    }  

    public void Dispose()  
    {  
        timer?.Dispose();  
    }  
}  
```  

#### **b) Reakce na změnu parametru**  

```csharp  
[Parameter]  
public int UserId { get; set; }  

protected override async Task OnParametersSetAsync()  
{  
    if (UserId != 0)  
        user = await UserService.GetUserAsync(UserId);  
}  
```  

---

### **Shrnutí**  

- Životní cyklus komponent umožňuje **řídit inicializaci, aktualizaci a úklid**.  
- **Klíčové metody**:  
  - `OnInitialized(Async)`: Pro načítání dat při vytvoření komponenty.  
  - `OnParametersSet(Async)`: Pro reakci na změny parametrů.  
  - `OnAfterRender(Async)`: Pro práci s vykresleným DOM.  
  - `Dispose`: Pro uvolnění prostředků.  
- **Doporučení**: Vyhněte se blokujícím operacím v synchronních metodách.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Component Lifecycle](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/lifecycle).  
