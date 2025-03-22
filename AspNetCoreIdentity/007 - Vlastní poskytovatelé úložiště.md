
## **Vlastn� poskytovatel� �lo�i�t� pro ASP.NET Core Identity**  

---

### **1. �vod**  

ASP.NET Core Identity standardn� vyu��v� **Entity Framework Core** pro ukl�d�n� u�ivatel�, rol� a souvisej�c�ch dat. Pokud pot�ebujete pou��t jin� �lo�i�t� (nap�. MongoDB, Redis, extern� API, nebo vlastn� datab�zi), m��ete implementovat **vlastn� poskytovatele �lo�i�t�** (custom storage providers). T�m p�ekryjete v�choz� chov�n� a pln� p�evezmete spr�vu dat.

---

### **2. Kdy Pou��t Vlastn� �lo�i�t�?**  

- **Nekompatibiln� datab�ze**: Pokud nechcete/pou��vat SQL Server, PostgreSQL, nebo jin� podporovan� datab�ze EF Core.  
- **NoSQL datab�ze**: Nap�. MongoDB, Cosmos DB, Cassandra.  
- **Extern� syst�my**: Ukl�d�n� dat do CRM, ERP, nebo mikroservis.  
- **Optimalizace v�konu**: Vlastn� implementace pro specifick� pot�eby (nap�. caching).  

---

### **3. Kl��ov� Rozhran� (Interfaces)**  

Pro vytvo�en� vlastn�ho �lo�i�t� je t�eba implementovat rozhran� z bal��ku `Microsoft.AspNetCore.Identity`. Z�kladn� rozhran�:  

| **Rozhran�** | **Popis** |  
|--------------|-----------|  
| `IUserStore<TUser>` | Spr�va u�ivatel� (Create, Update, Delete, Find). |  
| `IUserPasswordStore<TUser>` | Ukl�d�n� a spr�va hesel. |  
| `IUserRoleStore<TUser>` | P�i�azov�n� rol� u�ivatel�m. |  
| `IRoleStore<TRole>` | Spr�va rol�. |  
| `IUserEmailStore<TUser>` | Ov��ov�n� e-mail�. |  

---

### **4. Implementace Vlastn�ho �lo�i�t� � P��klad**  

#### **Krok 1: Vytvo�en� vlastn�ho `UserStore`**  

```csharp
public class CustomUserStore : IUserStore<IdentityUser>, IUserPasswordStore<IdentityUser>
{
    // Implementace IUserStore
    public Task<IdentityResult> CreateAsync(IdentityUser user, CancellationToken cancellationToken)
    {
        // Ulo�en� u�ivatele do vlastn�ho �lo�i�t� (nap�. MongoDB)
        return Task.FromResult(IdentityResult.Success);
    }

    public Task<IdentityUser> FindByIdAsync(string userId, CancellationToken cancellationToken)
    {
        // Na�ten� u�ivatele z vlastn�ho �lo�i�t�
        return Task.FromResult(new IdentityUser { Id = userId });
    }

    // Dal�� metody (UpdateAsync, DeleteAsync, FindByNameAsync...)

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

    // ... (Dispose a dal�� povinn� metody)
}
```

#### **Krok 2: Registrace vlastn�ho �lo�i�t� v DI**  

V `Program.cs`:  
```csharp
builder.Services.AddIdentityCore<IdentityUser>()
    .AddUserStore<CustomUserStore>()
    .AddDefaultTokenProviders();
```

---

### **5. D�le�it� Body**  

- **Hashov�n� hesel**: ASP.NET Core Identity nehashuje hesla automaticky v custom �lo�i�t�ch. Mus�te pou��t `IPasswordHasher<IdentityUser>`.  
- **Validace dat**: Ov��ujte data p�ed ulo�en�m (nap�. duplicitn� e-maily).  
- **Transakce**: Pokud �lo�i�t� podporuje transakce, implementujte atomick� operace.  

---

### **6. V�hody a Nev�hody**  

| **V�hody** | **Nev�hody** |  
|------------|--------------|  
| Flexibilita pro jak�koli �lo�i�t� | Vy�aduje ru�n� implementaci v�ech metod |  
| Integrace s legacy syst�my | Slo�it�j�� lad�n� a testov�n� |  
| Optimalizace v�konu | Nutnost �e�it bezpe�nostn� aspekty (nap�. hashov�n�) |  

---

### **7. P��klady Sc�n���**  

- **MongoDB User Store**: Ukl�d�n� u�ivatel� do NoSQL datab�ze.  
- **Redis pro rychl� p��stup**: Cache u�ivatelsk�ch dat.  
- **Integrace s Active Directory**: Autentizace p�es LDAP.  

---

### **8. Bezpe�nostn� Doporu�en�** 

- **Hashov�n� hesel**: V�dy pou�ijte algoritmy jako PBKDF2, bcrypt nebo Argon2.  
- **Ochrana proti �toku hrubou silou**: Omezte po�et pokus� o p�ihl�en�.  
- **�ifrov�n� citliv�ch dat**: Nap�. e-maily nebo telefonn� ��sla.  

---

### **9. U�ite�n� Zdroje**  

- **Dokumentace**:  
  [Microsoft Learn � Vlastn� �lo�i�t� Identity](https://learn.microsoft.com/cs-cz/aspnet/core/security/authentication/identity-custom-storage-providers)  
- **N�vody**:  
  - [Implementace UserStore pro MongoDB](https://www.mongodb.com/developer/languages/csharp/csharp-identity-storage-provider/)  
  - [Vlastn� �lo�i�t� s Dapper](https://code-maze.com/asp-net-core-identity-custom-storage-provider/)  

---

**Shrnut�**: Vlastn� poskytovatel� �lo�i�t� umo��uj� pln� nahradit v�choz� EF Core implementaci ASP.NET Core Identity. Kl��ov� je implementovat pot�ebn� rozhran� (nap�. `IUserStore`, `IRoleStore`) a zajistit bezpe�nostn� standardy. Tento p��stup je vhodn� pro integraci s nestandardn�mi datab�zemi nebo extern�mi syst�my.
