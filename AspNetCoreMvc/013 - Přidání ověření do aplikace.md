
## **Přidání ověření do aplikace ASP.NET Core MVC**  

Implementace validace uživatelských vstupů na straně klienta i serveru.

---

### **1. Úvod do ověření (validace)**  

- **Cíl**: Zajistit, aby uživatelská data splňovala požadavky aplikace (např. povinná pole, formát emailu).  
- **Typy validace**:  
  - **Klientovská (client-side)**: Rychlá okamžitá zpětná vazba (JavaScript).  
  - **Serverová (server-side)**: Zajišťuje bezpečnost a integritu dat.  

---

### **2. Validace pomocí Data Annotations**  

- **Atributy** z knihovny `System.ComponentModel.DataAnnotations` pro definování pravidel přímo v modelu.  

#### **Běžné atributy**:  

| **Atribut**           | **Popis**                                  | **Příklad**                          |  
|------------------------|--------------------------------------------|---------------------------------------|  
| `[Required]`          | Pole je povinné.                          | `[Required(ErrorMessage = "Jméno je povinné")]` |  
| `[StringLength]`       | Omezí délku řetězce.                      | `[StringLength(50, MinimumLength = 3)]` |  
| `[Range]`             | Omezí číselný rozsah.                     | `[Range(1, 100)]`                    |  
| `[EmailAddress]`      | Kontroluje formát emailu.                 | `[EmailAddress]`                     |  
| `[Compare]`           | Porovná dvě pole (např. heslo).           | `[Compare("Heslo")]`                 |  

#### **Příklad modelu**:  

```csharp  
public class Uzivatel  
{  
    [Required(ErrorMessage = "Jméno je povinné!")]  
    [StringLength(50)]  
    public string Jmeno { get; set; }  

    [EmailAddress(ErrorMessage = "Neplatný formát emailu.")]  
    public string Email { get; set; }  

    [Range(18, 100, ErrorMessage = "Věk musí být mezi 18 a 100.")]  
    public int Vek { get; set; }  
}  
```  

---

### **3. Serverová validace**  

- **Kontrola v akci kontroleru**:  
  ```csharp  
  [HttpPost]  
  public IActionResult Vytvor(Uzivatel uzivatel)  
  {  
      if (ModelState.IsValid)  
      {  
          // Uložení dat  
          return RedirectToAction("Index");  
      }  
      // Chyby – znovu zobrazí formulář s chybovými zprávami  
      return View(uzivatel);  
  }  
  ```  
- **ModelState**: Obsahuje stav validace a chybové zprávy.  

---

### **4. Klientovská validace**  

- **Automaticky generována** pomocí Data Annotations a knihoven:  
  - **jQuery Validation**  
  - **jQuery Unobtrusive Validation**  
- **Nastavení**:  
  1. Přidejte knihovny do `_Layout.cshtml`:  
     ```html  
     <script src="~/lib/jquery/dist/jquery.min.js"></script>  
     <script src="~/lib/jquery-validation/dist/jquery.validate.min.js"></script>  
     <script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.min.js"></script>  
     ```  
  2. Ujistěte se, že formulář obsahuje správné atributy (např. `asp-validation-for`).  

---

### **5. Zobrazení chybových zpráv ve View**  

- **Pro konkrétní pole**:  
  ```html  
  <input asp-for="Jmeno" class="form-control" />  
  <span asp-validation-for="Jmeno" class="text-danger"></span>  
  ```  
- **Souhrn chyb**:  
  ```html  
  <div asp-validation-summary="All" class="text-danger"></div>  
  ```  
  - **Možnosti**: `All` (všechny chyby), `ModelOnly` (jen chyby modelu).  

---

### **6. Vlastní validace**  

#### **a) Vlastní validátor pomocí atributu**  

```csharp  
public class PlatnyRokAttribute : ValidationAttribute  
{  
    protected override ValidationResult IsValid(object value, ValidationContext context)  
    {  
        if (value is int rok && rok >= 2000 && rok <= DateTime.Now.Year)  
        {  
            return ValidationResult.Success;  
        }  
        return new ValidationResult("Neplatný rok!");  
    }  
}  

// Použití v modelu  
[PlatnyRok]  
public int RokNarozeni { get; set; }  
```  

#### **b) Remote validace (validace přes AJAX)**  

```csharp  
[AcceptVerbs("GET", "POST")]  
public IActionResult OveritEmail(string email)  
{  
    if (_service.EmailExistuje(email))  
    {  
        return Json("Email již existuje.");  
    }  
    return Json(true);  
}  

// V modelu  
[Remote(action: "OveritEmail", controller: "Uzivatele")]  
public string Email { get; set; }  
```  

---

### **7. Best Practices**  

- **Vždy kombinujte klientovskou a serverovou validaci**.  
- **Nepovolujte HTML v uživatelských vstupech** (prevence XSS útoků).  
- **Používejte explicitní zpracování chyb** v kontrolerech.  
- **Testujte hraniční případy** (např. minimální a maximální hodnoty).  

---

### **8. Příklady**  

#### **Formulář s validací**  

```html  
<form asp-action="Vytvor" method="post">  
    <div class="form-group">  
        <label asp-for="Jmeno"></label>  
        <input asp-for="Jmeno" class="form-control" />  
        <span asp-validation-for="Jmeno" class="text-danger"></span>  
    </div>  

    <div class="form-group">  
        <label asp-for="Email"></label>  
        <input asp-for="Email" class="form-control" />  
        <span asp-validation-for="Email" class="text-danger"></span>  
    </div>  

    <button type="submit" class="btn btn-primary">Uložit</button>  
</form>  
```  

---

### **9. Shrnutí**  

- **Data Annotations** zjednodušují definici pravidel přímo v modelu.  
- **Klientovská validace** poskytuje rychlou zpětnou vazbu uživateli.  
- **Serverová validace** je nezbytná pro bezpečnost.  
- **Vlastní validátory** umožňují specifická pravidla.  
- **Remote validace** řeší závislosti na externích zdrojích (např. kontrola unikátnosti emailu).  

---

**Doporučené zdroje**:  
- Oficiální dokumentace: *Model Validation in ASP.NET Core MVC*  
