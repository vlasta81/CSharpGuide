
## **Middleware v ASP.NET Core MVC – Studijní materiál**  

Principy, funkce a konfigurace komponent pro zpracování HTTP požadavků.

---

### **1. Co je Middleware?**  

- **Definice**: Middleware jsou komponenty, které tvoří **pipeline požadavků a odpovědí** v ASP.NET Core.  
- **Úloha**: Každý Middleware zpracovává příchozí požadavek, předává ho dalšímu komponentu a může upravit odpověď.  
- **Příklady**: Autentizace, logování, zpracování statických souborů, chybové stránky.  

---

### **2. Pipeline požadavků**  

- **Princip**: Požadavek prochází postupně všemi Middleware komponenty v definovaném pořadí.  
- **Způsob zpracování**:  
  - Middleware může:  
    1. **Zpracovat požadavek** a vrátit odpověď (např. statické soubory).  
    2. **Předat požadavek** dalšímu Middleware.  
    3. **Upravit odpověď** po návratu z dalších komponent.  

---

### **3. Konfigurace Middleware v `Program.cs`**  

- **Metody**:  
  - **`Use`**: Přidá Middleware, který může volat další komponentu.  
  - **`Run`**: Přidá terminální Middleware (ukončí pipeline).  
  - **`Map`**: Větví pipeline podle cesty URL.  

#### **Příklad základní konfigurace**:  

```csharp  
var app = builder.Build();  

app.UseExceptionHandler("/Error"); // Zpracování výjimek  
app.UseStaticFiles(); // Slouží statické soubory z wwwroot  
app.UseRouting(); // Definuje routování  
app.UseAuthentication(); // Autentizace  
app.UseAuthorization(); // Autorizace  

app.MapControllerRoute(  
    name: "default",  
    pattern: "{controller=Home}/{action=Index}/{id?}");  

app.Run();  
```  

---

### **4. Vestavěné Middleware komponenty** 

| **Middleware**              | **Popis**                                  |  
|-----------------------------|--------------------------------------------|  
| `UseStaticFiles()`          | Slouží statické soubory (CSS, JS, obrázky). |  
| `UseRouting()`              | Aktivuje systém routování.                 |  
| `UseAuthentication()`       | Zajišťuje ověření identity uživatele.      |  
| `UseAuthorization()`        | Kontroluje oprávnění k přístupu.           |  
| `UseHttpsRedirection()`     | Přesměruje HTTP na HTTPS.                  |  
| `UseExceptionHandler()`     | Zobrazí chybovou stránku při výjimkách.    |  

---

### **5. Vytvoření vlastního Middleware**  

#### **a) Inline Middleware**  

```csharp  
app.Use(async (context, next) =>  
{  
    // Logování před zpracováním  
    Console.WriteLine($"Příchozí požadavek: {context.Request.Path}");  
    await next(); // Volání dalšího Middleware  
    // Logování po zpracování  
    Console.WriteLine($"Odpověď: {context.Response.StatusCode}");  
});  
```  

#### **b) Middleware třída**  

1. Vytvořte třídu s metodou `InvokeAsync`:  
   ```csharp  
   public class CasMiddleware  
   {  
       private readonly RequestDelegate _next;  

       public CasMiddleware(RequestDelegate next)  
       {  
           _next = next;  
       }  

       public async Task InvokeAsync(HttpContext context)  
       {  
           context.Response.Headers.Add("X-Server-Cas", DateTime.Now.ToString());  
           await _next(context);  
       }  
   }  
   ```  
2. Registrujte Middleware v `Program.cs`:  
   ```csharp  
   app.UseMiddleware<CasMiddleware>();  
   ```  

---

### **6. Pořadí Middleware**  

- **Klíčové pravidlo**: Pořadí určuje, jak se komponenty spouští!  
- **Doporučené pořadí**:  
  1. `UseExceptionHandler` (pro zachycení chyb).  
  2. `UseHttpsRedirection`.  
  3. `UseStaticFiles`.  
  4. `UseRouting`.  
  5. `UseAuthentication` + `UseAuthorization`.  
  6. `MapControllerRoute` nebo `MapRazorPages`.  

---

### **7. Příklady použití**  

#### **Větvení pipeline pomocí `Map`**  

```csharp  
app.Map("/admin", adminApp =>  
{  
    adminApp.UseAuthentication();  
    adminApp.UseAuthorization();  
    adminApp.UseRouting();  
    adminApp.MapControllers();  
});  
```  

#### **Podmíněný Middleware**  

```csharp  
app.UseWhen(context => context.Request.Path.StartsWithSegments("/api"), apiApp =>  
{  
    apiApp.UseMiddleware<ApiLoggerMiddleware>();  
});  
```  

---

### **8. Shrnutí**  

- **Middleware** tvoří základ zpracování požadavků v ASP.NET Core.  
- **Pořadí komponent** je klíčové pro správnou funkčnost (např. routování před autorizací).  
- **Vlastní Middleware** umožňuje přizpůsobit chování aplikace.  
- **Vestavěné komponenty** řeší běžné úlohy (autentizace, statické soubory).  

---

**Doporučené zdroje**:  
- Oficiální dokumentace: *Middleware in ASP.NET Core*  
