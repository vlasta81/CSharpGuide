
## **Kontext synchronizace v komponentách ASP.NET Core Blazor**  

Kontext synchronizace (**Synchronization Context**) v Blazor zajišťuje, že operace ovlivňující UI (např. aktualizace stavu komponenty) jsou prováděny **na správném vlákně**. Tento mechanismus je klíčový pro bezpečnou práci s asynchronními operacemi a prevenci race conditions.

---

### **1. Proč je kontext synchronizace důležitý?**  

- Blazor aplikace jsou **single-threaded** (používají hlavní vlákno pro UI).  
- Asynchronní operace (např. volání API) mohou probíhat na **pozadí**, ale jejich výsledky musí být zpracovány na **hlavním vlákně**, aby nedošlo k poškození UI.  
- **Příklad problému bez synchronizace**:  
  ```csharp
  private async Task NačtiData()
  {
      await Task.Delay(1000); // Simulace asynchronní operace
      // Bez synchronizace by tato změna mohla být provedena na špatném vlákně → chyba
      data = await HttpClient.GetFromJsonAsync(...);
  }
  ```

---

### **2. Jak Blazor řeší synchronizaci?**  

- **Automatické zachycení kontextu**:  
  - Při spuštění asynchronní metody (např. `OnInitializedAsync`) Blazor automaticky zachytí aktuální kontext synchronizace.  
  - Po dokončení asynchronní operace se kód vrátí do původního kontextu (hlavního vlákna).  
- **Ruční správa pomocí `InvokeAsync`**:  
  - Pokud operace běží na pozadí (např. v `Task.Run`), je nutné použít `InvokeAsync` k přesměrování aktualizace UI do hlavního vlákna.  

---

### **3. Použití `InvokeAsync`**  

- **Scénář**: Aktualizace stavu komponenty z pozadí (např. po dokončení úlohy ve `Task.Run`).  
- **Metoda**:  
  ```csharp
  private async Task AktualizujData()
  {
      await Task.Run(async () =>
      {
          var výsledek = await DlouháOperace();
          await InvokeAsync(() => 
          {
              // Tento kód se spustí na hlavním vlákně
              data = výsledek;
              StateHasChanged();
          });
      });
  }
  ```

---

### **4. Životní cyklus komponenty a synchronizace**  

- Metody jako `OnInitializedAsync`, `OnParametersSetAsync` již **automaticky pracují v kontextu synchronizace**.  
- **Pozor na**:  
  - Použití `Task.Run` uvnitř životního cyklu bez `InvokeAsync`.  
  - Volání `StateHasChanged` z pozadí → může způsobit výjimku.  

---

### **5. Blazor Server vs WebAssembly**  

- **Blazor Server**:  
  - Kontext synchronizace je vázán na **SignalR spojení** (každý klient má vlastní kontext).  
  - Aktualizace UI se odesílají přes SignalR.  
- **Blazor WebAssembly**:  
  - Kontext synchronizace odpovídá **hlavnímu vláknu prohlížeče**.  
  - Všechny operace UI musí běžet na tomto vlákně.  

---

### **6. Časté chyby a řešení**  

| **Chyba**                                   | **Řešení**                                  |  
|--------------------------------------------|--------------------------------------------|  
| `InvalidOperationException: The current thread is not associated with the Dispatcher.` | Použijte `InvokeAsync` pro aktualizaci UI. |  
| Zaseknutí UI při synchronních operacích     | Používejte `async/await` správně.          |  
| Neočekávané chování při paralelních úlohách | Synchronizujte přístup ke stavu komponenty. |  

---

### **7. Ukázka: Synchronizovaná práce s UI**  

```csharp
@code {
    private List<string> položky = new();
    private bool načítání = false;

    private async Task NačtiDataAsync()
    {
        načítání = true;
        await Task.Delay(2000); // Simulace asynchronní operace

        // Aktualizace UI musí být na hlavním vlákně
        await InvokeAsync(() =>
        {
            položky.Add("Položka 1");
            položky.Add("Položka 2");
            načítání = false;
            StateHasChanged();
        });
    }
}
```

---

### **Shrnutí**  

- **Kontext synchronizace** zajišťuje, že aktualizace UI probíhají na hlavním vlákně.  
- **Klíčové metody**:  
  - `InvokeAsync`: Pro ruční přesměrování kódu do hlavního vlákna.  
  - `StateHasChanged`: Volá se automaticky v kontextu synchronizace.  
- **Doporučení**:  
  - Vždy používejte `await` před asynchronními operacemi.  
  - Pro operace na pozadí kombinujte `Task.Run` s `InvokeAsync`.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Synchronization Context](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/rendering#synchronization-context).  
