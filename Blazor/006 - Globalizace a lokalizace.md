
## **Globalizace a lokalizace v ASP.NET Core Blazor**

---

### **1. Úvod**  

- **Globalizace (G11N)**: Příprava aplikace pro podporu různých jazyků a regionálních nastavení (formáty data, čísla, měny).  
- **Lokalizace (L10N)**: Překlad UI do konkrétních jazyků a kultur (texty, obrázky).  
- **Cíl**: Aplikace dostupná v mnoha jazycích s respektováním kulturních konvencí.

---

### **2. Základní koncepty**  

- **Kultura (`CultureInfo`)**: Určuje jazyk a regionální nastavení (např. `cs-CZ`, `en-US`).  
- **Zdrojové soubory (`.resx`)**: Ukládají lokalizované texty (např. `Resources.resx`, `Resources.fr.resx`).  
- **Poskytovatelé kultur**: Jak aplikace detekuje požadovanou kulturu (URL, cookie, jazyk prohlížeče).

---

### **3. Nastavení lokalizace v Blazoru**  

#### **a) Registrace služeb**  

V `Program.cs`:  
```csharp  
builder.Services.AddLocalization(); // Přidá podporu lokalizace  
builder.Services.Configure<RequestLocalizationOptions>(options =>  
{  
    options.SetDefaultCulture("cs-CZ");  
    options.AddSupportedCultures("cs-CZ", "en-US");  
    options.AddSupportedUICultures("cs-CZ", "en-US");  
});  
```  

#### **b) Konfigurace middleware (Blazor Server)**  

```csharp  
app.UseRequestLocalization(); // Aktivuje automatickou detekci kultury  
```  

#### **c) Lokalizace v Blazor WebAssembly**  

- Detekce kultury přes JavaScript Interop (jazyk prohlížeče):  
  ```csharp  
  var browserCulture = await JSRuntime.InvokeAsync<string>("getBrowserCulture");  
  CultureInfo.DefaultThreadCurrentCulture = new CultureInfo(browserCulture);  
  ```  

---

### **4. Vytváření zdrojových souborů**  

- **Struktura**:  
  - `Resources.resx`: Výchozí texty (např. angličtina).  
  - `Resources.cs.resx`: České překlady.  
  - Umístění: Složka `Resources` ve sdíleném projektu.  
- **Přístup v kódu**:  
  ```csharp  
  @inject IStringLocalizer<MyComponent> Localizer  
  <h1>@Localizer["WelcomeMessage"]</h1>  
  ```  

---

### **5. Formátování kulturních dat**  

- **Čísla, data, měny**:  
  ```csharp  
  var date = DateTime.Now.ToString("d", CultureInfo.CurrentCulture); // 12. 3. 2024  
  var number = 1234.56.ToString("C", CultureInfo.CurrentCulture); // 1 234,56 Kč  
  ```  
- **Integrace do komponent**:  
  ```razor  
  <p>@DateTime.Now.ToLocalizedDateString()</p> <!-- Vlastní metoda -->  
  ```  

---

### **6. Lokalizované routování**  

- **URL s parametrem kultury**:  
  ```razor  
  @page "/{culture}/produkty"  
  [Parameter] public string? Culture { get; set; }  
  ```  
- **Middleware pro nastavení kultury z URL**:  
  ```csharp  
  app.Use(async (context, next) =>  
  {  
      var culture = context.Request.RouteValues["culture"]?.ToString() ?? "cs-CZ";  
      CultureInfo.CurrentCulture = new CultureInfo(culture);  
      await next();  
  });  
  ```  

---

### **7. Lokalizace validačních zpráv**  

- **DataAnnotations**:  
  ```csharp  
  public class UserModel  
  {  
      [Required(ErrorMessage = "RequiredField")]  
      public string Name { get; set; }  
  }  
  ```  
- **Lokalizované chybové hlášky**:  
  V `Resources.resx`: `RequiredField` → "Toto pole je povinné."  

---

### **8. Testování a přepínání jazyků** 

- **Dropdown pro výběr jazyka**:  
  ```razor  
  <select @bind="Culture">  
      <option value="cs-CZ">Čeština</option>  
      <option value="en-US">English</option>  
  </select>  
  ```  
- **Uložení volby**: Do cookie nebo `localStorage` pomocí JS Interop.  

---

### **9. Omezení a doporučení**  

- **Blazor WebAssembly**:  
  - Nutnost načítat všechny jazykové verze při startu (zvýšení velikosti stahovaných souborů).  
  - Řešení: Dynamické načítání překladů za běhu (lazy loading).  
- **Konsistence**: Aktualizujte překlady při změnách UI.  
- **Nástroje**: Používejte nástroje jako `ResXManager` pro správu zdrojových souborů.  

---

### **Shrnutí**  

| **Komponenta**           | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Zdrojové soubory**     | `.resx` soubory pro každý jazyk v složce `Resources`.                     |  
| **IStringLocalizer**     | Služba pro přístup k lokalizovaným textům v komponentách.                 |  
| **Formátování dat**      | Respektování `CultureInfo` pro čísla, datum a měnu.                       |  
| **Routování**            | Podpora parametrů kultury v URL pro SEO a bookmarking.                    |  

**Užitečné zdroje**:  
- [Dokumentace: Globalizace a lokalizace v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/fundamentals/localization)  
