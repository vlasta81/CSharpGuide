
## **Rozložení (Layout) v ASP.NET Core Blazor**  

Rozložení (layout) v Blazor slouží k definici **společné struktury aplikace**, která se opakuje na více stránkách (např. hlavička, navigace, patička). Umožňuje centralizovat UI prvky a zjednodušit údržbu.

---

### **1. Základní koncept**  

- **Layout komponenta**:  
  - Je to standardní Blazor komponenta (soubor `.razor`).  
  - Obsahuje **`@Body` direktivu**, která určuje místo pro vykreslení obsahu jednotlivých stránek.  
  - Dědí z `LayoutComponentBase` (poskytuje vlastnost `Body` typu `RenderFragment`).  

**Příklad (MainLayout.razor)**:  
```razor
@inherits LayoutComponentBase

<header>
    <h1>Má aplikace</h1>
    <NavMenu />
</header>

<main>
    @Body <!-- Zde se vykreslí obsah stránky -->
</main>

<footer>
    <p>© 2024</p>
</footer>
```

---

### **2. Nastavení výchozího rozložení**  

- **Globální nastavení**: V souboru `App.razor` pomocí atributu `DefaultLayout`.  
  ```razor
  <Router AppAssembly="@typeof(Program).Assembly" DefaultLayout="@typeof(MainLayout)">
      <!-- ... -->
  </Router>
  ```  
- **Lokální nastavení**: Přepsání rozložení pro konkrétní stránku pomocí direktivy `@layout`.  
  ```razor
  @page "/admin"
  @layout AdminLayout <!-- Vlastní layout pro administraci -->
  ```

---

### **3. Vnořená rozložení (Nested Layouts)**  

- **Princip**: Rozložení může používat jiné rozložení jako svůj základ.  
- **Příklad**:  
  ```razor
  <!-- AdminLayout.razor -->
  @inherits LayoutComponentBase
  @layout MainLayout <!-- Používá MainLayout jako nadřazené -->

  <div class="admin-panel">
      @Body <!-- Obsah administrační části -->
  </div>
  ```

---

### **4. Dynamická změna rozložení**  

- **Scénář**: Jiné rozložení pro přihlášené/nepřihlášené uživatele.  
- **Řešení**:  
  - Použijte službu `NavigationManager` nebo stav aplikace k výběru layoutu.  
  ```razor
  @inject AuthenticationStateProvider AuthProvider

  <AuthorizeView>
      <Authorized>
          <MainLayout>
              @Body
          </MainLayout>
      </Authorized>
      <NotAuthorized>
          <GuestLayout>
              @Body
          </GuestLayout>
      </NotAuthorized>
  </AuthorizeView>
  ```

---

### **5. SEO a metadata v rozložení**  

- **Komponenty `<PageTitle>` a `<HeadContent>`**:  
  - Umožňují nastavit titul stránky a meta tagy dynamicky.  
  ```razor
  <PageTitle>@Title</PageTitle>
  <HeadContent>
      <meta name="description" content="@Description" />
  </HeadContent>

  @code {
      private string Title = "Domovská stránka";
      private string Description = "Vítejte v mé aplikaci!";
  }
  ```

---

### **6. Časté problémy a řešení**  

| **Problém**                          | **Řešení**                                 |  
|--------------------------------------|--------------------------------------------|  
| Rozložení se neaplikuje              | Zkontrolujte `App.razor` a direktivu `@layout`. |  
| Chybějící `@Body` v layoutu          | Přidejte `@Body` do layout komponenty.     |  
| Kolize CSS mezi layouty              | Použijte izolované CSS soubory (např. `MainLayout.razor.css`). |  

---

### **7. Best Practices**  

- **Centralizace stylů**: Přidružené CSS soubory k layoutům (např. `MainLayout.razor.css`).  
- **Znovupoužitelné komponenty**: Navigační menu nebo patičku implementujte jako samostatné komponenty.  
- **Minimalizace logiky v layoutu**: Rozložení by mělo být primárně pro UI, ne pro obchodní logiku.  

---

### **Shrnutí**  

| **Aspekt**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Výchozí layout**        | Definován v `App.razor` přes `DefaultLayout`.                             |  
| **@Body direktiva**       | Určuje místo pro obsah stránky.                                           |  
| **Dynamické layouty**     | Lze měnit podle stavu aplikace (např. autentizace).                       |  
| **SEO**                   | Metadata se nastavují pomocí `<PageTitle>` a `<HeadContent>`.             |  

**Klíčové body**:  
- Layouty zajišťují konzistenci UI napříč aplikací.  
- Lze kombinovat více vrstev layoutů (vnořené layouty).  
- Dynamická změna layoutu umožňuje přizpůsobení různým scénářům.  

**Užitečné zdroje**:  
- Oficiální dokumentace: [Blazor Layouts](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/layouts).  
- Ukázky: [Blazor Layout Samples](https://github.com/dotnet/blazor-samples).
