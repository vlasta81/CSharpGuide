
### **Přehled ASP.NET Core MVC**  

Shrnutí pro pochopení základních konceptů a architektury.

---

### **1. Co je ASP.NET Core MVC?**  

- **Framework** pro tvorbu webových aplikací a API pomocí architektonického vzoru **Model-View-Controller (MVC)**.  
- **Open-source**, multiplatformní (Windows, Linux, macOS), integrovaný s moderními vývojovými nástroji.  
- Postaven na **.NET Core**, podporuje vysoký výkon a škálovatelnost.

---

### **2. Klíčové komponenty MVC**  

1. **Model**  
   - Reprezentuje **data a obchodní logiku**.  
   - Např.: třídy pro práci s databází, validace, výpočty.  
2. **View**  
   - **Vizuální vrstva** – zobrazuje data uživateli (HTML, CSS, Razor syntax).  
   - Používá **Razor templates** pro dynamický obsah (kombinace HTML a C#).  
3. **Controller**  
   - **Řídí tok aplikace** – přijímá požadavky, pracuje s modelem, vrací view.  
   - Obsahuje **akční metody** (action methods), které reagují na URL.

---

### **3. Hlavní vlastnosti**  

- **Separace zájmů** (Separation of Concerns): Čisté rozdělení na Model, View, Controller.  
- **Razor Pages**: Zjednodušený přístup pro menší stránky s vlastní logikou.  
- **Dependency Injection (DI)**: Vestavěná podpora pro injektování závislostí.  
- **Middleware**: Pipeline pro zpracování HTTP požadavků (např. autentizace, logging).  
- **Routing**: Konfigurovatelné cesty (např. `/Home/Index` mapuje na `HomeController.Index()`).  
- **Tag Helpers**: Zjednodušují tvorbu HTML elementů (např. `<a asp-controller="Home" asp-action="Index">`).  
- **Cross-Site Request Forgery (CSRF) Protection**: Automatická ochrana před útoky.

---

### **4. Architektura a tok požadavku**  

1. Uživatel odešle **HTTP požadavek**.  
2. Požadavek prochází **middleware pipeline** (např. autentizace, statické soubory).  
3. **Routing** určí příslušný controller a akční metodu.  
4. Controller komunikuje s **modelem** pro data.  
5. Controller vrátí **view** (s daty z modelu) jako HTML odpověď.  

---

### **5. Struktura projektu**  

Typické složky a soubory:  
- **Models**: Třídy pro data a logiku.  
- **Views**: Razor soubory (.cshtml) pro UI.  
- **Controllers**: Třídy s akčními metodami.  
- **wwwroot**: Statické soubory (CSS, JS, obrázky).  
- **appsettings.json**: Konfigurace (např. connection stringy).  
- **Program.cs**: Vstupní bod aplikace (nastavení middleware, služeb).

---

### **6. Výhody a nevýhody**  

| **Výhody**                                  | **Nevýhody**                          |  
|---------------------------------------------|----------------------------------------|  
| Multiplatformnost                           | Vyšší náročnost na naučení (C#, .NET) |  
| Vysoký výkon (Kestrel web server)           | Komplexita pro jednoduché aplikace     |  
| Flexibilita (middleware, DI, konfigurace)    |                                       |  
| Integrace s moderními nástroji (React, API) |                                       |  

---

### **7. Typické use cases**  

- Enterprise aplikace s komplexní logikou.  
- RESTful API pro mobilní nebo frontend aplikace (např. Angular).  
- E-shopy, informační systémy.  

---

### **8. Základní kroky pro vytvoření aplikace**  

1. Nainstalujte **.NET SDK**.  
2. Vytvořte projekt:  
   ```bash  
   dotnet new mvc -n MojeAplikace  
   ```  
3. Přidejte model (např. `Uzivatel.cs` ve složce **Models**).  
4. Vytvořte controller s akčními metodami.  
5. Přidejte view pro zobrazení dat.  
6. Spusťte aplikaci:  
   ```bash  
   dotnet run  
   ```  

---

### **9. Ukázka kódu**  

**Controller (`HomeController.cs`):**  
```csharp  
public class HomeController : Controller  
{  
    public IActionResult Index()  
    {  
        var model = new { Pozdrav = "Vítejte v ASP.NET Core MVC!" };  
        return View(model);  
    }  
}  
```  

**View (`Index.cshtml`):**  
```html  
@model dynamic  
<h1>@Model.Pozdrav</h1>  
<p>Dnešní datum: @DateTime.Now.ToString("d")</p>  
```  

---

### **10. Závěr**  

ASP.NET Core MVC je výkonný nástroj pro tvorbu moderních webových aplikací s čitelnou architekturou. Díky podpoře DI, middleware a open-source komunitě je vhodný jak pro malé projekty, tak pro enterprise řešení. Pro hlubší studium doporučujeme oficiální dokumentaci Microsoftu a praktické workshopy.
