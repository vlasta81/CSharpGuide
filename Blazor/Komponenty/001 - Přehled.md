
## **Komponenty v ASP.NET Core Blazor – Přehled**

---

### **1. Úvod do komponent**  

- **Definice**: Komponenty jsou základní stavební jednotky Blazor aplikací. Jedná se o samostatné, znovupoužitelné části UI (např. tlačítko, formulář, stránka).  
- **Formát**: Každá komponenta je soubor s příponou `.razor`, který kombinuje HTML markup a C# kód.  
- **Výhody**:  
  - **Znovupoužitelnost**: Jednou vytvořenou komponentu lze použít na více místech.  
  - **Izolace logiky**: Každá komponenta má vlastní stav a metody.  
  - **Hierarchie**: Komponenty lze vnořovat do sebe (např. hlavní layout → navigace → tlačítko).  

---

### **2. Struktura komponenty**  

#### **a) Základní prvky**  

- **@page directive**: Definuje URL cestu pro navigaci (např. `@page "/users"`).  
- **HTML markup**: UI definované pomocí HTML nebo Razor syntaxe.  
- **@code blok**: Obsahuje C# logiku (metody, proměnné, životní cyklus).  

**Příklad jednoduché komponenty (Counter.razor)**:  
```razor
@page "/counter"

<h1>Počítadlo: @currentCount</h1>
<button @onclick="Increment">Přidat</button>

@code {
    private int currentCount = 0;

    private void Increment()
    {
        currentCount++;
    }
}
```

#### **b) Parametry (vstupní vlastnosti)**  

- **Definice**: Umožňují předávat data z rodičovské komponenty do dětské.  
- **Syntaxe**: Označeno atributem `[Parameter]`.  

**Příklad (ChildComponent.razor)**:  
```razor
<h3>@Nadpis</h3>

@code {
    [Parameter]
    public string Nadpis { get; set; } = "Výchozí nadpis";
}
```

**Použití v rodičovské komponentě**:  
```razor
<ChildComponent Nadpis="Aktuality" />
```

---

### **3. Životní cyklus komponenty**  

Metody, které se volají v různých fázích existence komponenty:  
1. **OnInitialized()**: Volá se při inicializaci komponenty (vhodné pro načítání dat).  
2. **OnParametersSet()**: Spustí se po změně parametrů.  
3. **OnAfterRender(bool firstRender)**: Volá se po vykreslení UI (první vykreslení má `firstRender = true`).  
4. **Dispose()**: Pro uvolnění prostředků (např. zrušení timeru).  

**Příklad**:  
```csharp
@code {
    protected override void OnInitialized()
    {
        Console.WriteLine("Komponenta byla inicializována.");
    }
}
```

---

### **4. Komunikace mezi komponentami**  

- **Rodič → dítě**: Pomocí parametrů (viz výše).  
- **Dítě → rodič**: Pomocí **EventCallback**.  
  ```razor
  <!-- Dětská komponenta -->
  <button @onclick="() => OnClick.InvokeAsync()">Klikni</button>

  @code {
      [Parameter]
      public EventCallback OnClick { get; set; }
  }

  <!-- Rodičovská komponenta -->
  <ChildComponent OnClick="HandleClick" />

  @code {
      private void HandleClick()
      {
          Console.WriteLine("Tlačítko bylo stisknuto.");
      }
  }
  ```  
- **Cascading hodnoty**: Předání hodnoty hluboko do hierarchie bez explicitního předávání parametrů.  
  ```csharp
  <CascadingValue Value="@theme">
      <ChildComponent />
  </CascadingValue>
  ```

---

### **5. Události a datová vazba**  

- **Události**: Reakce na akce uživatele (kliknutí, změna inputu).  
  ```razor
  <input @oninput="HandleInput" />
  @code {
      private void HandleInput(ChangeEventArgs e)
      {
          Console.WriteLine(e.Value.ToString());
      }
  }
  ```  
- **Datová vazba**: Synchronizace UI a proměnných pomocí `@bind`.  
  ```razor
  <input @bind="text" />
  @code {
      private string text = "";
  }
  ```

---

### **6. Znovupoužitelné komponenty a knihovny**  

- **Shared složka**: Komponenty v `Shared` jsou globálně dostupné.  
- **Vlastní knihovny**: Komponenty lze balit do Razor Class Library (RCL) pro sdílení mezi projekty.  

---

### **7. Pokročilé techniky**  

- **Dynamické komponenty**: Vykreslení komponenty za běhu pomocí `RenderFragment`.  
- **Komponenty s generiky**:  
  ```razor
  @typeparam TItem
  <ul>
      @foreach (var item in Items)
      {
          <li>@item</li>
      }
  </ul>
  @code {
      [Parameter]
      public List<TItem> Items { get; set; }
  }
  ```

---

### **8. Omezení a doporučení**  

- **Výkon**: Příliš vnořené komponenty mohou zpomalit rendering.  
- **Stavová správa**: Pro komplexní aplikace použijte stavové kontejnery (např. Fluxor) nebo Blazor Server.  
- **Testování**: Komponenty lze testovat pomocí nástrojů jako **bUnit**.  

---

### **Shrnutí**  

| **Aspekt**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Struktura**            | HTML + C# v `.razor` souborech s podporou parametrů a životního cyklu.    |  
| **Komunikace**           | Parametry (rodič → dítě), EventCallback (dítě → rodič), Cascading hodnoty.|  
| **Vazby a události**     | `@bind` pro synchronizaci dat, `@onclick`/`@oninput` pro akce.            |  
| **Znovupoužitelnost**    | Shared složka, Razor Class Library.                                       |  

**Klíčové výhody**:  
- Jednotný jazyk (C#) pro logiku i UI.  
- Modularita a snadná údržba díky komponentové architektuře.  

**Užitečné zdroje**:  
- Oficiální dokumentace: [Blazor Components](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/).  
