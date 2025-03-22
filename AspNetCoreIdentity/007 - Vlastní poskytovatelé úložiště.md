
## **Vlastní poskytovatelé úložištì pro ASP.NET Core Identity**  

---

### **1. Úvod**  

ASP.NET Core Identity standardnì využívá **Entity Framework Core** pro ukládání uživatelù, rolí a souvisejících dat. Pokud potøebujete použít jiné úložištì (napø. MongoDB, Redis, externí API, nebo vlastní databázi), mùžete implementovat **vlastní poskytovatele úložištì** (custom storage providers). Tím pøekryjete výchozí chování a plnì pøevezmete správu dat.

---

### **2. Kdy Použít Vlastní Úložištì?**  

- **Nekompatibilní databáze**: Pokud nechcete/používat SQL Server, PostgreSQL, nebo jiné podporované databáze EF Core.  
- **NoSQL databáze**: Napø. MongoDB, Cosmos DB, Cassandra.  
- **Externí systémy**: Ukládání dat do CRM, ERP, nebo mikroservis.  
- **Optimalizace výkonu**: Vlastní implementace pro specifické potøeby (napø. caching).  

---

### **3. Klíèová Rozhraní (Interfaces)**  

Pro vytvoøení vlastního úložištì je tøeba implementovat rozhraní z balíèku `Microsoft.AspNetCore.Identity`. Základní rozhraní:  

| **Rozhraní** | **Popis** |  
|--------------|-----------|  
| `IUserStore<TUser>` | Správa uživatelù (Create, Update, Delete, Find). |  
| `IUserPasswordStore<TUser>` | Ukládání a správa hesel. |  
| `IUserRoleStore<TUser>` | Pøiøazování rolí uživatelùm. |  
| `IRoleStore<TRole>` | Správa rolí. |  
| `IUserEmailStore<TUser>` | Ovìøování e-mailù. |  

---

### **4. Implementace Vlastního Úložištì – Pøíklad**  

#### **Krok 1: Vytvoøení vlastního `UserStore`**  

```csharp
public class CustomUserStore : IUserStore<IdentityUser>, IUserPasswordStore<IdentityUser>
{
    // Implementace IUserStore
    public Task<IdentityResult> CreateAsync(IdentityUser user, CancellationToken cancellationToken)
    {
        // Uložení uživatele do vlastního úložištì (napø. MongoDB)
        return Task.FromResult(IdentityResult.Success);
    }

    public Task<IdentityUser> FindByIdAsync(string userId, CancellationToken cancellationToken)
    {
        // Naètení uživatele z vlastního úložištì
        return Task.FromResult(new IdentityUser { Id = userId });
    }

    // Další metody (UpdateAsync, DeleteAsync, FindByNameAsync...)

    // Implementace IUserPasswordStore
    public Task<string> GetPasswordHashAsync(IdentityUser user, CancellationToken cancellationToken)
    {
        return Task.FromResult(user.PasswordHash);
    }

    public Task SetPasswordHashAsync(IdentityUser user, string passwordHash, CancellationToken cancellationToken)
    {
        user.PasswordHash = passwordHash;
        return Task.CompletedTask;
    }

    // ... (Dispose a další povinné metody)
}
```

#### **Krok 2: Registrace vlastního úložištì v DI**  

V `Program.cs`:  
```csharp
builder.Services.AddIdentityCore<IdentityUser>()
    .AddUserStore<CustomUserStore>()
    .AddDefaultTokenProviders();
```

---

### **5. Dùležité Body**  

- **Hashování hesel**: ASP.NET Core Identity nehashuje hesla automaticky v custom úložištích. Musíte použít `IPasswordHasher<IdentityUser>`.  
- **Validace dat**: Ovìøujte data pøed uložením (napø. duplicitní e-maily).  
- **Transakce**: Pokud úložištì podporuje transakce, implementujte atomické operace.  

---

### **6. Výhody a Nevýhody**  

| **Výhody** | **Nevýhody** |  
|------------|--------------|  
| Flexibilita pro jakékoli úložištì | Vyžaduje ruèní implementaci všech metod |  
| Integrace s legacy systémy | Složitìjší ladìní a testování |  
| Optimalizace výkonu | Nutnost øešit bezpeènostní aspekty (napø. hashování) |  

---

### **7. Pøíklady Scénáøù**  

- **MongoDB User Store**: Ukládání uživatelù do NoSQL databáze.  
- **Redis pro rychlý pøístup**: Cache uživatelských dat.  
- **Integrace s Active Directory**: Autentizace pøes LDAP.  

---

### **8. Bezpeènostní Doporuèení** 

- **Hashování hesel**: Vždy použijte algoritmy jako PBKDF2, bcrypt nebo Argon2.  
- **Ochrana proti útoku hrubou silou**: Omezte poèet pokusù o pøihlášení.  
- **Šifrování citlivých dat**: Napø. e-maily nebo telefonní èísla.  

---

### **9. Užiteèné Zdroje**  

- **Dokumentace**:  
  [Microsoft Learn – Vlastní úložištì Identity](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/identity-custom-storage-providers)  
- **Návody**:  
  - [Implementace UserStore pro MongoDB](https://www.mongodb.com/developer/languages/csharp/csharp-identity-storage-provider/)  
  - [Vlastní úložištì s Dapper](https://code-maze.com/asp-net-core-identity-custom-storage-provider/)  

---

**Shrnutí**: Vlastní poskytovatelé úložištì umožòují plnì nahradit výchozí EF Core implementaci ASP.NET Core Identity. Klíèové je implementovat potøebná rozhraní (napø. `IUserStore`, `IRoleStore`) a zajistit bezpeènostní standardy. Tento pøístup je vhodný pro integraci s nestandardními databázemi nebo externími systémy.
