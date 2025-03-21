
## **Využívání komponent ASP.NET Core Razor z Razor knihovny tříd (RCL) v Blazor**

---

### **1. Vytvoření Razor knihovny tříd (RCL)**  

- **Účel**: Sdílení komponent, stylů, skriptů a dalších prostředků mezi projekty.  
- **Postup**:  
  ```bash  
  dotnet new razorclasslib -n MojeKnihovna  
  ```  
- **Struktura**:  
  - Komponenty (`.razor`), CSS/JS soubory, statické prostředky v `wwwroot`.  
  - Podpora pro **Blazor Server** i **WebAssembly**.  

---

### **2. Využití komponenty z RCL v Blazor aplikaci**  

- **Přidání reference**:  
  ```xml  
  <!-- V .csproj Blazor projektu -->  
  <ItemGroup>  
    <ProjectReference Include="..\MojeKnihovna\MojeKnihovna.csproj" />  
  </ItemGroup>  
  ```  
- **Použití komponenty**:  
  ```razor  
  @using MojeKnihovna.Components  
  <MojeKomponenta />  
  ```  

---

### **3. Zpřístupnění směrovatelných komponent v RCL**  

- **Definice routy v komponentě**:  
  ```razor  
  @page "/moje-cesta"  
  <h3>Komponenta z RCL</h3>  
  ```  
- **Přidání routy v hostitelské aplikaci**:  
  ```csharp  
  // V Program.cs  
  builder.Services.AddRazorPages();  
  ```  

---

### **4. Statické prostředky v `wwwroot` složce RCL**  

- **Uložení souborů**:  
  - CSS, obrázky, JS soubory v `wwwroot` složce RCL.  
- **Přístup v hostitelské aplikaci**:  
  ```html  
  <link href="_content/MojeKnihovna/styles.css" rel="stylesheet" />  
  <script src="_content/MojeKnihovna/scripts.js"></script>  
  ```  

---

### **5. Kolokace JavaScriptových souborů s komponentami**  

- **Izolace JS kódu**:  
  - Každá komponenta může mít přidružený JS soubor (např. `Komponenta.razor.js`).  
  - Použití **JavaScriptových modulů** (ES6):  
    ```javascript  
    // Komponenta.razor.js  
    export function showAlert(message) {  
        alert(message);  
    }  
    ```  
  ```csharp  
  // V komponentě  
  private IJSObjectReference? module;  
  protected override async Task OnAfterRenderAsync(bool firstRender) {  
      module = await JSRuntime.InvokeAsync<IJSObjectReference>("import", "./_content/MojeKnihovna/Komponenta.razor.js");  
      await module.InvokeVoidAsync("showAlert", "Ahoj!");  
  }  
  ```  

---

### **6. Analyzátor kompatibility prohlížeče**  

- **Nástroj**: `Microsoft.AspNetCore.Compatibility.Analyzers` detekuje nekompatibilní kód pro Blazor WebAssembly.  
- **Konfigurace**: Automaticky kontroluje použití API nepodporovaných v prohlížeči.  

---

### **7. Izolace JavaScriptu v modulech**  

- **Výhody**:  
  - Zamezení konfliktů globálních proměnných.  
  - Explicitní import funkcí.  
- **Příklad**:  
  ```javascript  
  // module.js  
  export function init() { /* ... */ }  
  ```  

---

### **8. Zabránění oříznutí metod .NET volaných z JavaScriptu**  

- **Problém**: Linker při publikování odstraní nevyužívané metody.  
- **Řešení**:  
  - Označte metodu atributem `[JSInvokable]`:  
    ```csharp  
    [JSInvokable]  
    public static string GetData() => "Data";  
    ```  
  - Konfigurace linkeru v `.csproj`:  
    ```xml  
    <PropertyGroup>  
      <BlazorWebAssemblyPreserveCollation>true</BlazorWebAssemblyPreserveCollation>  
    </PropertyGroup>  
    ```  

---

### **9. Sestavení, balení a publikace do NuGetu**  

- **Balení RCL**:  
  ```bash  
  dotnet pack MojeKnihovna.csproj -c Release  
  ```  
- **Metadata v `.csproj`**:  
  ```xml  
  <PackageId>MojeKnihovna</PackageId>  
  <Version>1.0.0</Version>  
  <Authors>VaseJmeno</Authors>  
  <Description>Popis knihovny</Description>  
  ```  
- **Odeslání na NuGet.org**:  
  ```bash  
  dotnet nuget push MojeKnihovna.1.0.0.nupkg -k API_KLÍČ -s https://api.nuget.org/v3/index.json  
  ```  

---

### **10. Ochranné známky a licence**  

- **Právní aspekty**:  
  - Zkontrolujte, zda název balíčku není chráněn ochrannou známkou.  
  - Přidejte licenční soubor (`LICENSE`) a informace o autorských právech.  

---

### **Shrnutí**  

| **Funkce**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **RCL**                  | Sdílení komponent a prostředků mezi projekty.                             |  
| **Statické soubory**     | Přístup přes `_content/{NázevKnihovny}`.                                  |  
| **JavaScript moduly**    | Izolace kódu pro lepší správu a kompatibilitu.                            |  
| **NuGet balení**         | Vytvoření a distribuce knihovny jako balíčku.                             |  

**Doporučení**:  
- Testujte komponenty v RCL v reálných scénářích.  
- Používejte analýzu kompatibility prohlížeče pro WebAssembly.  
- Dokumentujte veřejné API knihovny.  

**Užitečné zdroje**:  
- [Dokumentace Microsoft: Razor Class Libraries](https://learn.microsoft.com/cs-cz/aspnet/core/razor-pages/ui-class)  
