
## **P�ehled Partial View (��ste�n�ch zobrazen�) v ASP.NET Core MVC**  

Role, pou�it� a implementace ��ste�n�ch zobrazen�.

---

### **1. Co je Partial View?**  

- **��ste�n� zobrazen�** � fragment UI, kter� lze opakovan� pou��t v r�zn�ch ��stech aplikace.  
- Umo��uje **modularizaci k�du** (nap�. formul��e, menu, koment��e).  
- Soubory maj� p��ponu **`.cshtml`** a za��naj� podtr��tkem (konvence: `_Nazev.cshtml`).  

---

### **2. Kdy pou��t Partial View?**  

- **Opakuj�c� se komponenty**: Hlavi�ka, pati�ka, sidebar.  
- **Dynamick� ��sti str�nky**: Formul��e, tabulky s daty.  
- **Slo�it�j�� UI**: Rozd�len� rozs�hl�ch pohled� na men�� ��sti.  

---

### **3. Vytvo�en� Partial View**  

- Um�st�n�:  
  - Ve slo�ce **`Shared`** (pro glob�ln� pou�it�).  
  - V podslo�ce konkr�tn�ho kontroleru (nap�. `Views/Home/_Komentare.cshtml`).  
- P��klad:  
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

### **4. Vlo�en� Partial View do hlavn�ho pohledu**  

#### **a) Pomoc� HTML Helperu**  

- **Synchronn�**:  
  ```csharp
  @Html.Partial("_KontaktniFormular")
  ```  
- **Asynchronn�** (doporu�eno pro v�kon):  
  ```csharp
  @await Html.PartialAsync("_KontaktniFormular")
  ```  

#### **b) Pomoc� Tag Helperu**  

```html
<partial name="_KontaktniFormular" />
```

#### **c) Pomoc� `Html.RenderPartial`**  

- Vykresl� obsah p��mo do v�stupu (pou��v� se v k�dov�ch bloc�ch):  
  ```csharp
  @{
      Html.RenderPartial("_KontaktniFormular");
  }
  ```

---

### **5. P�ed�v�n� dat do Partial View**  

1. **Implicitn� p�ed�n� modelu hlavn�ho pohledu**:  
   ```html
   <partial name="_DetailyProduktu" model="Model.Produkt" />
   ```  
2. **Explicitn� p�ed�n� vlastn�ho modelu**:  
   ```csharp
   @await Html.PartialAsync("_DetailyProduktu", new Produkt { Nazev = "Telefon" })
   ```  
3. **Pou�it� `ViewData`**:  
   ```csharp
   @{
       ViewData["Nadpis"] = "Kontakt";
   }
   <partial name="_Header" view-data="ViewData" />
   ```  

---

### **6. V�hody a nev�hody**  

| **V�hody**                                  | **Nev�hody**                          |  
|---------------------------------------------|----------------------------------------|  
| Sn�en� duplicity k�du                      | Omezen� logika (nem� vlastn� akce)     |  
| Zlep�en� �itelnosti a �dr�by                | Nen� vhodn� pro slo�it� komponenty     |  
| Snadn� integrace do r�zn�ch ��st� aplikace  |                                       |  

---

### **7. Rozd�l mezi Partial View a View Component**  

- **Partial View**:  
  - Pouze pro **statick� nebo jednoduch� dynamick� prvky**.  
  - Nem� vlastn� logiku � data mus� b�t p�ed�na z hlavn�ho pohledu.  
- **View Component**:  
  - **Slo�it�j�� komponenty** s vlastn� logikou (nap�. ko��k, dynamick� menu).  
  - M� samostatnou t��du a metodu `InvokeAsync`.  

---

### **8. P��klady pou�it�**  

#### **Partial View pro koment��e**  

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

#### **Vlo�en� do hlavn�ho pohledu**  

```html
@model Clanek
<article>
    <h1>@Model.Nadpis</h1>
    <p>@Model.Obsah</p>
    <partial name="_Komentare" model="Model.Komentare" />
</article>
```

---

### **9. Doporu�en� postupy**  

- **Pojmenov�vejte konzistentn�**: Za��nejte podtr��tkem (nap�. `_Formular.cshtml`).  
- **Nepou��vejte obchodn� logiku**: Delegate ji na kontroler nebo slu�by.  
- **Vyhn�te se vno�ov�n� p��li� mnoha partial views**: M��e zpomalit rendering.  
- **Cacheov�n�**: Pou�ijte `<cache>` pro statick� ��sti.  

---

### **10. Shrnut�**  

- Partial View slou�� k **opakovan�mu pou�it� UI komponent**.  
- Data lze p�ed�vat **implicitn�, explicitn� nebo p�es `ViewData`**.  
- Vhodn� pro **jednoduch� prvky** (formul��e, seznamy).  
- Pro komplexn� komponenty s logikou pou�ijte **View Component**.  

--- 

**Doporu�en� zdroje**:  
- Ofici�ln� dokumentace: *Partial views in ASP.NET Core*  
