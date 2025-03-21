
## **Opožděné načtení sestavení v ASP.NET Core Blazor WebAssembly**

---

### **1. Definice**  

Opožděné načtení (angl. *Lazy Loading*) je technika optimalizace výkonu, při které se vybraná sestavení (assemblies) načítají až v okamžiku, kdy jsou skutečně potřeba. V Blazor WebAssembly to pomáhá snížit počáteční dobu načítání aplikace, protože se nestahují všechny součásti najednou.

---

### **2. Výhody**  

- **Rychlejší počáteční načtení**: Menší velikost stažených dat při startu.  
- **Úspora šířky pásma**: Sestavení se načítají pouze při potřebě.  
- **Lepší uživatelský zážitek**: Ideální pro rozsáhlé aplikace s moduly, které nejsou hned využívány.

---

### **3. Implementace**  

#### **Krok 1: Úprava projektu (.csproj)**  

Označte sestavení pro opožděné načtení v souboru `.csproj`:  
```xml
<ItemGroup>
  <BlazorWebAssemblyLazyLoad Include="NázevSestavení.dll" />
</ItemGroup>
```

#### **Krok 2: Konfigurace služby v `Program.cs`**  

Přidejte podporu pro načítání sestavení:  
```csharp
builder.Services.AddLazyAssemblyLoader();
```

#### **Krok 3: Úprava komponenty `Router`**  

V `App.razor` upravte `Router` pro načítání sestavení při navigaci:  
```razor
<Router AppAssembly="@typeof(Program).Assembly" 
        OnNavigateAsync="@OnNavigateAsync">
    <!-- ... -->
</Router>

@code {
    private async Task OnNavigateAsync(NavigationContext context) {
        var lazyLoader = context.Services.GetRequiredService<LazyAssemblyLoader>();
        
        // Načtení sestavení pro konkrétní cestu (např. "/lazy-page")
        if (context.Path == "/lazy-page") {
            var assemblies = await lazyLoader.LoadAssembliesAsync(new[] { "NázevSestavení.dll" });
        }
    }
}
```

---

### **4. Omezení a důležité poznámky**  

- **Počáteční sestavení**: Sestavení nutná pro start aplikace (např. `Microsoft.AspNetCore.Components.Web.dll`) nelze načítat opožděně.  
- **Závislosti**: Pokud opožděné sestavení závisí na jiných knihovnách, musí být tyto závislosti také označeny pro opožděné načtení nebo zahrnuty do počátečního balíčku.  
- **AOT kompilace**: Opožděné načtení není kompatibilní s Ahead-of-Time (AOT) kompilací.  
- **Latence**: První načtení sestavení může způsobit krátké zpoždění (vhodné zobrazit indikátor načítání).

---

### **5. Doporučené postupy**  

- **Testování výkonu**: Ověřte, zda opožděné načtení skutečně zlepšuje výkon.  
- **Upozornění pro uživatele**: Při načítání zobrazte např. spinner nebo zprávu.  
- **Rozdělení logiky**: Seskupujte funkcionality do samostatných sestavení podle cest (routing).  

---

### **6. Typické chyby**  

- **Zapomenuté závislosti**: Neuvedení všech potřebných DLL pro fungování opožděného sestavení.  
- **Špatné cesty**: Chyby v cestách k sestavením způsobí selhání načtení.  
- **Nadměrné použití**: Příliš časté dělení sestavení může zvýšit komplexitu bez výrazného benefitu.

---

### **7. Shrnutí**  

Opožděné načtení je užitečný nástroj pro optimalizaci Blazor WebAssembly aplikací, ale vyžaduje pečlivé plánování. Vhodné pro aplikace s moduly, které nejsou využívány všemi uživateli nebo při startu. Vždy testujte dopad na výkon a UX.
