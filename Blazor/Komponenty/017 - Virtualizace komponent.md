
## **Virtualizace komponent v ASP.NET Core Blazor**  

Virtualizace komponent je technika, která **optimalizuje výkon** při zobrazování rozsáhlých seznamů nebo tabulek tím, že renderuje pouze prvky viditelné v aktuálním viewportu (např. při scrollování). Blazor poskytuje integrovanou podporu pro virtualizaci pomocí komponenty **`Virtualize`**.

---

### **1. Princip virtualizace**  

- **Problém**: Renderování tisíců položek najednou zpomaluje aplikaci a zatěžuje prohlížeč.  
- **Řešení**:  
  - Virtuální seznam **dynamicky načítá a uvolňuje prvky** podle pozice scrollu.  
  - V DOM jsou přítomny pouze prvky v (nebo blízko) viditelné oblasti.  

---

### **2. Základní použití komponenty `Virtualize`**  

- **Příklad**:  
  ```razor  
  <Virtualize Items="@seznamPoliček" Context="polozka">  
      <div class="polozka">@polozka.Nazev</div>  
  </Virtualize>  
  ```  
  - **`Items`**: Kolekce dat (např. `List<T>`, `IQueryable<T>`).  
  - **`Context`**: Název proměnné reprezentující aktuální položku (volitelné).  

---

### **3. Klíčové parametry `Virtualize`**  

| **Parametr**         | **Popis**                                                                 |  
|----------------------|---------------------------------------------------------------------------|  
| **`Items`**          | Kolekce položek k zobrazení.                                              |  
| **`ItemSize`**       | Výška (nebo šířka) jedné položky v px (výchozí: 50px).                    |  
| **`OverscanCount`** | Počet položek mimo viewport, které se přednačtou pro plynulý scroll (výchozí: 3). |  
| **`ItemsProvider`**  | Metoda pro asynchronní načítání dat (např. z API) – vhodné pro velké datasety. |  

---

### **4. Asynchronní načítání dat (`ItemsProvider`)**  

- **Scénář**: Datový zdroj je příliš velký na to, aby se načetl celý najednou (např. databáze s miliony záznamů).  
- **Implementace**:  
  ```csharp  
  private async ValueTask<ItemsProviderResult<Polozka>> NactiPolozky(  
      ItemsProviderRequest request)  
  {  
      var polozky = await Repozitar.GetPolozkyAsync(  
          request.StartIndex, request.Count);  
      return new ItemsProviderResult<Polozka>(polozky, celkovyPocet);  
  }  
  ```  
  ```razor  
  <Virtualize ItemsProvider="@NactiPolozky" Context="polozka">  
      <div>@polozka.Nazev</div>  
  </Virtualize>  
  ```  

---

### **5. Přizpůsobení vzhledu**  

- **Vlastní šablona obsahu**:  
  ```razor  
  <Virtualize Items="@polozky">  
      <ItemContent>  
          <div>@context.Nazev</div>  
      </ItemContent>  
      <Placeholder>  
          <div>Načítám...</div> <!-- Zobrazeno během načítání -->  
      </Placeholder>  
  </Virtualize>  
  ```  

---

### **6. Kdy použít virtualizaci?**  

- **Vhodné pro**:  
  - Seznamy s **více než 100 položkami**.  
  - Tabulky s velkým počtem řádků.  
  - Data načtená z externích API nebo databází.  
- **Nevhodné pro**:  
  - Komponenty vyžadující interakci všech prvků (např. komplexní formuláře).  
  - Situace, kdy je třeba přistupovat k všem prvkům v DOM (např. pro JavaScriptové knihovny).  

---

### **7. Výkon a optimalizace**  

- **`ItemSize`**: Nastavte přesnou výšku/šířku pro přesné výpočty rozložení.  
- **`OverscanCount`**: Zvýšením hodnoty zlepšíte plynulost scrollování, ale zvýšíte paměťovou náročnost.  
- **`ItemsProvider`**: Pro extrémně velké datasety kombinujte s stránkováním na serveru.  

---

### **8. Časté problémy a řešení**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|--------------------------------------------|  
| Prázdné oblasti při scrollování      | Zkontrolujte `ItemSize` – může být nesprávně nastaveno. |  
| Pomalé načítání dat                  | Optimalizujte `ItemsProvider` (např. cachováním). |  
| Nezobrazují se všechny položky       | Zajistěte, aby `ItemsProvider` vracel správný počet položek. |  

---

### **9. Shrnutí** 

| **Aspekt**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Cíl**                  | Optimalizace výkonu pro velké datasety.                                   |  
| **Základní komponenta**  | `<Virtualize>`                                                            |  
| **Klíčové parametry**    | `Items`, `ItemSize`, `OverscanCount`, `ItemsProvider`.                    |  
| **Výhody**               | Snížení paměťové náročnosti a zrychlení renderování.                      |  

**Doporučení**:  
- Pro statické kolekce použijte `Items`.  
- Pro dynamické nebo extrémně velké datasety použijte `ItemsProvider`.  
- Vždy testujte s reálnými daty pro optimalizaci `ItemSize` a `OverscanCount`.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Virtualize Component](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/virtualization).  
