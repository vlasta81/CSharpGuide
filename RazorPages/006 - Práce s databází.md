
### Práce s databází v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Propojení Razor Pages a databáze**:  
  Razor Pages využívá **Entity Framework Core (EF Core)** jako ORM (Object-Relational Mapper) pro práci s databází.  
  - **Výhody**: Automatizace CRUD operací, LINQ dotazy, migrace schématu.  
  - **Typické scénáře**: Blog, e-shop, správa uživatelů.  

---

#### **2. Nastavení DbContext**  

1. **Definice DbContextu**:  
   - Třída dědící z `DbContext` reprezentuje relaci s databází.  
   - Obsahuje `DbSet<T>` pro každou entitu.  
   ```csharp
   public class AppDbContext : DbContext
   {
       public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
       public DbSet<Product> Products { get; set; }
   }
   ```

2. **Registrace DbContextu**:  
   - V `Program.cs` přidejte službu a konfigurujte připojení.  
   ```csharp
   builder.Services.AddDbContext<AppDbContext>(options =>
       options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));
   ```

3. **Connection String**:  
   - Uložte do `appsettings.json`.  
   ```json
   {
     "ConnectionStrings": {
       "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=MyDatabase;Trusted_Connection=True;"
     }
   }
   ```

---

#### **3. Migrace**  

- **Vytvoření migrace**:  
  ```bash
  dotnet ef migrations add InitialCreate
  ```
- **Aplikace migrace**:  
  ```bash
  dotnet ef database update
  ```

---

#### **4. CRUD Operace v PageModel**  

##### **a) Čtení dat (Read)**  

- **Získání všech záznamů**:  
  ```csharp
  public class IndexModel : PageModel
  {
      private readonly AppDbContext _context;
      public List<Product> Products { get; set; }

      public IndexModel(AppDbContext context) => _context = context;

      public void OnGet()
      {
          Products = _context.Products.ToList();
      }
  }
  ```

##### **b) Vytvoření záznamu (Create)**  

```csharp
public class CreateModel : PageModel
{
    [BindProperty]
    public Product Product { get; set; }

    public void OnGet() { }

    public async Task<IActionResult> OnPostAsync()
    {
        if (ModelState.IsValid)
        {
            _context.Products.Add(Product);
            await _context.SaveChangesAsync();
            return RedirectToPage("/Index");
        }
        return Page();
    }
}
```

##### **c) Aktualizace (Update)**  

```csharp
public class EditModel : PageModel
{
    [BindProperty]
    public Product Product { get; set; }

    public async Task<IActionResult> OnGetAsync(int id)
    {
        Product = await _context.Products.FindAsync(id);
        if (Product == null) return NotFound();
        return Page();
    }

    public async Task<IActionResult> OnPostAsync()
    {
        _context.Products.Update(Product);
        await _context.SaveChangesAsync();
        return RedirectToPage("/Index");
    }
}
```

##### **d) Smazání (Delete)**  

```csharp
public async Task<IActionResult> OnPostDeleteAsync(int id)
{
    var product = await _context.Products.FindAsync(id);
    if (product != null)
    {
        _context.Products.Remove(product);
        await _context.SaveChangesAsync();
    }
    return RedirectToPage("/Index");
}
```

---

#### **5. Validace dat**  

- **DataAnnotations**: Přidejte validační atributy k entitám.  
  ```csharp
  public class Product
  {
      public int Id { get; set; }
      [Required(ErrorMessage = "Název je povinný")]
      public string Name { get; set; }
      [Range(1, 1000, ErrorMessage = "Cena musí být mezi 1 a 1000")]
      public decimal Price { get; set; }
  }
  ```

- **Zobrazení chyb ve View**:  
  ```html
  <input asp-for="Product.Name" />
  <span asp-validation-for="Product.Name" class="text-danger"></span>
  ```

---

#### **6. Asynchronní operace**  

- **Doporučení**: Pro lepší výkon používejte asynchronní metody (`ToListAsync`, `SaveChangesAsync`).  
- **Příklad**:  
  ```csharp
  public async Task OnGetAsync()
  {
      Products = await _context.Products.ToListAsync();
  }
  ```

---

#### **7. Bezpečnostní doporučení**  

- **Nikdy nedůvěřujte vstupům**: Vždy validujte data před uložením.  
- **SQL Injection**: EF Core automaticky parametrizuje dotazy – nekonkatenujte řetězce!  
- **Omezení přístupu**: Používejte autorizační pravidla pro citlivé operace.  

---

#### **8. Časté problémy** 

- **Zapomenuté `SaveChanges()`**: Změny se neprojeví v DB.  
- **Nezachycené výjimky**: Ošetřete chyby pomocí `try-catch`.  
- **Nesprávné mapování entit**: Kontrolujte migrace a `DbSet` vlastnosti.  

---

#### **9. Shrnutí**  

- **EF Core** zjednodušuje práci s databází v Razor Pages.  
- **DbContext** slouží jako most mezi aplikací a DB.  
- **CRUD operace** se provádějí pomocí metod `Add`, `Update`, `Remove` a `SaveChanges`.  
- **Asynchronní přístup** zlepšuje výkon aplikace.  
- **Validace** je klíčová pro integritu dat.  

---

#### **10. Užitečné zdroje**  

- Dokumentace: [EF Core v ASP.NET Core](https://learn.microsoft.com/cs-cz/ef/core/)  
- Tutoriál: [Práce s daty v Razor Pages](https://learn.microsoft.com/cs-cz/aspnet/core/tutorials/razor-pages/sql)  
