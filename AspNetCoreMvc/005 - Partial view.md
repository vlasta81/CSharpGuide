
## **Pøehled Partial View (èásteèných zobrazení) v ASP.NET Core MVC**  

Role, použití a implementace èásteèných zobrazení.

---

### **1. Co je Partial View?**  

- **Èásteèné zobrazení** – fragment UI, který lze opakovanì použít v rùzných èástech aplikace.  
- Umožòuje **modularizaci kódu** (napø. formuláøe, menu, komentáøe).  
- Soubory mají pøíponu **`.cshtml`** a zaèínají podtržítkem (konvence: `_Nazev.cshtml`).  

---

### **2. Kdy použít Partial View?**  

- **Opakující se komponenty**: Hlavièka, patièka, sidebar.  
- **Dynamické èásti stránky**: Formuláøe, tabulky s daty.  
- **Složitìjší UI**: Rozdìlení rozsáhlých pohledù na menší èásti.  

---

### **3. Vytvoøení Partial View**  

- Umístìní:  
  - Ve složce **`Shared`** (pro globální použití).  
  - V podsložce konkrétního kontroleru (napø. `Views/Home/_Komentare.cshtml`).  
- Pøíklad:  
  ```html
  <!-- _KontaktniFormular.cshtml -->
  <div class="form-container">
      <form asp-action="Odeslat">
          <input asp-for="Email" placeholder="Zadejte email" />
          <button type="submit">Odeslat</button>
      </form>
  </div>
  ```

---

### **4. Vložení Partial View do hlavního pohledu**  

#### **a) Pomocí HTML Helperu**  

- **Synchronní**:  
  ```csharp
  @Html.Partial("_KontaktniFormular")
  ```  
- **Asynchronní** (doporuèeno pro výkon):  
  ```csharp
  @await Html.PartialAsync("_KontaktniFormular")
  ```  

#### **b) Pomocí Tag Helperu**  

```html
<partial name="_KontaktniFormular" />
```

#### **c) Pomocí `Html.RenderPartial`**  

- Vykreslí obsah pøímo do výstupu (používá se v kódových blocích):  
  ```csharp
  @{
      Html.RenderPartial("_KontaktniFormular");
  }
  ```

---

### **5. Pøedávání dat do Partial View**  

1. **Implicitní pøedání modelu hlavního pohledu**:  
   ```html
   <partial name="_DetailyProduktu" model="Model.Produkt" />
   ```  
2. **Explicitní pøedání vlastního modelu**:  
   ```csharp
   @await Html.PartialAsync("_DetailyProduktu", new Produkt { Nazev = "Telefon" })
   ```  
3. **Použití `ViewData`**:  
   ```csharp
   @{
       ViewData["Nadpis"] = "Kontakt";
   }
   <partial name="_Header" view-data="ViewData" />
   ```  

---

### **6. Výhody a nevýhody**  

| **Výhody**                                  | **Nevýhody**                          |  
|---------------------------------------------|----------------------------------------|  
| Snížení duplicity kódu                      | Omezená logika (nemá vlastní akce)     |  
| Zlepšení èitelnosti a údržby                | Není vhodné pro složité komponenty     |  
| Snadná integrace do rùzných èástí aplikace  |                                       |  

---

### **7. Rozdíl mezi Partial View a View Component**  

- **Partial View**:  
  - Pouze pro **statické nebo jednoduché dynamické prvky**.  
  - Nemá vlastní logiku – data musí být pøedána z hlavního pohledu.  
- **View Component**:  
  - **Složitìjší komponenty** s vlastní logikou (napø. košík, dynamické menu).  
  - Má samostatnou tøídu a metodu `InvokeAsync`.  

---

### **8. Pøíklady použití**  

#### **Partial View pro komentáøe**  

```html
<!-- _Komentare.cshtml -->
@model List<Komentar>
<div class="comments-section">
    @foreach (var komentar in Model)
    {
        <div class="comment">
            <h4>@komentar.Autor</h4>
            <p>@komentar.Text</p>
        </div>
    }
</div>
```

#### **Vložení do hlavního pohledu**  

```html
@model Clanek
<article>
    <h1>@Model.Nadpis</h1>
    <p>@Model.Obsah</p>
    <partial name="_Komentare" model="Model.Komentare" />
</article>
```

---

### **9. Doporuèené postupy**  

- **Pojmenovávejte konzistentnì**: Zaèínejte podtržítkem (napø. `_Formular.cshtml`).  
- **Nepoužívejte obchodní logiku**: Delegate ji na kontroler nebo služby.  
- **Vyhnìte se vnoøování pøíliš mnoha partial views**: Mùže zpomalit rendering.  
- **Cacheování**: Použijte `<cache>` pro statické èásti.  

---

### **10. Shrnutí**  

- Partial View slouží k **opakovanému použití UI komponent**.  
- Data lze pøedávat **implicitnì, explicitnì nebo pøes `ViewData`**.  
- Vhodné pro **jednoduché prvky** (formuláøe, seznamy).  
- Pro komplexní komponenty s logikou použijte **View Component**.  

--- 

**Doporuèené zdroje**:  
- Oficiální dokumentace: *Partial views in ASP.NET Core*  
