
### Vztahy v Entity Framework Core

---

#### **1. Typy vztahů**  

| **Typ** | **Popis**                                  | **Příklad**                          |  
|---------|--------------------------------------------|--------------------------------------|  
| **1:1** | Každý záznam v entitě A odkazuje na **právě jeden** záznam v entitě B. | Uživatel ↔ Profil uživatele |  
| **1:N** | Záznam v entitě A odkazuje na **více záznamů** v entitě B. | Blog ↔ Příspěvky |  
| **M:N** | Záznamy v entitě A a B se vzájemně propojují **přes spojovací tabulku**. | Studenti ↔ Kurzy |  

---

#### **2. 1:N (Jeden k mnoha)**  

**Příklad**: *Blog* má mnoho *Příspěvků*.  

**Entita Blog**:  
```csharp  
public class Blog  
{  
    public int Id { get; set; }  
    public string Nazev { get; set; }  
    public List<Prispevek> Prispevky { get; set; } // Navigační vlastnost  
}  
```  

**Entita Příspěvek**:  
```csharp  
public class Prispevek  
{  
    public int Id { get; set; }  
    public string Obsah { get; set; }  
    public int BlogId { get; set; } // Cizí klíč  
    public Blog Blog { get; set; } // Navigační vlastnost  
}  
```  

**Konfigurace (Fluent API)**:  
```csharp  
modelBuilder.Entity<Blog>()  
    .HasMany(b => b.Prispevky)  
    .WithOne(p => p.Blog)  
    .HasForeignKey(p => p.BlogId);  
```  

---

#### **3. M:N (Mnoho k mnoha)**  

**Příklad**: *Student* se zapisuje do mnoha *Kurzů*.  

**Entita Student a Kurz (EF Core 5+ bez spojovací tabulky)**:  
```csharp  
public class Student  
{  
    public int Id { get; set; }  
    public List<Kurz> Kurzy { get; set; } // Navigační vlastnost  
}  

public class Kurz  
{  
    public int Id { get; set; }  
    public List<Student> Studenti { get; set; } // Navigační vlastnost  
}  
```  

**Konfigurace (Fluent API)**:  
```csharp  
modelBuilder.Entity<Student>()  
    .HasMany(s => s.Kurzy)  
    .WithMany(k => k.Studenti);  
```  

**Se spojovací entitou (pro pokročilé scénáře)**:  
```csharp  
public class Zapis  
{  
    public int StudentId { get; set; }  
    public int KurzId { get; set; }  
    public Student Student { get; set; }  
    public Kurz Kurz { get; set; }  
}  
```  

---

#### **4. 1:1 (Jeden k jednomu)**  

**Příklad**: *Uživatel* má jeden *Profil*.  

**Entita Uživatel**:  
```csharp  
public class Uzivatel  
{  
    public int Id { get; set; }  
    public Profil Profil { get; set; } // Navigační vlastnost  
}  
```  

**Entita Profil**:  
```csharp  
public class Profil  
{  
    public int Id { get; set; }  
    public int UzivatelId { get; set; } // Cizí klíč  
    public Uzivatel Uzivatel { get; set; } // Navigační vlastnost  
}  
```  

**Konfigurace (Fluent API)**:  
```csharp  
modelBuilder.Entity<Uzivatel>()  
    .HasOne(u => u.Profil)  
    .WithOne(p => p.Uzivatel)  
    .HasForeignKey<Profil>(p => p.UzivatelId);  
```  

---

#### **5. Konfigurace pomocí Data Annotations**  

- **Cizí klíč**:  
  ```csharp  
  [ForeignKey("BlogId")]  
  public Blog Blog { get; set; }  
  ```  
- **Vztah 1:1**:  
  ```csharp  
  [Key, ForeignKey("Uzivatel")]  
  public int UzivatelId { get; set; }  
  ```  

---

#### **6. Běžné chyby a řešení**  

| **Chyba**                          | **Příčina**                     | **Řešení**                         |  
|------------------------------------|----------------------------------|-------------------------------------|  
| `Invalid column name 'BlogId'`     | Chybějící cizí klíč nebo konfigurace. | Definujte cizí klíč v entitě nebo Fluent API. |  
| `Cycle detected`                   | Rekurzivní vztah (např. Blog ↔ Příspěvek ↔ Blog). | Použijte `OnDelete(DeleteBehavior.ClientSetNull)`. |  
| Neuložené změny v M:N vztahu       | Spojovací data nejsou automaticky sledována. | Při M:N bez join entity použijte explicitní operace. |  

---

#### **7. Doporučené postupy**  

- **Fluent API**: Pro komplexní konfiguraci (např. složené klíče).  
- **Explicitní cizí klíče**: Vždy definujte `[NázevEntita]Id` pro přehlednost.  
- **Kaskádové mazání**: Nastavte pomocí `OnDelete()` (např. `.OnDelete(DeleteBehavior.Cascade)`).  
- **Migrace**: Po změně vztahu spusťte `dotnet ef migrations add`.  

---

**Shrnutí**:  
- **1:N**: Nejčastější vztah, cizí klíč v podřízené entitě.  
- **M:N**: Od EF Core 5+ bez nutnosti join entity, ale lze ji použít pro pokročilé scénáře.  
- **1:1**: Vyžaduje explicitní konfiguraci cizího klíče.  

**Zdroje**:  
- [EF Core Dokumentace – Vztahy](https://learn.microsoft.com/en-us/ef/core/modeling/relationships)  
- [EF Core Many-to-Many](https://learn.microsoft.com/en-us/ef/core/modeling/relationships/many-to-many)
