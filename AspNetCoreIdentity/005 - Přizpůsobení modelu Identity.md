
## **Přizpůsobení modelu Identity v ASP.NET Core**

---

### **1. Úvod**  

ASP.NET Core Identity poskytuje výchozí modely pro správu uživatelů (`IdentityUser`) a rolí (`IdentityRole`). Tyto modely lze přizpůsobit, aby odpovídaly specifickým požadavkům aplikace – například přidáním vlastních vlastností, změnou datových typů klíčů nebo úpravou pravidel validace. Cílem je rozšířit funkcionalitu bez nutnosti psaní kódu od nuly.

---

### **2. Klíčové Možnosti Přizpůsobení**  

- **Rozšíření uživatelského/rolového modelu**: Přidání nových vlastností (např. `FullName`, `DateOfBirth`).  
- **Změna primárního klíče**: Použití `Guid` nebo `int` místo výchozího `string`.  
- **Úprava pravidel validace**: Nastavení délky hesla, požadavků na e-mail atd.  
- **Vlastní úložiště dat**: Integrace s jinou databází než Entity Framework Core.  
- **Přizpůsobení UI**: Modifikace Razor stránek pro registraci, přihlášení atd.  

---

### **3. Postup Přizpůsobení**  

#### **Krok 1: Vytvoření vlastního uživatelského modelu**  

Rozšiřte třídu `IdentityUser`:  
```csharp
public class ApplicationUser : IdentityUser<Guid> // Použití Guid jako klíče
{
    public string FullName { get; set; }
    public DateTime DateOfBirth { get; set; }
    public string? ProfilePhotoUrl { get; set; } // Nullable vlastnost
}
```

#### **Krok 2: Vytvoření vlastní role (volitelné)**  

```csharp
public class ApplicationRole : IdentityRole<Guid>
{
    public string Description { get; set; }
}
```

#### **Krok 3: Aktualizace DbContext**  

Upravte `ApplicationDbContext`, aby používal vaše modely:  
```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser, ApplicationRole, Guid>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) 
        : base(options) { }
}
```

#### **Krok 4: Registrace služeb v `Program.cs`**  

```csharp
builder.Services.AddIdentity<ApplicationUser, ApplicationRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>()
    .AddDefaultTokenProviders();

// Přizpůsobení pravidel validace
builder.Services.Configure<IdentityOptions>(options =>
{
    options.Password.RequiredLength = 8;
    options.User.RequireUniqueEmail = true;
});
```

---

### **4. Běžné Úpravy**  

#### **Změna primárního klíče na `Guid`**  

- Vlastní uživatelský a rolový model musí dědit z `IdentityUser<TKey>` a `IdentityRole<TKey>`.  
- V DbContext explicitně nastavte typ klíče:  
  ```csharp
  public class ApplicationDbContext : IdentityDbContext<ApplicationUser, ApplicationRole, Guid>
  ```

#### **Přidání vlastních vlastností**  

- Přidejte pole do `ApplicationUser` a následně proveďte migraci databáze.  

#### **Úprava pravidel pro hesla**  

```csharp
builder.Services.Configure<IdentityOptions>(options =>
{
    options.Password.RequireDigit = false;
    options.Password.RequireLowercase = true;
});
```

---

### **5. Migrace Databáze**  

Po úpravě modelů vytvořte migraci:  
```bash
dotnet ef migrations add CustomizeIdentityModel
dotnet ef database update
```

---

### **6. Bezpečnostní Důležité Body**  

- **Validace vstupů**: Vždy ověřujte data pomocí atributů jako `[Required]` nebo `[StringLength]`.  
- **Šifrování citlivých dat**: Pokud ukládáte citlivé informace (např. rodná čísla), použijte šifrování.  
- **Omezení přístupu**: Zabezpečte administrační rozhraní atributem `[Authorize(Roles = "Admin")]`.  

---

### **7. Výhody a Nevýhody**  

| **Výhody** | **Nevýhody** |  
|------------|--------------|  
| Flexibilní přizpůsobení potřebám aplikace | Nutnost aktualizovat migrace při změnách |  
| Kompatibilita s Entity Framework Core | Složitější konfigurace pro pokročilé scénáře |  
| Jednoduchá integrace s ASP.NET Core |  |  

---

### **8. Časté Problémy**  

- **Chybějící migrace**: Po přidání vlastností spusťte `dotnet ef database update`.  
- **Neplatné typy klíčů**: Pokud použijete `Guid`, ujistěte se, že všechny reference (např. v `UserManager`) jsou aktualizovány.  
- **Neaktivní služby**: Kontrolujte, zda je `AddIdentity` správně nakonfigurováno v `Program.cs`.  

---

### **9. Užitečné Zdroje**  

- **Oficiální dokumentace**:  
  [Microsoft Learn – Vlastní model Identity](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/customize-identity-model)  
- **Návody**:  
  - [Customizing ASP.NET Core Identity](https://code-maze.com/asp-net-core-identity-custom-user-data/)  
  - [Using GUID as Primary Key](https://www.thereformedprogrammer.net/using-guid-keys-in-asp-net-core-identity/)  

---

**Shrnutí**: Přizpůsobení modelu Identity v ASP.NET Core umožňuje vytvořit řešení na míru vaší aplikaci. Klíčové je rozšíření tříd `IdentityUser` a `IdentityRole`, aktualizace DbContext a správná konfigurace služeb. S dobrou migrační strategií a validací dat získáte robustní a bezpečný systém správy uživatelů.
