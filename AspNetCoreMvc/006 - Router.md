
## **Přehled Routingu (Směrování) v ASP.NET Core MVC**  

Principy, konfigurace a pravidla pro mapování URL na akce kontrolerů.

---

### **1. Co je Routing?**  

- **Mechanismus**, který mapuje **URL požadavky** na konkrétní **akční metody kontrolerů**.  
- Určuje, jak se adresy v prohlížeči (např. `/produkty/5`) převádějí na volání kódu.  
- Základní součást architektury MVC pro **přehlednou a čitelnou strukturu URL**.

---

### **2. Typy Routingu**  

1. **Konvenční (Conventional) Routing**  
   - Definuje se **centrálně** v `Program.cs` pomocí šablon.  
   - Příklad:  
     ```csharp  
     app.MapControllerRoute(
         name: "default",
         pattern: "{controller=Home}/{action=Index}/{id?}");
     ```  
   - **Výchozí route**: `/Home/Index` → `HomeController.Index()`.  

2. **Atributové (Attribute) Routing**  
   - Definuje se přímo na kontrolerech a akcích pomocí **atributů** (např. `[Route]`).  
   - Příklad:  
     ```csharp  
     [Route("produkty")]  
     public class ProduktyController : Controller  
     {  
         [HttpGet("detail/{id:int}")]  
         public IActionResult Detail(int id) { ... }  
     }  
     ```  
   - URL: `/produkty/detail/5` → `ProduktyController.Detail(5)`.

---

### **3. Komponenty Routingu**  

- **Route Template**: Šablona cesty (např. `{controller}/{action}/{id?}`).  
- **Parametry**: Zachycují hodnoty z URL (např. `id` v `detail/{id}`).  
- **Omezení (Constraints)**: Ověřují formát parametrů (např. `{id:int}`).  
- **Výchozí hodnoty**: Pokud parametr chybí (např. `controller=Home`).  

---

### **4. Příklady Šablon**  

| **Šablona**                  | **Příklad URL**       | **Mapování**                          |  
|-------------------------------|-----------------------|---------------------------------------|  
| `"blog/{action}/{id?}"`       | `/blog/edit/3`        | `BlogController.Edit(3)`              |  
| `"api/{controller}/v{version}"` | `/api/produkty/v2`    | `ProduktyController` s parametrem `version=2` |  
| `"{nazev:alpha}/info"`        | `/telefony/info`      | Parametr `nazev` musí obsahovat pouze písmena. |  

---

### **5. Omezení Parametrů (Constraints)**  

- **Typ hodnoty**: `{id:int}`, `{isActive:bool}`.  
- **Regex**: `{nazev:regex(^[A-Za-z]+$)}`.  
- **Vlastní omezení**: Implementace rozhraní `IRouteConstraint`.  

#### Příklad:  

```csharp  
[HttpGet("detail/{id:int:min(1)}")]  
public IActionResult Detail(int id) { ... }  
// Přijme pouze ID ≥ 1 (např. `/detail/5`, ne `/detail/abc`).
```

---

### **6. Priorita Routování**  

- **Atributové routy** mají přednost před **konvenčními**.  
- Konkrétnější šablony jsou vybrány před obecnými.  
- Řešení nejednoznačnosti:  
  - Upřesněte omezení.  
  - Explicitně určete pořadí pomocí `Order` v atributu.  

---

### **7. Speciální Případy**  

- **Statické segmenty**: Fixní části URL (např. `"admin/login"`).  
- **Volitelné parametry**: `{id?}` (lze vynechat).  
- **Catch-all parametry**: `{*url}` – zachytí zbytek cesty (např. pro chyby 404).  

---

### **8. Generování URL**  

- **HTML Helpers**:  
  ```html  
  <a asp-controller="Produkty" asp-action="Detail" asp-route-id="5">Detail</a>  
  ```  
- **Metody v kontroleru**:  
  ```csharp  
  var url = Url.Action("Detail", "Produkty", new { id = 5 });  
  ```  

---

### **9. Doporučené Postupy**  

- Pro **API** používejte **atributové routování** (přehlednost, granularita).  
- Pro **MVC aplikace** kombinujte oba přístupy.  
- **Validujte vstupy** pomocí omezení (zamezte neočekávaným hodnotám).  
- **Logujte nejednoznačné routy** pro snazší debugování.  

---

### **10. Časté Chyby**  

- **Nejednoznačné routy**: Dvě šablony odpovídají stejné URL.  
- **Chybějící omezení**: Např. `string` místo `int` pro ID.  
- **Špatný pořádek registrace rout**: Obecné routy by měly být až za konkrétními.  

---

### **11. Ukázka Konfigurace**  

```csharp  
// Program.cs  
app.MapControllerRoute(
    name: "admin",
    pattern: "admin/{action=Login}",
    defaults: new { controller = "Admin" });

app.MapControllerRoute(
    name: "default",
    pattern: "{controller=Home}/{action=Index}/{id?}");
```  

---

### **12. Shrnutí**  

- Routing určuje, **jak se URL mapují na akce kontrolerů**.  
- **Konvenční routování** je vhodné pro MVC aplikace s pevnou strukturou.  
- **Atributové routování** poskytuje větší flexibilitu (zejména pro API).  
- Omezení a validace parametrů zvyšují **bezpečnost a stabilitu**.  
- Správná konfigurace rout zajišťuje **přehlednost a výkon** aplikace.  

--- 

**Doporučené zdroje**:  
- Oficiální dokumentace: *Routing in ASP.NET Core*  
