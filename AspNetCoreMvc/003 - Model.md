
## **Přehled Modelu v ASP.NET Core MVC – Studijní materiál**  

Základní role, struktura a funkce modelu v architektuře MVC.

---

### **1. Co je Model?**  

- **Datová a obchodní vrstva** aplikace.  
- Reprezentuje **data, pravidla a logiku** (např. validace, výpočty, interakce s databází).  
- **Nezávislý na UI a řídicí logice** – dodržuje princip separace zájmů (SoC).

---

### **2. Role Modelu**  

- **Správa dat**: Definice struktury dat (např. třída `Uzivatel` s vlastnostmi `Jmeno`, `Email`).  
- **Obchodní logika**: Metody pro zpracování dat (např. výpočet ceny objednávky).  
- **Validace**: Ověřování správnosti dat pomocí atributů (např. `[Required]`).  
- **Komunikace s databází**: Přes ORM (Entity Framework Core) nebo služby.  

---

### **3. Typy Modelů**  

1. **Domain Model (Doménový model)**  
   - Odpovídá **struktuře databázových entit** (např. tabulka `Produkty` → třída `Produkt`).  
2. **View Model (Model pro pohled)**  
   - **Přizpůsobuje data pro zobrazení ve View** (např. kombinace dat z více entit).  
   - Chrání před přímým vystavením doménového modelu.  
3. **Data Transfer Object (DTO)**  
   - Slouží k přenosu dat mezi vrstvami (např. API a klient).  

---

### **4. Validační atributy (Data Annotations)**  

- Atributy pro definování pravidel validace:  
  ```csharp  
  public class Uzivatel  
  {  
      [Required(ErrorMessage = "Jméno je povinné!")]  
      [StringLength(50)]  
      public string Jmeno { get; set; }  

      [EmailAddress]  
      public string Email { get; set; }  

      [Range(18, 100, ErrorMessage = "Věk musí být mezi 18 a 100.")]  
      public int Vek { get; set; }  
  }  
  ```  
- Automaticky se aplikují při **model bindingu** (např. odeslání formuláře).  

---

### **5. Práce s daty a Entity Framework Core**  

- **ORM nástroj** pro mapování objektů na databázové tabulky.  
- **DbSet<T>**: Reprezentuje kolekci entit v kontextu databáze.  
- Příklad konfigurace:  
  ```csharp  
  public class AppDbContext : DbContext  
  {  
      public DbSet<Uzivatel> Uzivatele { get; set; }  
  }  
  ```  

---

### **6. Model Binding**  

- Automatické mapování dat z HTTP požadavku (formuláře, URL, tělo) na vlastnosti modelu.  
- V kontroleru:  
  ```csharp  
  [HttpPost]  
  public IActionResult VytvorUzivatele(Uzivatel uzivatel)  
  {  
      if (ModelState.IsValid)  
      {  
          // Uložení do databáze  
      }  
      return View(uzivatel);  
  }  
  ```  

---

### **7. Příklady kódu**  

#### **Domain Model**  

```csharp  
public class Produkt  
{  
    public int Id { get; set; }  
    public string Nazev { get; set; }  
    public decimal Cena { get; set; }  
}  
```  

#### **View Model**  

```csharp  
public class ProduktDetailViewModel  
{  
    public string Nazev { get; set; }  
    public decimal CenaSDph { get; set; } // Přidáno pro potřeby view  
}  
```  

#### **Použití v View**  

```html  
@model ProduktDetailViewModel  
<h1>@Model.Nazev</h1>  
<p>Cena s DPH: @Model.CenaSDph Kč</p>  
```  

---

### **8. Doporučené postupy**  

- **Tenké modely**: Logiku delegujte na služby nebo manažery.  
- **Separace vrstev**: Doménový model ≠ View Model (kvůli bezpečnosti a flexibilitě).  
- **Asynchronní operace**: Používejte `async`/`await` pro práci s databází.  
- **Validace na obou stranách**: Klient (JavaScript) + server (ModelState).  

---

### **9. Časté chyby**  

- **Přímé vystavení doménového modelu ve View**: Riziko úniku citlivých dat.  
- **Příliš složité modely**: Rozdělujte logiku do menších tříd.  
- **Ignorování validace**: Vždy ověřujte vstupy!  

---

### **10. Shrnutí**  

- Model je **jádrem dat a obchodní logiky** aplikace.  
- **View Modely** chrání a přizpůsobují data pro UI.  
- **Data Annotations** zjednodušují validaci.  
- EF Core usnadňuje práci s databází.  
- Dodržování best practices zvyšuje **udržovatelnost a bezpečnost**.  
