
### Předvyplnění dat (Seeding) v Entity Framework Core

---

#### **1. Co je předvyplnění dat?**  

- **Definice**: Proces **inicializace databáze výchozími daty** při vytváření nebo migraci.  
- **Účel**:  
  - Vytvořit základní testovací data pro vývoj.  
  - Naplnit referenční tabulky (např. seznam zemí, rolí uživatelů).  
  - Zajistit konzistenci v produkčním prostředí (např. výchozí administrátorský účet).  

---

#### **2. Metody předvyplnění dat**  

EF Core podporuje dva hlavní přístupy:  

1. **Model Seed Data (Statické předvyplnění)**:  
   - Data jsou definována přímo v konfiguraci modelu (Fluent API).  
   - Jsou součástí migrací.  
   - **Vhodné pro**: Neměnná referenční data (např. seznam států).  

2. **Custom Seeding (Dynamické předvyplnění)**:  
   - Data jsou vložena pomocí kódu (např. při spuštění aplikace).  
   - **Vhodné pro**: Data závislá na prostředí (testovací vs. produkční) nebo dynamické inicializace.  

---

#### **3. Model Seed Data (Fluent API)**  

**Příklad**: Předvyplnění tabulky `Produkt` při migraci.  
```csharp  
protected override void OnModelCreating(ModelBuilder modelBuilder)  
{  
    modelBuilder.Entity<Produkt>().HasData(  
        new Produkt { Id = 1, Nazev = "Notebook", Cena = 25000 },  
        new Produkt { Id = 2, Nazev = "Mobil", Cena = 12000 }  
    );  
}  
```  

**Klíčové body**:  
- **Primární klíče**: Musí být explicitně nastaveny (i když jsou generovány automaticky).  
- **Migrace**: Při spuštění `dotnet ef migrations add` se data zahrnou do migračního skriptu.  

---

#### **4. Custom Seeding (Dynamický přístup)** 

**Příklad**: Vložení dat při spuštění aplikace.  
```csharp  
public static class DbInitializer  
{  
    public static void Initialize(AppDbContext context)  
    {  
        context.Database.EnsureCreated(); // Vytvoří databázi, pokud neexistuje  

        if (!context.Produkty.Any())  
        {  
            context.Produkty.AddRange(  
                new Produkt { Nazev = "Tablet", Cena = 8000 },  
                new Produkt { Nazev = "Monitor", Cena = 5000 }  
            );  
            context.SaveChanges();  
        }  
    }  
}  
```  

**Použití v `Program.cs`**:  
```csharp  
using (var scope = app.Services.CreateScope())  
{  
    var dbContext = scope.ServiceProvider.GetRequiredService<AppDbContext>();  
    DbInitializer.Initialize(dbContext);  
}  
```  

---

#### **5. Předvyplnění vztahů (1:N, M:N)**  

- **1:N**: Při seedování nadřízené entity musíte nastavit cizí klíče u podřízených entit.  
```csharp  
modelBuilder.Entity<Autor>().HasData(  
    new Autor { Id = 1, Jmeno = "Jan Novak" }  
);  

modelBuilder.Entity<Kniha>().HasData(  
    new Kniha { Id = 1, Nazev = "EF Core Guide", AutorId = 1 }  
);  
```  

- **M:N**: Vyžaduje seedování spojovací tabulky (pokud není použito v EF Core 5+ bez join entity).  
```csharp  
modelBuilder.Entity<StudentKurz>().HasData(  
    new StudentKurz { StudentId = 1, KurzId = 1 }  
);  
```  

---

#### **6. Omezení a doporučení**  

| **Situace**                     | **Řešení**                                  |  
|---------------------------------|---------------------------------------------|  
| **Duplicitní data**             | Před vložením ověřte existenci (`Any()`).   |  
| **Změny v seed data**           | Upravte migraci nebo vytvořte novou.        |  
| **Citlivá data (hesla)**        | Použijte šifrování nebo proměnné prostředí. |  

---

#### **7. Časté chyby**  

- **Chybějící primární klíče**: Při použití `HasData` musí být PK explicitně nastaveny.  
- **Nesprávné pořadí entit**: Seedujte nadřízené entity před podřízenými.  
- **Použití `EnsureCreated` s migracemi**: `EnsureCreated` ignoruje migrace – používejte `Migrate()`.  

---

#### **8. Best Practices**  

1. **Model Seed Data**: Pro statická referenční data (země, role).  
2. **Custom Seeding**: Pro data závislá na prostředí nebo testovací účely.  
3. **Testování**: Ověřte, zda seedování nezpůsobuje duplicity.  

---

**Shrnutí**:  
- **Model Seed Data**: Integrováno do migrací, vhodné pro neměnná data.  
- **Custom Seeding**: Flexibilní, ale vyžaduje explicitní správu.  
- **Migrace**: Při změnách v seed datech vytvořte novou migraci.  

**Zdroje**:  
- [EF Core Dokumentace – Seed Data](https://learn.microsoft.com/en-us/ef/core/modeling/data-seeding)  
- [EF Core Migrations](https://learn.microsoft.com/en-us/ef/core/managing-schemas/migrations/)
