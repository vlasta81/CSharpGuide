
## **Přidání, stažení a odstranění vlastních uživatelských dat v ASP.NET Core Identity**  

---

### **1. Úvod** 

ASP.NET Core Identity umožňuje rozšířit výchozí model uživatele (`IdentityUser`) o vlastní vlastnosti (např. datum narození, adresa, avatar). Tato data lze přidávat, číst, upravovat a mazat pomocí `UserManager<TUser>` a integrace s Entity Framework Core. Níže je postup pro práci s vlastními daty.

---

### **2. Přidání Vlastních Dat**  

#### **Krok 1: Vytvoření vlastního uživatelského modelu**  

Rozšiřte třídu `IdentityUser` o nové vlastnosti:  
```csharp
public class ApplicationUser : IdentityUser
{
    public string FullName { get; set; }
    public DateTime DateOfBirth { get; set; }
    public string Address { get; set; }
}
```

#### **Krok 2: Aktualizace DbContext**  

Upravte `ApplicationDbContext`, aby používal vaši vlastní třídu:  
```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options) { }
}
```

#### **Krok 3: Registrace vlastního uživatele v DI**  

V `Program.cs`:  
```csharp
builder.Services.AddIdentity<ApplicationUser, IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

#### **Krok 4: Přidání dat při registraci** 

Upravte registrační stránku (např. `Register.cshtml.cs`), aby zachytila nová pole:  
```csharp
public async Task<IActionResult> OnPostAsync(RegisterModel model)
{
    var user = new ApplicationUser
    {
        UserName = model.Email,
        Email = model.Email,
        FullName = model.FullName,
        DateOfBirth = model.DateOfBirth
    };
    
    var result = await _userManager.CreateAsync(user, model.Password);
    // ...
}
```

---

### **3. Stažení (Čtení) Vlastních Dat**  

#### **Příklad: Získání dat v Controlleru**  

```csharp
public async Task<IActionResult> Profile()
{
    var user = await _userManager.GetUserAsync(User);
    if (user == null) return NotFound();

    var userData = new
    {
        user.FullName,
        user.Email,
        user.DateOfBirth
    };

    return View(userData);
}
```

#### **Zobrazení v Razor View**  

```html
<h1>Profil uživatele</h1>
<p>Jméno: @Model.FullName</p>
<p>Email: @Model.Email</p>
<p>Datum narození: @Model.DateOfBirth.ToShortDateString()</p>
```

---

### **4. Odstranění Uživatelských Dat**  

#### **Odstranění celého uživatele**  

Použijte `UserManager.DeleteAsync`:  
```csharp
public async Task<IActionResult> DeleteAccount()
{
    var user = await _userManager.GetUserAsync(User);
    if (user == null) return NotFound();

    var result = await _userManager.DeleteAsync(user);
    if (result.Succeeded)
    {
        await _signInManager.SignOutAsync();
        return RedirectToPage("/Index");
    }
    // ...
}
```

#### **Odstranění konkrétních vlastností** 

Jednoduše aktualizujte hodnotu na `null` (pokud je vlastnost nullable):  
```csharp
user.Address = null;
await _userManager.UpdateAsync(user);
```

---

### **5. Migrace Databáze**  

Po přidání vlastních vlastností je nutné vytvořit migraci:  
```bash
dotnet ef migrations add AddCustomUserFields
dotnet ef database update
```

---

### **6. Důležité Bezpečnostní Pokyny** 

- **Validace vstupů**: Používejte atributy jako `[Required]` nebo `[DataType]` v modelech.  
  ```csharp
  public class RegisterModel
  {
      [Required]
      [DataType(DataType.Date)]
      public DateTime DateOfBirth { get; set; }
  }
  ```
- **Ochrana dat**: Citlivá data (např. datum narození) šifrujte nebo anonymizujte.  
- **Autorizace**: Zabezpečte endpointy atributem `[Authorize]`.

---

### **7. Příklady Použití** 

- **E-shop**: Uložení dodací adresy a preferencí.  
- **Sociální síť**: Správa profilových fotografií a biografie.  
- **Aplikace s předplatným**: Uložení platebních údajů (šifrovaně).

---

### **8. Časté Problémy**  

- **Chybějící migrace**: Pokud se nové sloupce nezobrazí v databázi, spusťte migraci znovu.  
- **Nevalidní data**: Ošetřete výjimky při čtení/ukládání (např. `try-catch` bloky).  
- **Neaktualizovaný UI**: Pokud používáte scaffolded stránky, aktualizujte formuláře o nová pole.

---

### **9. Užitečné Zdroje** 

- **Dokumentace**:  
  [Microsoft Learn – Vlastní uživatelská data v Identity](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/customize-identity-model)  
- **Návody**:  
  - [Custom User Properties in ASP.NET Core Identity](https://code-maze.com/asp-net-core-identity-custom-user-data/)  
  - [ASP.NET Core Identity – Complete Guide](https://www.youtube.com/watch?v=egITMrwMOPU)  

---

**Shrnutí**: Přidání vlastních uživatelských dat do ASP.NET Core Identity je jednoduché díky dědění z `IdentityUser`. Pomocí `UserManager` a EF Core můžete data efektivně spravovat. Nezapomeňte na validaci, migrace a zabezpečení citlivých informací.
