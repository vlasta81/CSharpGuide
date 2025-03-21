
## **Splatting atributů a doplňkové parametry v ASP.NET Core Blazor komponentách**  

---

### **1. Splatting atributů**  

**Definice**: Splatting („rozprostření“ atributů) umožňuje předat **kolekci atributů** komponentě najednou, aniž byste museli každý atribut definovat explicitně. Používá se pro zjednodušení práce s dynamickými nebo neznámými atributy.  

#### **Jak funguje?**  

- Atributy se předávají pomocí slovníku (`Dictionary<string, object>`).  
- V komponentě se použije direktiva `@attributes` k aplikování všech atributů na cílový HTML prvek nebo komponentu.  

#### **Příklad použití**:  

```razor  
<!-- Rodičovská komponenta -->  
<MujButton Trida="btn-primary" DataId="123" @attributes="DalsiAtributy" />  

@code {  
    private Dictionary<string, object> DalsiAtributy = new()  
    {  
        { "disabled", true },  
        { "aria-label", "Potvrdit" }  
    };  
}  

<!-- Dětská komponenta (MujButton.razor) -->  
<button class="@Trida" @attributes="Attributes">  
    @ChildContent  
</button>  

@code {  
    [Parameter]  
    public string Trida { get; set; } = "";  

    [Parameter]  
    public RenderFragment ChildContent { get; set; }  

    // Slovník pro zachycení všech nerozpoznaných atributů  
    [Parameter(CaptureUnmatchedValues = true)]  
    public Dictionary<string, object> Attributes { get; set; } = new();  
}  
```  

#### **Výhody**:  

- **Flexibilita**: Komponenta přijímá libovolné atributy (např. `data-*`, `aria-*`).  
- **Redukce kódu**: Není nutné definovat každý atribut jako samostatný parametr.  

---

### **2. Doplňkové parametry (CaptureUnmatchedValues)**  

**Definice**: Doplňkové parametry jsou atributy, které komponenta **nezachytila explicitně**. Lze je zachytit pomocí vlastnosti označené `[Parameter(CaptureUnmatchedValues = true)]`.  

#### **Kdy použít?**  

- Když komponenta **obaluje HTML prvek nebo jinou komponentu** a potřebuje předat neznámé atributy.  
- Pro práci s **dynamickými atributy** (např. při integraci s JavaScriptem).  

#### **Příklad**:  

```razor  
<!-- Komponenta InputWrapper.razor -->  
<input @bind-value="@Value" @attributes="Attributes" />  

@code {  
    [Parameter]  
    public string Value { get; set; } = "";  

    [Parameter(CaptureUnmatchedValues = true)]  
    public Dictionary<string, object> Attributes { get; set; } = new();  
}  
```  
- Všechny atributy jako `placeholder`, `maxlength`, nebo `data-*` budou automaticky předány do `<input>`.  

---

### **3. Omezení a doporučení**  

#### **a) Kolize názvů**  

- Pokud atribut ve slovníku má stejný název jako explicitní parametr, **vyhraje explicitní parametr**.  
- **Příklad**:  
  ```razor  
  <MujInput Trida="custom-class" @attributes="Attributes" />  
  // Pokud Attributes obsahuje "Trida", použije se "custom-class".  
  ```  

#### **b) Typy hodnot**  

- Atributy musí mít hodnoty kompatibilní s cílovým prvkem (např. `disabled` očekává `bool`, ne `string`).  

#### **c) Bezpečnost**  

- Nepředávejte nespravované atributy (např. `onclick`), pokud komponenta neslouží k obalení HTML prvku.  

---

### **4. Srovnání splattingu a explicitních parametrů**  

| **Parametr**          | **Explicitní parametr**                  | **Splatting**                          |  
|-----------------------|------------------------------------------|----------------------------------------|  
| **Flexibilita**       | Pevně definované atributy                | Libovolné atributy                     |  
| **Použití**           | Časté atributy (např. `Class`, `Style`)  | Dynamické/neznámé atributy             |  
| **Syntaxe**           | `<Komponenta Trida="..." />`             | `<Komponenta @attributes="slovnik" />` |  

---

### **5. Časté chyby**  

- **Zapomenutí `CaptureUnmatchedValues`**: Atributy se nezachytí a ignorují se.  
- **Nekonzistentní typy**: Předání `string` místo `bool` pro atribut `disabled`.  

---

### **Shrnutí**  

- **Splatting atributů** (`@attributes`) slouží k hromadnému předání atributů pomocí slovníku.  
- **Doplňkové parametry** (`[Parameter(CaptureUnmatchedValues = true)]`) zachytí všechny nerozpoznané atributy.  
- **Používejte pro**:  
  - Komponenty obalující HTML prvky (např. vlastní inputy).  
  - Integraci s JavaScriptem (atributy `data-*`, `aria-*`).  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Attribute Splatting](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/#attribute-splatting).  
- Ukázky: [Blazor Component Samples](https://github.com/dotnet/blazor-samples).
