
### Middleware v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Co je Middleware?**  
  Middleware jsou komponenty v ASP.NET Core, které tvoří **pipeline pro zpracování HTTP požadavků a odpovědí**.  
  - **Účel**: Každý Middleware provádí specifickou úlohu (např. autentizace, logování, zpracování statických souborů).  
  - **Princip**: Požadavek prochází postupně všemi Middleware komponentami, dokud není vygenerována odpověď.  

---

#### **2. Klíčové vlastnosti Middleware**  

- **Sekvenční zpracování**: Middleware se spouští v pořadí, v jakém jsou registrované v `Program.cs`.  
- **Možnost ukončení pipeline**: Middleware může zpracovat požadavek a ukončit další volání (např. autentizace selhala → vrátí 401).  
- **Integrace s Razor Pages**: Middleware jako `UseRouting`, `UseEndpoints` a `UseStaticFiles` jsou klíčové pro fungování stránek.  

---

#### **3. Výchozí Middleware Pipeline**  

- Typická konfigurace v `Program.cs`:  
  ```csharp
  var app = builder.Build();

  app.UseExceptionHandler("/Error"); // Zpracování výjimek
  app.UseHsts(); // HTTP Strict Transport Security
  app.UseHttpsRedirection(); // Přesměrování na HTTPS
  app.UseStaticFiles(); // Statické soubory (wwwroot)
  app.UseRouting(); // Směrování
  app.UseAuthentication(); // Autentizace
  app.UseAuthorization(); // Autorizace
  app.MapRazorPages(); // Mapování Razor Pages

  app.Run();
  ```

---

#### **4. Konfigurace Middleware**  

- **Registrace v `Program.cs`**:  
  - `app.UseXyz()`: Přidá Middleware do pipeline (např. `app.UseStaticFiles()`).  
  - `app.Map("/cesta")`: Vytvoří větev pipeline pro specifickou cestu.  
  - `app.Run()`: Terminálový Middleware (ukončí zpracování).  

- **Příklad mapování větve**:  
  ```csharp
  app.Map("/admin", adminApp => 
  {
      adminApp.UseAuthentication();
      adminApp.UseAuthorization();
      adminApp.MapRazorPages();
  });
  ```

---

#### **5. Vytvoření vlastního Middleware**  

##### **a) Třída s metodou `InvokeAsync`**  

```csharp
public class LoggingMiddleware
{
    private readonly RequestDelegate _next;

    public LoggingMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        Console.WriteLine($"Požadavek: {context.Request.Path}");
        await _next(context);
        Console.WriteLine($"Odpověď: {context.Response.StatusCode}");
    }
}

// Registrace v Program.cs
app.UseMiddleware<LoggingMiddleware>();
```

##### **b) Inline Middleware**  

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine("Požadavek začal");
    await next();
    Console.WriteLine("Požadavek dokončen");
});
```

---

#### **6. Integrace Middleware s Razor Pages**  

- **`UseRouting` a `UseEndpoints`**:  
  - `UseRouting`: Určuje, které Middleware zpracovává routování.  
  - `UseEndpoints`: Mapuje koncové body (Razor Pages, API).  

- **Příklad**:  
  ```csharp
  app.UseRouting();
  app.UseEndpoints(endpoints => endpoints.MapRazorPages());
  ```

---

#### **7. Ošetření chyb**  

- **Middleware pro zpracování výjimek**:  
  ```csharp
  app.UseExceptionHandler("/Error"); // Přesměruje na stránku Error.cshtml při chybě
  app.UseStatusCodePagesWithReExecute("/StatusCode/{0}"); // Vlastní stránky pro HTTP kódy
  ```

---

#### **8. Doporučené postupy**  

- **Správné pořadí Middleware**:  
  - Výjimky a HTTPS na začátku.  
  - Statické soubory před routováním.  
  - Autentizace před autorizací.  
- **Minimalizace vlastního Middleware**: Používejte vestavěné komponenty (např. `UseAuthentication`).  
- **Optimalizace výkonu**: Middleware by měl být lehký (např. neblokující operace).  

---

#### **9. Příklady použití**  

##### **Middleware pro přidání hlaviček**  

```csharp
app.Use(async (context, next) =>
{
    context.Response.Headers.Add("X-Content-Type-Options", "nosniff");
    await next();
});
```

##### **Middleware pro měření času odpovědi**  

```csharp
app.Use(async (context, next) =>
{
    var stopwatch = Stopwatch.StartNew();
    await next();
    stopwatch.Stop();
    Console.WriteLine($"Čas zpracování: {stopwatch.ElapsedMilliseconds} ms");
});
```

---

#### **10. Časté problémy**  

- **Nesprávné pořadí Middleware**: Např. `UseRouting` musí předcházet `UseEndpoints`.  
- **Zapomenutá registrace Middleware**: Např. `UseStaticFiles` chybí → statické soubory nefungují.  
- **Nekonečné smyčky**: Pokud Middleware nevolá `await next()`, pipeline se přeruší.  

---

#### **11. Shrnutí**  

- **Middleware** je základním stavebním kamenem ASP.NET Core aplikací.  
- **Pipeline** definuje, jak se požadavky zpracovávají.  
- **Vlastní Middleware** umožňuje rozšířit funkcionalitu (logování, validace, atd.).  
- **Správné pořadí** je klíčové pro stabilitu a bezpečnost.  

---

#### **12. Užitečné zdroje** 

- Dokumentace: [Middleware v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/fundamentals/middleware/)  
- Tutoriál: [Vytvoření vlastního Middleware](https://learn.microsoft.com/cs-cz/aspnet/core/fundamentals/middleware/write)  
- Ukázky: [ASP.NET Core GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/fundamentals/middleware)  
