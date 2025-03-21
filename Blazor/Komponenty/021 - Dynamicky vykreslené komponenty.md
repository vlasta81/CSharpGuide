
## **Dynamicky vykreslené komponenty v ASP.NET Core Blazor**

---

### **1. Úvod do dynamického vykreslování**  

Dynamické vykreslování komponent umožňuje **generovat UI za běhu** na základě dat, podmínek nebo uživatelských akcí. Tento přístup je užitečný pro aplikace, kde se struktura UI mění dynamicky (např. formuláře, dashboardy, pluginy).

---

### **2. Metody dynamického vykreslování**  

#### **a) Podmíněné vykreslení (Conditional Rendering)** 

- Použití `@if`, `@else`, `@switch` pro zobrazení komponenty na základě stavu.  
```razor  
@if (showComponent)  
{  
    <MyComponent />  
}  
else  
{  
    <p>Komponenta není viditelná.</p>  
}  
```

#### **b) RenderFragment**  

- Delegát `RenderFragment` umožňuje definovat obsah dynamicky.  
```razor  
<DynamicContent>@dynamicFragment</DynamicContent>  

@code {  
    private RenderFragment dynamicFragment = builder =>  
    {  
        builder.OpenComponent(0, typeof(MyComponent));  
        builder.CloseComponent();  
    };  
}  
```

#### **c) Komponenta `DynamicComponent` (Blazor 6.0+)**  

- Vykreslí komponentu na základě jejího typu (užitečné pro neznámé komponenty za běhu).  
```razor  
<DynamicComponent Type="@selectedComponentType" Parameters="@parameters" />  

@code {  
    private Type selectedComponentType = typeof(LoginForm);  
    private Dictionary<string, object> parameters = new()  
    {  
        { "Username", "user" }  
    };  
}  
```

---

### **3. Předávání parametrů dynamickým komponentám** 

- **Parametry přes slovník**: Použití `ParameterView` nebo kolekce `Dictionary<string, object>`.  
```csharp  
var parameters = new Dictionary<string, object>  
{  
    { "Text", "Ahoj!" },  
    { "OnClick", EventCallback.Factory.Create(this, HandleClick) }  
};  
```

---

### **4. Správa událostí a životního cyklu**  

- **EventCallback**: Propojení událostí dynamické komponenty s metodami rodiče.  
```razor  
<DynamicComponent Type="@selectedComponentType"  
                  Parameters="@(new { OnSubmit = EventCallback.Factory.Create(this, HandleSubmit) })" />  
```

- **Životní cyklus**: Dynamické komponenty procházejí standardním životním cyklem (`OnInitialized`, `OnParametersSet`).  
- **Uvolnění prostředků**: Implementace `IDisposable` pro dynamicky vytvořené komponenty.

---

### **5. Zachování stavu pomocí `@key`** 

- Zabránění neočekávanému resetování stavu při změně komponenty.  
```razor  
@foreach (var item in items)  
{  
    <DynamicComponent @key="item.Id" Type="item.ComponentType" />  
}  
```

---

### **6. Výkon a optimalizace** 

- **Virtualizace**: Pro velké seznamy dynamických komponent použijte `<Virtualize>`.  
- **Minimalizace překreslování**: Použití `ShouldRender()` pro kontrolu nutnosti aktualizace.  
- **Omezení častých změn**: Např. debounce pro uživatelské vstupy ovlivňující dynamické vykreslování.

---

### **7. Příklady použití**  

- **Formulářový generátor**: Dynamické vytváření políček na základě datového modelu.  
- **Modulární dashboard**: Uživatel přidává/odebírá widgety.  
- **Pluginový systém**: Načítání komponent z externích knihoven.  

**Příklad kódu (Formulářový generátr)**:  
```razor  
@foreach (var field in formFields)  
{  
    <DynamicComponent Type="@field.ComponentType" Parameters="@field.Parameters" />  
}  
```

---

### **8. Bezpečnostní aspekty**  

- **Validace vstupů**: Zajistěte, že dynamicky vykreslované komponenty pocházejí z důvěryhodných zdrojů.  
- **Omezení přístupu**: Používejte autorizaci k zabránění vykreslení neoprávněných komponent.

---

### **9. Omezení** 

- **Reflexe a typy**: Dynamické načítání komponent může vyžadovat práci s reflexí, což komplikuje optimalizaci AOT (Ahead-Of-Time kompilace).  
- **Složitost ladění**: Dynamické komponenty ztěžují sledování toku aplikace.

---

### **Shrnutí**  

| **Funkce**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Podmíněné vykreslení** | `@if`, `@switch` pro jednoduché případy.                                  |  
| **`DynamicComponent`**   | Vykreslení komponenty na základě typu.                                    |  
| **Parametry**             | Předávání přes slovník nebo anonymní objekty.                             |  
| **Výkon**                | Optimalizace pomocí `@key`, `ShouldRender`, virtualizace.                |  

**Klíčové výhody**:  
- Flexibilita UI bez nutnosti předem znát všechny komponenty.  
- Ideální pro aplikace s modulární architekturou.  

**Užitečné zdroje**:  
- Dokumentace: [Dynamické komponenty v Blazor](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/dynamiccomponent).  
