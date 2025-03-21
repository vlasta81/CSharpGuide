
## **Podpora obecných typů (generics) v komponentách ASP.NET Core Blazor**  

V Blazor lze vytvářet **komponenty s obecnými typy** (generics), které umožňují pracovat s různými datovými typy bez nutnosti opakovaného psaní kódu. Tento přístup zvyšuje flexibilitu a znovupoužitelnost komponent.

---

### **1. Definice obecné komponenty**  

- **Direktiva `@typeparam`**: Slouží k deklaraci obecného typového parametru.  
- **Příklad**:  
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
      public List<TItem> Items { get; set; } = new();
  }
  ```

---

### **2. Použití obecné komponenty**  

- **Explicitní určení typu**:  
  ```razor
  <GenericList TItem="string" Items="@stringList" />
  ```  
- **Odvození typu** (v .NET 6+):  
  ```razor
  <GenericList Items="@intList" /> <!-- Typ TItem se odvodí jako int -->
  ```

---

### **3. Restrikce typových parametrů**  

- **Omezení pomocí `where`**:  
  ```razor
  @typeparam TItem where TItem : IComparable

  @code {
      [Parameter]
      public TItem Value { get; set; }
  }
  ```  
- **Podporovaná omezení**:  
  - `class`, `struct`, `new()`, rozhraní, konkrétní třídy.  

---

### **4. Data binding s obecnými komponentami**  

- **Vazba na obecné hodnoty**:  
  ```razor
  @typeparam TValue

  <input @bind="Value" />

  @code {
      [Parameter]
      public TValue Value { get; set; }

      [Parameter]
      public EventCallback<TValue> ValueChanged { get; set; }
  }
  ```  
- **Použití**:  
  ```razor
  <GenericInput TValue="int" @bind-Value="@number" />
  ```

---

### **5. Životní cyklus a DI v obecných komponentách**  

- **Metody životního cyklu** (např. `OnInitializedAsync`) fungují stejně jako u neobecných komponent.  
- **Dependency Injection**:  
  ```razor
  @typeparam TItem
  @inject ILogger<GenericComponent<TItem>> Logger

  @code {
      protected override void OnInitialized()
      {
          Logger.LogInformation($"Typ komponenty: {typeof(TItem).Name}");
      }
  }
  ```

---

### **6. Časté use cases**  

- **Generic seznamy/tabulky**: Zobrazení dat různého typu (např. `Grid<T>`, `ListView<T>`).  
- **Formuláře**: Univerzální komponenty pro editaci různých modelů (např. `Form<Employee>`, `Form<Product>`).  
- **Filtrování a řazení**: Komponenty pracující s `IQueryable<T>`.  

---

### **7. Výhody a nevýhody**  

| **Výhody**                          | **Nevýhody**                          |  
|-------------------------------------|----------------------------------------|  
| Snížení duplicity kódu              | Složitější syntaxe pro začátečníky     |  
| Silná typová kontrola                | Omezená podpora v některých scénářích  |  
| Univerzálnost pro různé datové typy | Nutnost řešit restrikce typů           |  

---

### **8. Ukázka: Generic tabulka**  

```razor
@typeparam TItem

<table class="table">
    <thead>
        <tr>@TableHeader</tr>
    </thead>
    <tbody>
        @foreach (var item in Items)
        {
            <tr>@TableRow(item)</tr>
        }
    </tbody>
</table>

@code {
    [Parameter]
    public List<TItem> Items { get; set; } = new();

    [Parameter]
    public RenderFragment TableHeader { get; set; }

    [Parameter]
    public RenderFragment<TItem> TableRow { get; set; }
}
```

**Použití**:  
```razor
<GenericTable TItem="User" Items="@users">
    <TableHeader>
        <th>Jméno</th>
        <th>Email</th>
    </TableHeader>
    <TableRow Context="user">
        <td>@user.Name</td>
        <td>@user.Email</td>
    </TableRow>
</GenericTable>
```

---

### **Shrnutí**  

- **Obecné komponenty** umožňují vytvářet univerzální a typově bezpečné UI prvky.  
- **Klíčové prvky**:  
  - `@typeparam` pro deklaraci typového parametru.  
  - Restrikce pomocí `where`.  
  - Podpora datové vazby a lifecycle metod.  
- **Používejte je pro**:  
  - Sdílené komponenty napříč různými datovými modely.  
  - Snížení redundance kódu.  

**Užitečné zdroje**:  
- Dokumentace: [Generic Components in Blazor](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/generic-type-support).  
