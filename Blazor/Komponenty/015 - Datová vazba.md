
## **Datová vazba v aplikacích ASP.NET Core Blazor**  

Datová vazba (data binding) v Blazor propojuje UI prvky s datovými modely, což umožňuje **automatickou synchronizaci mezi uživatelským rozhraním a logikou aplikace**. Blazor podporuje jednosměrnou i obousměrnou vazbu.

---

### **1. Typy datové vazby**  

#### **a) Jednosměrná vazba (One-way binding)**  

- Zobrazuje data z modelu v UI bez možnosti zpětné aktualizace.  
- Používá se pro **statický obsah** nebo hodnoty, které nelze měnit uživatelem.  
- **Syntaxe**:  
  ```razor  
  <p>@Jmeno</p> <!-- Hodnota Jmeno se zobrazí, ale nelze upravit -->  
  ```

#### **b) Obousměrná vazba (Two-way binding)**  

- Synchronizuje změny mezi UI a modelem v obou směrech.  
- Používá se pro formuláře, inputy a interaktivní prvky.  
- **Syntaxe**:  
  ```razor  
  <input @bind="Text" />  
  @code {  
      private string Text { get; set; } = "Výchozí text";  
  }  
  ```  

---

### **2. Základní direktiva `@bind`**  

- **Princip**: Výchozí chování aktualizuje model **po opuštění prvku** (např. při stisknutí Tab).  
- **Příklad**:  
  ```razor  
  <input @bind="Hodnota" />  
  <p>Aktuální hodnota: @Hodnota</p>  

  @code {  
      private string Hodnota { get; set; }  
  }  
  ```  

---

### **3. Kontrola aktualizací pomocí `@bind:event`**  

- **Změníte, kdy se model aktualizuje** (např. při každém stisku klávesy).  
- **Syntaxe**:  
  ```razor  
  <input @bind="Hodnota" @bind:event="oninput" /> <!-- Aktualizace v reálném čase -->  
  ```  

---

### **4. Vazba na vlastní komponenty**  

- **Krok 1**: V dětské komponentě definujte parametry a `EventCallback`.  
  ```razor  
  <input @value="Hodnota" @oninput="AktualizujHodnotu" />  

  @code {  
      [Parameter]  
      public string Hodnota { get; set; }  

      [Parameter]  
      public EventCallback<string> HodnotaChanged { get; set; }  

      private async Task AktualizujHodnotu(ChangeEventArgs e)  
      {  
          await HodnotaChanged.InvokeAsync(e.Value.ToString());  
      }  
  }  
  ```  
- **Krok 2**: V rodičovské komponentě použijte `@bind`:  
  ```razor  
  <MojeKomponenta @bind-Hodnota="text" />  
  ```

---

### **5. Vazba na formuláře a validaci**  

- **Komponenta `EditForm`**:  
  - Integruje se s validačními atributy (např. `[Required]`).  
  - **Příklad**:  
    ```razor  
    <EditForm Model="@model" OnValidSubmit="UlozData">  
        <DataAnnotationsValidator />  
        <InputText @bind-Value="model.Jmeno" />  
        <ValidationMessage For="() => model.Jmeno" />  
        <button type="submit">Odeslat</button>  
    </EditForm>  
    ```  

---

### **6. Vazba na kolekce**  

- **Použití `@key`**: Zachovává stav prvků v seznamech při změnách.  
  ```razor  
  @foreach (var item in Polozky)  
  {  
      <div @key="item.Id">  
          <input @bind="item.Text" />  
      </div>  
  }  
  ```  

---

### **7. Pokročilé techniky**  

#### **a) Vazba na komplexní objekty**  

  ```razor  
  <input @bind="Uzivatel.Email" />  
  @code {  
      private Uzivatel Uzivatel { get; set; } = new();  
  }  
  ```  

#### **b) Vazba s převodem hodnot (parsing a formátování)**  

  ```razor  
  <input @bind="Cislo" @bind:format="N2" /> <!-- Formátování na 2 desetinná místa -->  
  ```  

---

### **8. Časté problémy a řešení**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|--------------------------------------------|  
| Hodnota se neaktualizuje             | Zkontrolujte `@bind:event` nebo použijte `StateHasChanged()`. |  
| Validační chyby u formulářů          | Přidejte `DataAnnotationsValidator`.       |  
| Neočekávané chování u kolekcí        | Použijte `@key` k identifikaci prvků.      |  

---

### **Shrnutí**

| **Koncept**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **`@bind`**              | Obousměrná vazba mezi UI a modelem.                                       |  
| **`@bind:event`**        | Určuje událost pro aktualizaci (např. `oninput`, `onchange`).             |  
| **Formuláře**            | Integrace s `EditForm` a validačními atributy.                            |  
| **Kolekce**              | Zachování stavu pomocí `@key`.                                            |  

**Klíčové body**:  
- Obousměrná vazba zjednodušuje práci s formuláři.  
- Pro dynamické kolekce vždy používejte `@key`.  
- Validace se integruje přes `EditForm` a `DataAnnotationsValidator`.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Data Binding](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/data-binding).  
