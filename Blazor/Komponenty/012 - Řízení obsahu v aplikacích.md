
## **Řízení `<head>` obsahu v aplikacích ASP.NET Core Blazor**  

---

### **1. Úvod**  

- **`<head>` sekce** v HTML obsahuje metadata (titulek, meta tagy, odkazy na CSS/JS), která jsou klíčová pro SEO a funkcionalitu stránky.  
- V Blazor (zejména WebAssembly) je správa `<head>` náročnější, protože aplikace je SPA (Single-Page Application) a obsah se dynamicky mění.  

---

### **2. Nastavení počátečního `<head>` obsahu**  

- **Blazor Server**:  
  - Nastavuje se v `Pages/_Host.cshtml` (např. `<title>`, základní meta tagy).  
- **Blazor WebAssembly**:  
  - Definuje se v `wwwroot/index.html`.  

---

### **3. Dynamické úpravy `<head>` za běhu**  

- **Komponenty pro dynamickou správu**:  
  - **`<PageTitle>`**: Nastavuje titulek stránky.  
  - **`<HeadContent>`**: Přidává libovolný obsah do `<head>` (meta tagy, odkazy na CSS, atd.).  
- **Povinný krok**: Vložit **`<HeadOutlet />`** do `App.razor` nebo layoutu pro vykreslení obsahu.  

#### **Příklad**  

```razor  
<!-- App.razor -->  
<Router AppAssembly="@typeof(Program).Assembly">  
    <!-- ... -->  
</Router>  
<HeadOutlet /> <!-- Bez tohoto se obsah nevykreslí -->  
```  

```razor  
<!-- Stránka nebo komponenta -->  
<PageTitle>@title</PageTitle>  
<HeadContent>  
    <meta name="description" content="@description" />  
    <link rel="stylesheet" href="/custom-styles.css" />  
</HeadContent>  

@code {  
    private string title = "Domů";  
    private string description = "Vítejte v mé aplikaci!";  
}  
```  

---

### **4. SEO a server-side prerendering**  

- **Problém**: Vyhledávače nemusí zpracovat JavaScript (Blazor WebAssembly).  
- **Řešení**:  
  - **Server-side prerendering (SSR)**:  
    - Generuje počáteční HTML na serveru včetně `<head>` obsahu.  
    - Nastavuje se v Hostovaném modelu Blazor WebAssembly.  
  - **Dynamické meta tagy**:  
    - Používejte `<HeadContent>` v kombinaci s SSR pro SEO-friendly metadata.  

---

### **5. Pokročilé techniky**  

- **Stavové služby pro `<head>`**:  
  - Vytvořte službu pro centrální správu metadat (např. při změně stránky).  
  ```csharp  
  public class SeoService  
  {  
      public string Title { get; set; }  
      public string Description { get; set; }  
  }  
  ```  
- **Podmíněný obsah**:  
  ```razor  
  @if (user.IsAdmin)  
  {  
      <HeadContent>  
          <meta name="robots" content="noindex" />  
      </HeadContent>  
  }  
  ```  

---

### **6. Časté problémy a řešení**  

| **Problém**                          | **Řešení**                                 |  
|--------------------------------------|--------------------------------------------|  
| `<head>` obsah se neaktualizuje      | Přidejte `<HeadOutlet />` do `App.razor`. |  
| Duplicitní meta tagy                 | Kontrolujte, zda komponety nepřepisují stejné tagy. |  
| Chybějící metadata pro SEO          | Použijte server-side prerendering.         |  

---

### **7. Best Practices**  

1. **Používejte `<PageTitle>` a `<HeadContent>`** pro všechny dynamické úpravy.  
2. **Centralizujte SEO nastavení** pomocí služeb nebo layoutů.  
3. **Vyhněte se přímé manipulaci s `<head>` přes JavaScript** (kromě nutných případů).  
4. **Testujte s vypnutým JavaScriptem** pro ověření SEO compatibility.  

---

### **Shrnutí**  

| **Funkce**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **`<PageTitle>`**         | Dynamicky nastavuje titulek stránky.                                      |  
| **`<HeadContent>`**       | Vkládá metadata, odkazy na CSS/JS.                                        |  
| **`<HeadOutlet />`**      | Povinný komponent pro vykreslení obsahu `<head>`.                         |  
| **Server-side rendering** | Klíčový pro SEO v Blazor WebAssembly.                                     |  

**Klíčové body**:  
- `<head>` obsah lze dynamicky měnit z jakékoli komponenty.  
- Pro SEO je kritická kombinace SSR a správného použití `<HeadContent>`.  
- Vždy testujte, jak aplikaci vidí vyhledávače (např. pomocí nástrojů Google Search Console).  

**Užitečné zdroje**:  
- Dokumentace: [Blazor `<head>` Content](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/control-head-content).  
- Ukázky: [Blazor SEO Samples](https://github.com/dotnet/blazor-samples).
