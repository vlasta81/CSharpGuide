
## **Generování uživatelského rozhraní Identity v ASP.NET Core**

---

### **1. Úvod**  

ASP.NET Core Identity poskytuje **předpřipravené UI** (uživatelské rozhraní) pro správu uživatelů, registraci, přihlašování a další funkce. Toto UI je součástí **Razor Class Library** a lze jej rychle integrovat do projektu. Zároveň je možné jej přizpůsobit pomocí **scaffoldu** (generování vlastních šablon).

---

### **2. Klíčové Funkce**  

- **Hotové stránky**: Registrace, přihlášení, správa profilu, reset hesla, 2FA.  
- **Integrace s Razor Pages**: UI je postaveno na Razor Pages.  
- **Témata a stylování**: Výchozí vzhled lze upravit pomocí CSS/Bootstrap.  
- **Lokalizace**: Podpora překladů do různých jazyků (včetně češtiny).  

---

### **3. Nastavení Výchozího UI**  

#### **Kroky pro generování UI**  

1. **Instalace potřebných balíčků**:  
   ```bash
   dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
   dotnet add package Microsoft.AspNetCore.Identity.UI
   ```

2. **Registrace služeb v `Program.cs`**:  
   ```csharp
   builder.Services.AddDefaultIdentity<IdentityUser>(options => {
       options.SignIn.RequireConfirmedAccount = true; // Vyžadovat potvrzení emailu
   }).AddEntityFrameworkStores<ApplicationDbContext>();
   ```

3. **Použití Identity UI v layoutu**:  
   - Přidat odkazy na přihlášení/odhlášení do `_Layout.cshtml`:  
     ```html
     <partial name="_LoginPartial" />
     ```

4. **Migrace databáze**:  
   ```bash
   dotnet ef migrations add InitialIdentitySetup
   dotnet ef database update
   ```

---

### **4. Přizpůsobení UI pomocí Scaffoldu**  

Scaffolding umožňuje generovat vlastní verze stránek Identity a upravovat je.  

#### **Postup**  

1. **Instalace scaffoldu**:  
   ```bash
   dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
   dotnet tool install -g dotnet-aspnet-codegenerator
   ```

2. **Generování vlastních stránek**:  
   ```bash
   dotnet aspnet-codegenerator identity -dc ApplicationDbContext --files "Account.Register;Account.Login"
   ```
   - `-dc`: DbContext pro Identity.  
   - `--files`: Vygenerovat konkrétní stránky (např. `Account.Register`).  

3. **Úprava vygenerovaných souborů**:  
   - Stránky se uloží do složky `Areas/Identity/Pages/Account`.  
   - Upravte `.cshtml` soubory nebo přidejte vlastní logiku do `PageModel`.  

---

### **5. Přizpůsobení Vzhledu**  

- **CSS/Bootstrap**: Přepište třídy v `_LoginPartial.cshtml` nebo `Register.cshtml`.  
- **Vlastní layout**: Nastavte výchozí layout pro Identity stránky v `_ViewStart.cshtml`:  
  ```html
  @{
      Layout = "/Views/Shared/_CustomLayout.cshtml";
  }
  ```
- **Lokalizace**:  
  - Přidejte lokalizační soubory (`.resx`) do složky `Resources/Identity.Pages`.  
  - Příklad pro češtinu: `Resources/Identity.Pages.Account.Login.cs.resx`.

---

### **6. Pokročilé Možnosti**  

- **Vlastní uživatelský model**:  
  ```csharp
  public class ApplicationUser : IdentityUser {
      public string FullName { get; set; }
  }
  ```
  - Upravte registrační stránku, aby zahrnovala nové vlastnosti.  

- **Rozšíření funkcí**:  
  - Přidejte vlastní validaci hesel.  
  - Implementujte vlastní email službu pro potvrzování účtů.  

---

### **7. Bezpečnostní Důležité Body**  

- **Ochrana proti XSS a CSRF**: Výchozí UI obsahuje zabudovanou ochranu (např. `ValidateAntiForgeryToken`).  
- **HTTPS**: V produkčním prostředí vždy používejte HTTPS.  
- **Validace vstupů**: Upravte stránky, aby omezovaly neplatná data (např. pomocí `DataAnnotations`).  

---

### **8. Výhody a Nevýhody**  

| **Výhody** | **Nevýhody** |  
|------------|--------------|  
| Rychlá implementace | Omezený výchozí vzhled |  
| Integrace s ASP.NET Core | Nutnost scaffoldu pro pokročilé úpravy |  
| Podpora migrací a rolí |  |

---

### **9. Časté Problémy a Řešení**  

- **Stránky Identity se nezobrazují**:  
  - Zkontrolujte, zda je `AddDefaultIdentity` správně nakonfigurováno.  
  - Ověřte, zda jsou Razor Pages přidány v `Program.cs`:  
    ```csharp
    app.MapRazorPages(); // Konec pipeline
    ```

- **Chybějící lokalizace**:  
  - Přidejte chybějící `.resx` soubory nebo použijte `IStringLocalizer`.  

---

### **10. Užitečné Zdroje**  

- **Oficiální dokumentace**:  
  [Microsoft Learn – Scaffold Identity v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/scaffold-identity)  
- **Návody**:  
  - [Customizing ASP.NET Core Identity](https://code-maze.com/asp-net-core-identity-customization/)  
  - [Styling Identity UI s Bootstrap](https://www.youtube.com/watch?v=Q4-QxJjZ8l0)  

---

**Shrnutí**: Generování uživatelského rozhraní Identity v ASP.NET Core šetří čas díky předpřipraveným stránkám. Scaffolding umožňuje flexibilní úpravy, ať už jde o vzhled, lokalizaci nebo přidání vlastní logiky. Pro většinu aplikací je to ideální řešení, které lze snadno rozšířit podle potřeb projektu.
