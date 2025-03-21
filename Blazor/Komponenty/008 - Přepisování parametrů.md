
## **Přepisování parametrů v komponentách ASP.NET Core Blazor**  

V Blazor komponentách jsou parametry základním mechanismem pro předávání dat z rodičovské do dětské komponenty. Přepisování parametrů znamená **změnu hodnoty parametru uvnitř dětské komponenty**, což vyžaduje specifický přístup kvůli jednosměrnému toku dat. Zde je přehled klíčových principů a postupů:

---

### **1. Základní princip parametrů**  

- **Definice parametru**: Vlastnost označená atributem `[Parameter]`.  
  ```csharp
  [Parameter]
  public string Text { get; set; }
  ```  
- **Jednosměrný tok dat**: Hodnota parametru je předána z rodiče do dítěte. Dětská komponenta by **neměla přímo měnit hodnotu parametru** – to může vést k nekonzistentnímu stavu.  

---

### **2. Kdy a jak přepisovat parametry?**  

- **Scénář**: Dětská komponenta potřebuje upravit hodnotu parametru pro interní použití (např. validace, transformace dat).  
- **Řešení**:  
  - Použijte **lokální proměnnou**, která inicializuje hodnotu z parametru.  
  - Aktualizujte lokální proměnnou při změně parametru (pomocí životního cyklu).  

---

### **3. Životní cyklus pro přepis parametrů**  

#### **a) Metoda `OnParametersSet`**  

- Spouští se při změně parametrů z rodiče nebo při inicializaci.  
- **Příklad**:  
  ```csharp
  private string _internalText;

  protected override void OnParametersSet()
  {
      // Přepsání hodnoty (např. oříznutí textu)
      _internalText = Text.Trim();
  }
  ```  

#### **b) Metoda `SetParametersAsync`**  

- Umožňuje ruční kontrolu parametrů před jejich aplikací.  
  ```csharp
  public override async Task SetParametersAsync(ParameterView parameters)
  {
      // Přečtení parametrů a úprava před aplikací
      if (parameters.TryGetValue(nameof(Text), out string text))
      {
          await base.SetParametersAsync(ParameterView.Empty);
          Text = text.ToUpper(); // Úprava hodnoty
          await base.SetParametersAsync(ParameterView.FromSetParameters(this));
      }
  }
  ```  

---

### **4. Dvoucestná vazba (two-way binding)**  

- Pokud potřebujete, aby změna v dítěti aktualizovala rodiče, použijte **EventCallback**.  
- **Příklad**:  
  ```razor
  <!-- Rodičovská komponenta -->
  <ChildComponent @bind-Text="parentText" />

  <!-- Dětská komponenta -->
  <input @value="Text" @oninput="HandleInput" />

  @code {
      [Parameter]
      public string Text { get; set; }

      [Parameter]
      public EventCallback<string> TextChanged { get; set; }

      private async Task HandleInput(ChangeEventArgs e)
      {
          string newValue = e.Value.ToString().ToUpper();
          await TextChanged.InvokeAsync(newValue); // Aktualizace rodiče
      }
  }
  ```  

---

### **5. Přepisování kaskádových parametrů**  

- Kaskádové parametry (`[CascadingParameter]`) lze přepsat v dětské komponentě jejich **re-deklarací**.  
- **Příklad**:  
  ```razor
  <!-- Rodičovská komponenta -->
  <CascadingValue Value="@theme">
      <ChildComponent />
  </CascadingValue>

  <!-- Dětská komponenta -->
  <CascadingValue Value="@newTheme">
      <GrandchildComponent />
  </CascadingValue>

  @code {
      [CascadingParameter]
      public Theme theme { get; set; }

      private Theme newTheme = new Theme { Color = "red" }; // Přepsání hodnoty
  }
  ```  

---

### **6. Omezení a doporučení**  

- **Nepřepisujte parametry přímo**: Vždy používejte lokální proměnné nebo `EventCallback`.  
- **Pozor na nekonečné smyčky**: Změna parametru v dítěti může spustit přerenderování rodiče a zacyklení.  
- **Používejte `StateHasChanged()`**: Pokud ručně měníte stav, vyvolávejte aktualizaci UI.  

---

### **7. Ukázka: Validace parametru** 

```razor
@code {
    [Parameter]
    public int MaxValue { get; set; } = 100;

    private int _currentValue;

    protected override void OnParametersSet()
    {
        // Omezení hodnoty na MaxValue
        _currentValue = Math.Min(_currentValue, MaxValue);
    }

    private void Increment()
    {
        _currentValue++;
        _currentValue = Math.Min(_currentValue, MaxValue);
    }
}
```  

---

### **Shrnutí**  

| **Situace**               | **Postup**                                                                 |  
|---------------------------|---------------------------------------------------------------------------|  
| **Interní úprava hodnoty** | Použijte lokální proměnnou a `OnParametersSet`.                           |  
| **Aktualizace rodiče**    | Využijte dvoucestnou vazbu s `EventCallback<T>`.                          |  
| **Kaskádové parametry**   | Re-deklarujte parametr v dítěti s novou hodnotou.                         |  

**Klíčové body**:  
- Parametry jsou určeny pro **jednosměrný tok dat** z rodiče do dítěte.  
- Přepisování parametrů vyžaduje správné použití životního cyklu a lokálních proměnných.  
- Pro obousměrnou komunikaci použijte `@bind-Property` s `EventCallback`.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Component Parameters](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/data-binding).  
- Článek: [Blazor Two-Way Binding Deep Dive](https://code-maze.com/blazor-two-way-binding/).
