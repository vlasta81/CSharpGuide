
## **Kaskádové hodnoty a parametry v ASP.NET Core Blazor komponentách**

---

### **1. Úvod do kaskádových hodnot**  

- **Definice**: Mechanismus pro předávání dat z **nadřazené komponenty** do **potomků** bez nutnosti explicitního předávání přes každou úroveň hierarchie.  
- **Použití**:  
  - Sdílení kontextu (např. téma vzhledu, lokalizace, stav uživatele).  
  - Zjednodušení kódu u hluboce vnořených komponent.  

---

### **2. Základní použití**  

#### **a) Poskytnutí kaskádové hodnoty** 

- Použijte komponentu `<CascadingValue>` v rodičovské komponentě.  
  ```razor  
  <CascadingValue Value="@theme" Name="AppTheme">  
      <ChildComponent />  
  </CascadingValue>  

  @code {  
      private Theme theme = new Theme { Color = "blue" };  
  }  
  ```  
  - **Parametry**:  
    - `Value`: Hodnota k předání.  
    - `Name` (volitelné): Identifikátor pro rozlišení více hodnot stejného typu.  
    - `IsFixed` (výchozí `false`): Pokud `true`, hodnota se nemění → optimalizace výkonu.  

#### **b) Příjem kaskádové hodnoty**  

- Označte vlastnost v potomkovi atributem `[CascadingParameter]`.  
  ```razor  
  @code {  
      [CascadingParameter(Name = "AppTheme")]  
      public Theme Theme { get; set; }  
  }  
  ```  
  - **Pravidla**:  
    - Pokud není `Name` uveden, hodnota se přiřadí podle shody typu.  
    - Pokud je `Name` uveden, musí se shodovat s `Name` v `<CascadingValue>`.  

---

### **3. Příklady použití**  

#### **a) Sdílení tématu**  

```razor  
<!-- Rodičovská komponenta -->  
<CascadingValue Value="@theme">  
    <Layout>  
        <Content />  
    </Layout>  
</CascadingValue>  

@code {  
    private Theme theme = new Theme { PrimaryColor = "#007bff" };  
}  

<!-- Dětská komponenta (Content.razor) -->  
<div style="color: @Theme.PrimaryColor">  
    Text s barvou z tématu.  
</div>  

@code {  
    [CascadingParameter]  
    public Theme Theme { get; set; }  
}  
```  

#### **b) Autentizace uživatele**  

```razor  
<CascadingValue Value="@authState">  
    <MainLayout />  
</CascadingValue>  

@code {  
    private AuthenticationState authState;  

    protected override async Task OnInitializedAsync()  
    {  
        authState = await AuthService.GetAuthenticationStateAsync();  
    }  
}  
```  

---

### **4. Pokročilé techniky**  

#### **a) Více kaskádových hodnot**  

- Lze kombinovat více `<CascadingValue>` s různými `Name` nebo typy.  
  ```razor  
  <CascadingValue Value="@theme" Name="AppTheme">  
      <CascadingValue Value="@user">  
          <ChildComponent />  
      </CascadingValue>  
  </CascadingValue>  
  ```  

#### **b) Implicitní vs. explicitní přiřazení**  

- **Implicitní**: Shoda podle typu (bez `Name`).  
- **Explicitní**: Shoda podle `Name`.  

---

### **5. Omezení a doporučení**  

- **Výkon**:  
  - Pokud hodnota nemění (`IsFixed="true"`), zabrání se zbytečnému překreslování potomků.  
- **Srozumitelnost**:  
  - Používejte kaskádové parametry pouze pro **globální kontext**, ne pro běžnou komunikaci mezi komponentami.  
- **Konflikty**:  
  - Při více hodnotách stejného typu použijte `Name` k rozlišení.  

---

### **6. Srovnání s běžnými parametry**  

| **Parametr**          | **Běžný parametr**                  | **Kaskádový parametr**               |  
|-----------------------|--------------------------------------|---------------------------------------|  
| **Předávání**         | Explicitní (rodič → dítě)           | Implicitní (kaskádováno hierarchií)  |  
| **Flexibilita**       | Vhodné pro přímé vztagy             | Vhodné pro sdílení napříč více úrovněmi |  
| **Výkon**             | Žádné režijní náklady               | Může způsobit častější překreslování |  

---

### **7. Časté chyby**  

| **Chyba**                          | **Řešení**                                  |  
|------------------------------------|---------------------------------------------|  
| Hodnota není dostupná v potomkovi  | Zkontrolujte, zda je `<CascadingValue>` správně vnořen. |  
| Konflikt typů/name                | Použijte `Name` pro explicitní přiřazení.    |  
| Neočekávané překreslování         | Nastavte `IsFixed="true"`, pokud se hodnota nemění. |  

---

### **Shrnutí**  

- **Kaskádové hodnoty** zjednodušují sdílení dat v komplexních hierarchiích komponent.  
- **Klíčové komponenty**:  
  - `<CascadingValue>` pro poskytnutí hodnoty.  
  - `[CascadingParameter]` pro její příjem.  
- **Používejte pro**: Témata, lokalizaci, stav uživatele, nebo konfiguraci.  

**Užitečné zdroje**:  
- Oficiální dokumentace: [Blazor Cascading Values](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/cascading-values).  
- Ukázky: [Blazor Cascading Parameters Samples](https://github.com/dotnet/blazor-samples).
