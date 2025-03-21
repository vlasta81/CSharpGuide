
## **Oddíly (sekce) a jejich interakce v ASP.NET Core Blazor**  

Oddíly (sekce) v Blazor umožňují definovat **dynamické oblasti v layoutu**, které mohou být vyplněny obsahem z jednotlivých stránek. Slouží k vytváření flexibilních rozhraní s opakujícími se prvky (např. postranní panely, záhlaví, patičky).

---

### **1. Základní koncept**  

- **Oddíl (sekce)**:  
  - Pojmenovaná oblast v layoutu, kam lze vkládat obsah z konkrétní stránky.  
  - Definuje se pomocí `RenderFragment` v layout komponentě.  
  - **Příklad**: Layout s oddílem pro postranní panel a záhlaví.  

---

### **2. Vytvoření oddílu v layoutu**  

1. **Definujte parametr typu `RenderFragment`** v layout komponentě:  
   ```razor  
   @inherits LayoutComponentBase  

   <div class="main-layout">  
       <header>  
           @HeaderSection <!-- Oddíl pro záhlaví -->  
       </header>  
       <main>  
           @Body <!-- Hlavní obsah stránky -->  
       </main>  
       <aside>  
           @SidebarSection <!-- Oddíl pro postranní panel -->  
       </aside>  
   </div>  

   @code {  
       [Parameter]  
       public RenderFragment? HeaderSection { get; set; }  

       [Parameter]  
       public RenderFragment? SidebarSection { get; set; }  
   }  
   ```  

2. **Použijte layout ve stránce** a vyplňte oddíly:  
   ```razor  
   @page "/moje-stranka"  
   @layout MujLayout  

   <PageTitle>Moje stránka</PageTitle>  

   <!-- Definice obsahu pro oddíly -->  
   <HeaderSection>  
       <h1>Vlastní záhlaví</h1>  
   </HeaderSection>  

   <SidebarSection>  
       <ul>  
           <li>Menu 1</li>  
           <li>Menu 2</li>  
       </ul>  
   </SidebarSection>  

   <!-- Hlavní obsah -->  
   <p>Obsah stránky...</p>  
   ```  

---

### **3. Interakce mezi oddíly a stránkami**  

- **Předávání dat**:  
  - Oddíly mohou komunikovat se stránkou pomocí **parametrů** nebo **stavových služeb**.  
  - **Příklad**: Tlačítko v postranním panelu aktualizuje data v hlavním obsahu.  

#### **a) Komunikace přes parametry**  

```razor  
<!-- Layout komponenta -->  
<aside>  
    @SidebarSection(sidebarData) <!-- Předání dat do oddílu -->  
</aside>  

@code {  
    private string sidebarData = "Data z layoutu";  
}  
```  

```razor  
<!-- Stránka -->  
<SidebarSection Context="data">  
    <button @onclick="() => AktualizujObsah(data)">Klikni</button>  
</SidebarSection>  

@code {  
    private void AktualizujObsah(string data)  
    {  
        // Logika aktualizace  
    }  
}  
```  

#### **b) Komunikace přes stavovou službu**  

1. Vytvořte službu pro správu stavu:  
   ```csharp  
   public class AppState  
   {  
       public event Action? OnChange;  
       public string? SharedData { get; set; }  

       public void UpdateData(string data)  
       {  
           SharedData = data;  
           OnChange?.Invoke();  
       }  
   }  
   ```  
2. Injektujte službu do layoutu a stránky:  
   ```razor  
   <!-- Oddíl v layoutu -->  
   <aside>  
       <p>@appState.SharedData</p>  
   </aside>  
   ```  
   ```razor  
   <!-- Stránka -->  
   <button @onclick="() => appState.UpdateData("Ahoj!")">Aktualizuj</button>  
   ```  

---

### **4. Dynamické oddíly**  

- **Změníte obsah oddílu za běhu** pomocí podmíněného renderování:  
  ```razor  
  @if (showAdvancedMenu)  
  {  
      <SidebarSection>  
          <!-- Pokročilé menu -->  
      </SidebarSection>  
  }  
  else  
  {  
      <SidebarSection>  
          <!-- Základní menu -->  
      </SidebarSection>  
  }  
  ```  

---

### **5. Časté problémy**  

| **Problém**                          | **Řešení**                                 |  
|--------------------------------------|--------------------------------------------|  
| Oddíl se nevykresluje                | Zkontrolujte název parametru v layoutu a stránce. |  
| Neočekávané chování při aktualizaci  | Použijte `StateHasChanged()` pro vynucení překreslení. |  
| Kolize názvů oddílů                  | Používejte unikátní názvy (např. `FooterSection`, `AdminSidebar`). |  

---

### **6. Best Practices** 

- **Izolujte logiku oddílů**: Každý oddíl by měl být samostatná komponenta.  
- **Používejte popisné názvy**: Např. `MainFooter` místo `Section1`.  
- **Minimalizujte závislosti**: Oddíly by neměly přímo manipulovat s daty mimo svůj scope.  

---

### **Shrnutí**  

| **Funkce**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Definice oddílu**       | Parametr `RenderFragment` v layoutu.                                      |  
| **Komunikace**            | Parametry, stavové služby, nebo události.                                 |  
| **Dynamické oddíly**      | Podmíněné renderování (`@if`).                                            |  
| **Použití**               | Opakující se UI prvky s variabilním obsahem (menu, záhlaví, patičky).    |  

**Klíčové výhody**:  
- **Flexibilita**: Oddíly umožňují přizpůsobit layout bez změny jeho základní struktury.  
- **Znovupoužitelnost**: Stejný layout lze použít pro různé stránky s odlišnými oddíly.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Layouts and Sections](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/layouts).  
- Ukázky: [Blazor Section Interaction Samples](https://github.com/dotnet/blazor-samples).
