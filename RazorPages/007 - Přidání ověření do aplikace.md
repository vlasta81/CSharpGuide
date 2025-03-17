
### Přidání ověření v Razor Pages ASP.NET Core

---

#### **1. Úvod**  

- **Proč ověřovat data?**  
  Ověření (validace) zajišťuje, že uživatelské vstupy splňují očekávaná pravidla (např. povinné pole, správný formát e-mailu, rozsah hodnot).  
  - **Typy ověření**:  
    - **Serverové**: Zpracovává se na serveru (v `PageModel`).  
    - **Klientské**: Validace v prohlížeči pomocí JavaScriptu (rychlejší feedback pro uživatele).  
  - **Integrace s Razor Pages**: Automatická validace pomocí atributů a Tag Helpers.

---

#### **2. Základní ověření pomocí DataAnnotations**  

- **Atributy z `System.ComponentModel.DataAnnotations`**:  
  - `[Required]`: Pole je povinné.  
  - `[StringLength(max)]`: Maximální délka řetězce.  
  - `[Range(min, max)]`: Číslo musí být v rozsahu.  
  - `[EmailAddress]`: Validace formátu e-mailu.  
  - `[Compare("Property")]`: Shoda s jinou vlastností (např. heslo a potvrzení hesla).  

- **Příklad v modelu**:  
  ```csharp
  public class UserInput
  {
      [Required(ErrorMessage = "Jméno je povinné!")]
      [StringLength(50, ErrorMessage = "Maximálně 50 znaků.")]
      public string Name { get; set; }

      [EmailAddress(ErrorMessage = "Neplatný formát e-mailu.")]
      public string Email { get; set; }

      [Range(18, 99, ErrorMessage = "Věk musí být mezi 18 a 99.")]
      public int Age { get; set; }
  }
  ```

---

#### **3. Validace v PageModel**  

- **Kontrola `ModelState.IsValid`**:  
  V metodě `OnPost` ověřte platnost dat před uložením.  
  ```csharp
  public IActionResult OnPost()
  {
      if (!ModelState.IsValid)
          return Page(); // Zobrazí chyby

      // Uložení dat
      return RedirectToPage("/Success");
  }
  ```

- **Vlastní validační logika**:  
  ```csharp
  if (UserInput.Age < 18)
      ModelState.AddModelError("UserInput.Age", "Uživatel musí být dospělý.");
  ```

---

#### **4. Zobrazení chyb ve View**  

- **Tag Helper `asp-validation-for`**:  
  Zobrazí chybovou zprávu pro konkrétní vlastnost.  
  ```html
  <input asp-for="UserInput.Name" />
  <span asp-validation-for="UserInput.Name" class="text-danger"></span>
  ```

- **Shrnutí chyb**:  
  ```html
  <div asp-validation-summary="All" class="text-danger"></div>
  ```
  - **Parametry**:  
    - `All`: Zobrazí všechny chyby.  
    - `ModelOnly`: Chyby bez vlastností (např. globální chyby).  

---

#### **5. Klientské ověření**  

- **Automatická integrace**:  
  - Razor Pages generuje HTML atributy pro klientskou validaci (např. `data-val-required`, `data-val-length`).  
  - Vyžaduje se připojení knihoven:  
    ```html
    <script src="~/lib/jquery/dist/jquery.min.js"></script>
    <script src="~/lib/jquery-validation/dist/jquery.validate.min.js"></script>
    <script src="~/lib/jquery-validation-unobtrusive/jquery.validate.unobtrusive.min.js"></script>
    ```

- **Vypnutí klientské validace**:  
  ```html
  <form method="post" asp-antiforgery="true" data-val="false">
  ```

---

#### **6. Vlastní validační atributy**  

- **Vytvoření vlastního atributu**:  
  ```csharp
  public class CzechPhoneAttribute : ValidationAttribute
  {
      protected override ValidationResult IsValid(object value, ValidationContext context)
      {
          if (value == null || !Regex.IsMatch(value.ToString(), @"^\+420\d{9}$"))
              return new ValidationResult("Neplatné české číslo (+420XXXXXXXXX).");
          return ValidationResult.Success;
      }
  }
  ```

- **Použití**:  
  ```csharp
  [CzechPhone]
  public string Phone { get; set; }
  ```

---

#### **7. Doporučené postupy**  

- **Vždy validujte na serveru**: Klientská validace není spolehlivá (lze obejít).  
- **Používejte jednotné chybové zprávy**: Uživatelsky přívětivé a konzistentní.  
- **Testujte hraniční případy**: Např. prázdné řetězce, extrémní hodnoty.  
- **Oddělujte vstupní modely od doménových modelů**: Zamezte přímému mapování na entity (např. `UserInput` vs. `User`).  

---

#### **8. Časté chyby**  

- **Chybějící `[BindProperty]`**: Vlastnosti se nenačtou z formuláře.  
- **Zapomenuté připojení validačních skriptů**: Klientská validace nefunguje.  
- **Neošetřené výjimky při validaci**: Např. přetypování neplatného čísla.  

---

#### **9. Shrnutí**  

- **DataAnnotations**: Hlavní nástroj pro definici pravidel.  
- **ModelState.IsValid**: Klíčová metoda pro kontrolu validity.  
- **Klientská validace**: Vylepšuje UX, ale nenahrazuje serverovou.  
- **Vlastní atributy**: Pro specifické požadavky.  

---

#### **10. Užitečné zdroje**  

- Dokumentace: [Validace v ASP.NET Core](https://learn.microsoft.com/cs-cz/aspnet/core/mvc/models/validation)  
- Tutoriál: [Formuláře a validace v Razor Pages](https://learn.microsoft.com/cs-cz/aspnet/core/tutorials/razor-pages/validation)
