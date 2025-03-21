
## **Komponenta 'QuickGrid' v ASP.NET Core Blazor**

---

### **1. Úvod do QuickGrid**  

- **Definice**: Komponenta `QuickGrid` je vestavěný nástroj v ASP.NET Core Blazor pro **efektivní zobrazování tabulkových dat**.  
- **Účel**: Umožňuje vytvářet výkonné a interaktivní tabulky s podporou řazení, stránkování a filtrování.  
- **Výhody**:  
  - **Optimalizace výkonu**: Efektivní vykreslování i pro velké datové sady.  
  - **Integrace s Blazorem**: Nativní podpora pro práci s Blazor komponentami a službami.  
  - **Jednoduchá konfigurace**: Minimální kód pro základní funkce.  

---

### **2. Klíčové funkce**  

1. **Řazení (Sorting)**:  
   - Automatické řazení dat po kliknutí na záhlaví sloupce.  
   - Podpora pro **více sloupců** najednou (Shift + klik).  
2. **Stránkování (Pagination)**:  
   - Rozdělení dat na stránky s možností nastavení počtu položek na stránku.  
3. **Šablony (Templates)**:  
   - Přizpůsobení vzhledu buněk, záhlaví nebo patičky tabulky.  
4. **Filtrování (Filtering)**:  
   - Možnost integrace vlastních filtrů (např. vyhledávací pole).  
5. **Virtuální scrollování**:  
   - Efektivní zobrazení velkých datových sad bez zpomalení (vykresluje pouze viditelné řádky).  

---

### **3. Základní použití**  

#### **a) Nastavení datového zdroje**  

- Data mohou být **kolekce v paměti** (např. `List<T>`) nebo **dotazy na server** (např. `IQueryable<T>`).  
```razor  
<QuickGrid Items="@users">  
    <!-- Definice sloupců -->  
</QuickGrid>  

@code {  
    private List<User> users = new();  
}  
```

#### **b) Definice sloupců**  

- Každý sloupec se konfiguruje pomocí `PropertyColumn` nebo `TemplateColumn`.  
```razor  
<PropertyColumn Title="Jméno" Property="@(u => u.Name)" Sortable="true" />  
<TemplateColumn Title="Akce">  
    <Template Context="user">  
        <button @onclick="() => DeleteUser(user)">Smazat</button>  
    </Template>  
</TemplateColumn>  
```

---

### **4. Pokročilá konfigurace**  

- **Vlastní řazení**:  
  ```razor  
  <PropertyColumn Title="Věk" Property="@(u => u.Age)" SortBy="@(u => u.Age * 2)" />  
  ```  
- **Stránkování s `Paginator`**:  
  ```razor  
  <Paginator TotalItems="@totalUsers" PageSize="20" @bind-CurrentPage="currentPage" />  
  ```  
- **Stylování**:  
  - Přidání CSS tříd pomocí `Class` atributu.  
  ```razor  
  <QuickGrid Class="custom-grid">  
      <!-- Sloupce -->  
  </QuickGrid>  
  ```  

---

### **5. Integrace se serverovým kódem**  

- Pro práci s velkými daty lze použít **serverové stránkování a řazení**:  
  ```csharp  
  var query = context.Users  
      .Where(u => u.IsActive)  
      .OrderBy(u => u.Name)  
      .Skip((currentPage - 1) * pageSize)  
      .Take(pageSize);  
  ```  

---

### **6. Výkonové tipy**  

- **Virtuální scrollování**: Aktivujte pomocí `Virtualize="true"`.  
- **Minimalizace re-renderů**: Používejte `@key` pro řádky.  
- **Optimalizace dotazů**: Při serverovém stránkování vždy omezujte dotazy na potřebná data.  

---

### **7. Omezení**  

- **Základní funkcionalita**: Chybí pokročilé funkce jako editace buněk nebo drag-and-drop (nutné implementovat ručně).  
- **Návrhová omezení**: Primárně určeno pro zobrazení dat, ne pro komplexní interakce.  

---

### **8. Příklady použití**  

- **Seznam uživatelů**: Zobrazení s možností řazení dle jména, věku, atd.  
- **Reporty**: Tabulky s finančními daty a stránkováním.  
- **Admin rozhraní**: Správa produktů nebo objednávek s filtry.  

---

### **9. Doporučené zdroje**  

- **Oficiální dokumentace**: [Microsoft Learn – QuickGrid](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/quickgrid).  
- **Ukázkové projekty**: [GitHub – Blazor Samples](https://github.com/dotnet/blazor-samples).  

---

**Shrnutí**:  
- `QuickGrid` je ideální pro **rychlou implementaci tabulek** s řazením a stránkováním.  
- Klíčové přednosti: **výkon, integrace s Blazorem a jednoduchost**.  
- Pro složitější scénáře je nutné rozšířit funkcionalitu vlastními komponentami.
