
## **Práce s databází v aplikaci ASP.NET Core MVC**  

Integrace databází pomocí Entity Framework Core a základní operace s daty.

---

### **1. Úvod do práce s databází**  

- **Entity Framework Core (EF Core)**: ORM (Object-Relational Mapper) pro mapování objektů na databázové tabulky.  
- **Výhody**:  
  - Automatizace SQL dotazů.  
  - Podpora migrací (správa změn v databázovém schématu).  
  - Práce s daty jako s objekty v C#.  
- **Podporované databáze**: SQL Server, SQLite, PostgreSQL, MySQL a další.

---

### **2. Konfigurace databázového kontextu**  

#### **a) DbContext**  

- Třída dědící od `DbContext` definuje databázové modely a relace.  
- **Příklad**:  
  ```csharp  
  public class AppDbContext : DbContext  
  {  
      public DbSet<Uzivatel> Uzivatele { get; set; }  
      public DbSet<Produkt> Produkty { get; set; }  

      protected override void OnConfiguring(DbContextOptionsBuilder options)  
          => options.UseSqlServer("Server=localhost;Database=MojeDB;Trusted_Connection=True;");  
  }  
  ```  

#### **b) Registrace DbContext v aplikaci**  

- V `Program.cs`:  
  ```csharp  
  builder.Services.AddDbContext<AppDbContext>(options =>  
      options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));  
  ```  
- **Connection string** v `appsettings.json`:  
  ```json  
  "ConnectionStrings": {  
      "DefaultConnection": "Server=localhost;Database=MojeDB;Trusted_Connection=True;"  
  }  
  ```  

---

### **3. Migrace (správa změn schématu)** 

- **Nástroje**:  
  - Příkazy v **Package Manager Console** (Visual Studio).  
  - CLI příkazy (`dotnet ef`).  
- **Kroky**:  
  1. Vytvořte migraci:  
     ```bash  
     dotnet ef migrations add "PridanaTabulkaUzivatelu"  
     ```  
  2. Aplikujte migraci na databázi:  
     ```bash  
     dotnet ef database update  
     ```  

---

### **4. CRUD operace**  

#### **a) Vytvoření záznamu (Create)**  

```csharp  
public class UzivatelController : Controller  
{  
    private readonly AppDbContext _context;  

    public UzivatelController(AppDbContext context)  
    {  
        _context = context;  
    }  

    [HttpPost]  
    public async Task<IActionResult> Vytvor(Uzivatel uzivatel)  
    {  
        _context.Uzivatele.Add(uzivatel);  
        await _context.SaveChangesAsync();  
        return RedirectToAction("Index");  
    }  
}  
```  

#### **b) Čtení záznamů (Read)**  

```csharp  
public IActionResult Index()  
{  
    var uzivatele = _context.Uzivatele.ToList();  
    return View(uzivatele);  
}  

// S filtrací  
public IActionResult Detail(int id)  
{  
    var uzivatel = _context.Uzivatele.FirstOrDefault(u => u.Id == id);  
    return View(uzivatel);  
}  
```  

#### **c) Aktualizace (Update)**

```csharp  
[HttpPost]  
public async Task<IActionResult> Upravit(Uzivatel uzivatel)  
{  
    _context.Uzivatele.Update(uzivatel);  
    await _context.SaveChangesAsync();  
    return RedirectToAction("Index");  
}  
```  

#### **d) Smazání (Delete)**  

```csharp  
[HttpPost]  
public async Task<IActionResult> Smazat(int id)  
{  
    var uzivatel = await _context.Uzivatele.FindAsync(id);  
    if (uzivatel != null)  
    {  
        _context.Uzivatele.Remove(uzivatel);  
        await _context.SaveChangesAsync();  
    }  
    return RedirectToAction("Index");  
}  
```  

---

### **5. Relace mezi entitami**  

#### **a) 1:N (jeden k mnoha)**  

```csharp  
public class Autor  
{  
    public int Id { get; set; }  
    public string Jmeno { get; set; }  
    public List<Kniha> Knihy { get; set; }  
}  

public class Kniha  
{  
    public int Id { get; set; }  
    public string Nazev { get; set; }  
    public int AutorId { get; set; }  
    public Autor Autor { get; set; }  
}  
```  

#### **b) Konfigurace pomocí Fluent API** 

```csharp  
protected override void OnModelCreating(ModelBuilder modelBuilder)  
{  
    modelBuilder.Entity<Autor>()  
        .HasMany(a => a.Knihy)  
        .WithOne(k => k.Autor)  
        .HasForeignKey(k => k.AutorId);  
}  
```  

---

### **6. Doporučené postupy**  

- **Asynchronní metody**: Pro všechny operace s databází (`ToListAsync()`, `SaveChangesAsync()`).  
- **Validace dat**: Ověřujte vstupy před uložením.  
- **Repository pattern**: Oddělte logiku přístupu k datům do samostatných tříd.  
- **Zabezpečení**: Používejte parametrizované dotazy (EF Core to dělá automaticky).  

---

### **7. Časté chyby**  

- **Zapomenuté volání `SaveChangesAsync()`**: Změny se neuloží do DB.  
- **Nesprávné nastavení relací**: Může způsobit chyby při načítání dat.  
- **Nevhodné použití `Include()`**: Může vést k pomalým dotazům (tzv. N+1 problém).  

---

### **8. Ukázka dotazu s LINQ**  

```csharp  
var draheProdukty = await _context.Produkty  
    .Where(p => p.Cena > 1000)  
    .OrderByDescending(p => p.Cena)  
    .ToListAsync();  
```  

---

### **9. Shrnutí**  

- **EF Core** zjednodušuje práci s databází pomocí objektového přístupu.  
- **Migrace** umožňují verzování databázového schématu.  
- **CRUD operace** se provádějí pomocí metod `Add`, `Update`, `Remove` a `SaveChanges`.  
- **Relace** se konfigurují přes navigační vlastnosti nebo Fluent API.  
- **Asynchronní operace** zvyšují výkon aplikace.  

---

**Doporučené zdroje**:  
- Oficiální dokumentace: *Entity Framework Core in ASP.NET Core MVC*  
