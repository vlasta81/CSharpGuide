
## **Komponenty šablony v ASP.NET Core Blazor**

---

### **1. Komponenty bez vizuálního vzhledu**  

- **Definice**: Komponenty, které **nerenderují žádné UI**, ale slouží k logické organizaci kódu, sdílení stavu nebo poskytování služeb jiným komponentám.  
- **Využití**:  
  - **Správa stavu**: Centralizace dat pro více komponent (např. konfigurace, uživatelská předvolení).  
  - **Logická kapsle**: Encapsulace operací (např. načítání dat z API).  
  - **Poskytování služeb**: Využití dependency injection pro sdílení funkcionality.  

#### **Příklad**:  

```razor  
<!-- StateManager.razor (bez HTML) -->  
@code {  
    [Parameter]  
    public RenderFragment? ChildContent { get; set; }  

    public string Theme { get; set; } = "light";  
}  

<!-- Použití -->  
<StateManager>  
    <ChildComponent />  
</StateManager>  
```

---

### **2. Zachování relací stavu pomocí `@key`**  

- **Účel**: Zachování stavu komponent při změnách v kolekcích nebo dynamickém renderování (např. seznamy, podmíněné zobrazení).  
- **Princip**:  
  - `@key` přiřadí unikátní identifikátor k prvku nebo komponentě.  
  - Blazor podle klíče rozpozná, zda má komponentu **zachovat** nebo vytvořit novou instanci.  

#### **Příklad se seznamem**:  

```razor  
@foreach (var item in Items)  
{  
    <ChildComponent @key="item.Id" Item="item" />  
}  
```  
- **Výhoda**: Stav vstupních políček nebo rozbalených sekcí zůstává zachován i při změně pořadí položek.  

---

### **3. Kdy použít `@key`**  

- **Dynamické kolekce**: Renderování seznamů s přidáváním/odebíráním položek.  
- **Podmíněné zobrazení**: Přepínání mezi komponentami (např. formulářové kroky).  
- **Formuláře s komplexním stavem**: Editace více záznamů najednou.  

---

### **4. Kombinace komponent bez UI a `@key`** 

- **Scénář**: Komponenta bez vizuálního vzhledu spravuje stav a je použita v dynamickém seznamu.  
- **Příklad**:  
```razor  
@foreach (var config in Configurations)  
{  
    <ConfigurationProvider @key="config.Id" Config="config">  
        <ChildComponent />  
    </ConfigurationProvider>  
}  
```  
- **Efekt**: Stav `ConfigurationProvider` (např. načtená data) se zachová i při změně seznamu.  

---

### **5. Časté chyby a řešení**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|--------------------------------------------|  
| Ztráta stavu při změně kolekce       | Přidejte `@key` s unikátním identifikátorem (např. `item.Id`). |  
| Nechtěné překreslování komponent     | Omezte použití `@key` na nezbytné případy. |  
| Komponenta bez UI renderuje HTML     | Ověřte, že v `.razor` souboru není žádný markup. |  

---

### **6. Best Practices**  

- **`@key`**:  
  - Používejte pro prvky s interním stavem (formuláře, rozbalovací menu).  
  - Vyhněte se indexům seznamu jako klíči (`@key="index"`), použijte unikátní ID.  
- **Komponenty bez UI**:  
  - Dělejte je jednoduché a zaměřené na jednu zodpovědnost.  
  - Pro komunikaci s potomky používejte `CascadingValue` nebo `EventCallback`.  

---

### **7. Shrnutí**  

| **Koncept**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Komponenty bez UI**     | Logické jednotky pro správu stavu nebo služeb, bez HTML výstupu.          |  
| **`@key`**                | Zachovává stav komponent při dynamických změnách (seznamy, podmínky).     |  
| **Kombinace**             | `@key` zajišťuje stabilitu i u komponent bez vizuálního vzhledu.          |  

**Klíčové výhody**:  
- **Přehlednost**: Oddělení logiky od UI zlepšuje čitelnost kódu.  
- **Stabilita**: `@key` eliminuje neočekávané chování při změnách kolekcí.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Component Keys](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/#key).  
