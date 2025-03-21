
## **Izolace šablon stylů CSS v ASP.NET Core Blazor**

---

### **1. Izolace šablon stylů CSS**  

- **Cíl**: Zabránit konfliktům CSS tříd mezi komponentami pomocí **automatického oborování stylů** na úrovni komponenty.  
- **Princip**:  
  - Pro každou komponentu (`.razor`) se vytvoří soubor `.razor.css` s příponou `.scoped.css` (např. `Komponenta.razor.css`).  
  - Blazor při kompilaci **generuje unikátní identifikátory** pro CSS třídy, které jsou platné pouze pro danou komponentu.  
- **Výhody**:  
  - Žádné kolize názvů tříd.  
  - Snadná údržba stylů specifických pro komponentu.  

---

### **2. Sdružování izolovaných stylů (Bundling)**  

- **Automatické sestavení**: Izolované CSS soubory se při buildování **sloučí do jednoho souboru** (např. `{Projekt}.styles.css`) v `wwwroot`.  
- **Integrace**: Soubor se automaticky referenceuje v `_Layout.cshtml` nebo `index.html`.  
- **Konfigurace**:  
  ```xml  
  <!-- V .csproj souboru -->  
  <PropertyGroup>  
    <ScopedCssEnabled>true</ScopedCssEnabled>  
  </PropertyGroup>  
  ```  

---

### **3. Podpora podřízených komponent**  

- **Izolace hierarchie**:  
  - Každá komponenta (včetně podřízených) má vlastní izolované styly.  
  - Styly rodičovské komponenty **neovlivňují** podřízené komponenty (pokud není použito `::deep`).  
- **Příklad**:  
  ```css  
  /* Rodičovská komponenta.razor.css */  
  .text { color: red; }  

  ::deep .child-style { color: blue; } /* Pronikne do podřízených komponent */  
  ```  

---

### **4. Podpora preprocesorů CSS (Sass/Less)**  

- **Nativní podpora**: Blazor nepodporuje preprocesory přímo, ale lze je integrovat přes:  
  - **Nástroje jako WebCompiler**: Automatická kompilace `.scss`/`.less` do `.css`.  
  - **Ruční konfigurace buildování**:  
    ```xml  
    <!-- V .csproj souboru -->  
    <Target Name="CompileSass" BeforeTargets="Compile">  
      <Exec Command="sass Styles/app.scss wwwroot/css/app.css" />  
    </Target>  
    ```  

---

### **5. Konfigurace izolace CSS**  

- **Vypnutí izolace**:  
  ```xml  
  <PropertyGroup>  
    <ScopedCssEnabled>false</ScopedCssEnabled>  
  </PropertyGroup>  
  ```  
- **Vlastní název souboru**:  
  ```xml  
  <ScopedCssBundleName>custom-styles.css</ScopedCssBundleName>  
  ```  

---

### **6. Podpora knihovny tříd Razor (Razor Class Library – RCL)** 

- **Izolace v RCL**:  
  - Komponenty v RCL mohou mít vlastní izolované styly.  
  - Při publikování knihovny se styly **automaticky přidají do hostující aplikace**.  
- **Reference stylů z RCL**:  
  ```html  
  <!-- V hostující aplikaci (wwwroot/index.html nebo _Layout.cshtml) -->  
  <link href="_content/{NázevKnihovny}/styles.css" rel="stylesheet" />  
  ```  

---

### **7. Časté problémy a řešení** 

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|--------------------------------------------|  
| Styly z RCL se nezobrazují           | Zkontrolujte cestu v `href="_content/..."`.|  
| Preprocesory nefungují               | Nastavte kompilaci přes buildovací nástroje.|  
| Globalní styly přebíjejí izolované   | Používejte `::deep` pro cílené přepsání.   |  

---

### **8. Best Practices**  

- **Izolované styly**: Používejte primárně pro komponenty s unikátním vzhledem.  
- **Globalní styly**: Uchovávejte v `wwwroot/css` pro společné prvky (např. reset CSS).  
- **Preprocesory**: Organizujte proměnné a mixiny v samostatných souborech (např. `_variables.scss`).  

---

### **Shrnutí**  

| **Funkce**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Izolace CSS**          | Automatické oborování tříd na úrovni komponenty.                          |  
| **Bundling**             | Sloučení všech izolovaných stylů do jednoho souboru.                      |  
| **RCL podpora**          | Styly z knihoven se automaticky propagují do hostující aplikace.          |  
| **Preprocesory**         | Podpora přes externí nástroje (Sass/Less).                                |  

**Užitečné zdroje**:  
- Dokumentace: [CSS Isolation in Blazor](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/css-isolation).  
- Nástroj WebCompiler: [NuGet balíček](https://www.nuget.org/packages/BuildWebCompiler/).
