
## **Formuláře v Blazor ASP.NET**

---

### **1. Vstupní komponenty**  

- **Vestavěné komponenty**:  
  - `InputText`: Pro textové vstupy.  
  - `InputNumber`: Pro číselné hodnoty.  
  - `InputCheckbox`: Pro logické hodnoty (ano/ne).  
  - `InputSelect`: Rozbalovací seznam (dropdown).  
  - `InputDate`: Výběr data.  
  - `InputTextArea`: Víceřádkový text.  
- **Použití**:  
  ```razor  
  <EditForm Model="@model">  
      <InputText @bind-Value="model.Jmeno" />  
      <InputNumber @bind-Value="model.Vek" />  
  </EditForm>  
  ```  

---

### **2. Datová vazba**  

- **Dvoucestná vazba**:  
  - Použití `@bind-Value` pro propojení komponenty s vlastností modelu.  
  ```razor  
  <InputText @bind-Value="model.Email" />  
  ```  
- **Model formuláře**:  
  - Definujte třídu s vlastnostmi a validačními atributy.  
  ```csharp  
  public class UzivatelModel  
  {  
      [Required(ErrorMessage = "E-mail je povinný.")]  
      public string Email { get; set; }  
  }  
  ```  

---

### **3. Ověřování (validace)**  

- **Validační atributy**:  
  - `[Required]`, `[StringLength]`, `[Range]`, `[EmailAddress]`, atd.  
- **Komponenty pro validaci**:  
  - `DataAnnotationsValidator`: Aktivuje validaci dle atributů.  
  - `ValidationSummary`: Zobrazí souhrn chyb.  
  - `ValidationMessage`: Chybová zpráva pro konkrétní pole.  
  ```razor  
  <EditForm Model="@model" OnValidSubmit="UlozData">  
      <DataAnnotationsValidator />  
      <ValidationSummary />  
      <InputText @bind-Value="model.Email" />  
      <ValidationMessage For="@(() => model.Email)" />  
      <button type="submit">Odeslat</button>  
  </EditForm>  
  ```  

---

### **4. Zpracování odeslání formuláře**  

- **Události**:  
  - `OnValidSubmit`: Spustí se při úspěšné validaci.  
  - `OnInvalidSubmit`: Spustí se při neplatných datech.  
  ```csharp  
  private void UlozData()  
  {  
      // Logika pro uložení dat  
  }  
  ```  

---

### **5. Vlastní validace**  

- **Serverová validace**:  
  - Ověření dat na serveru a přidání chyb do `ModelState`.  
  ```csharp  
  if (!IsValidEmail(model.Email))  
  {  
      ModelState.AddModelError(nameof(model.Email), "Neplatný e-mail.");  
  }  
  ```  
- **Vlastní validační komponenty**:  
  - Implementace `ComponentBase` s logikou validace.  

---

### **6. Odstraňování potíží**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|--------------------------------------------|  
| Validace se nespustí                 | Přidejte `DataAnnotationsValidator`.       |  
| Chybové zprávy se nezobrazují        | Zkontrolujte `ValidationMessage/Summary`.  |  
| Formulář se neodesílá                | Ověřte, zda tlačítko má `type="submit"`.   |  
| Hodnoty se neaktualizují             | Zkontrolujte správnost `@bind-Value`.      |  
| Neplatná kultura pro čísla/datum     | Nastavte `CultureInfo` v `Program.cs`.     |  

---

### **7. Pokročilé techniky**  

- **EditContext**: Manuální správa stavu formuláře.  
- **FluentValidation**: Knihovna pro komplexní validační pravidla.  
- **Resetování formuláře**:  
  ```csharp  
  private void ResetForm()  
  {  
      model = new UzivatelModel();  
      editContext?.MarkAsUnmodified();  
  }  
  ```  

---

### **Shrnutí**  

| **Komponenta**           | **Účel**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **`EditForm`**           | Základní obal pro formulář s podporou validace.                          |  
| **`DataAnnotationsValidator`** | Aktivuje validaci dle atributů.                                |  
| **`ValidationMessage`**  | Zobrazí chybu pro konkrétní pole.                                        |  
| **`@bind-Value`**        | Propojení vstupních komponent s modelem.                                 |  

**Užitečné zdroje**:  
- [Dokumentace: Formuláře v Blazoru](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/forms-and-input-components)  
- [Příklady validace](https://github.com/dotnet/blazor-samples)
