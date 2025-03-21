
## **Použití Razor komponent v JavaScriptových aplikacích a SPA architekturách v ASP.NET Core Blazor**

---

### **1. Úvod do integrace Blazor s JavaScriptem**  

ASP.NET Core Blazor umožňuje kombinovat **Razor komponenty** (psané v C#) s JavaScriptovými aplikacemi nebo SPA (Single-Page Application) architekturami (např. React, Angular, Vue). Tato integrace je užitečná pro postupnou migraci, využití specifických funkcí Blazoru v existujícím JavaScriptovém projektu nebo pro propojení výhod obou technologií.

---

### **2. Klíčové metody integrace**  

#### **a) JavaScript Interoperabilita (JS Interop)**  

- **Blazor → JavaScript**: Volání JS funkcí z C# pomocí `IJSRuntime`.  
  ```csharp  
  await JSRuntime.InvokeVoidAsync("alert", "Ahoj z Blazor!");  
  ```  
- **JavaScript → Blazor**: Volání C# metod z JS pomocí `DotNet.invokeMethodAsync`.  
  ```javascript  
  await DotNet.invokeMethodAsync("MojeKnihovna", "MetodaVCsharp", data);  
  ```  

#### **b) Hostování Blazor komponent v JavaScriptových aplikacích** 

- **Blazor WebAssembly jako samostatný modul**:  
  - Blazor komponenty lze hostovat v iframe nebo jako samostatnou část stránky.  
  - Komunikace přes `postMessage` nebo vlastní eventy.  
- **Využití custom HTML elementů**:  
  - Balení Blazor komponenty jako webové komponenty (Custom Elements) pro použití v jakémkoli JS frameworku.  

#### **c) Společné routování**  

- Synchronizace URL mezi Blazor a JavaScriptovou aplikací pomocí `NavigationManager` a JS historie.  

---

### **3. Scénáře použití**  

- **Migrace existující SPA**: Postupné nahrazování částí JavaScriptové aplikace Blazor komponentami.  
- **Využití C# logiky**: Komplexní výpočty, integrace s .NET knihovnami (např. ML.NET).  
- **Interaktivní widgety**: Grafy, formuláře, nebo real-time prvky v JavaScriptové aplikaci.  

---

### **4. Návrhové vzory**  

#### **a) Mikrofrontendy**  

- Blazor komponenty fungují jako nezávislé mikrofrontendy v rámci větší SPA.  
- Komunikace přes **události** nebo **stavové úložiště** (např. Redux, Vuex).  

#### **b) Hybridní aplikace**  

- **Blazor Server pro rychlou interaktivitu**: Renderování na serveru s nízkou latencí.  
- **Blazor WebAssembly pro offline funkce**: Samostatné moduly nezávislé na serveru.  

---

### **5. Technické výzvy**  

- **Velikost Blazor WebAssembly**:  
  - **Řešení**: Lazy loading, optimalizace pomocí AOT (Ahead-Of-Time kompilace).  
- **Komunikace mezi C# a JS**:  
  - **Omezení**: Serializace komplexních objektů (např. tříd s metodami).  
- **Konflikty stylů**:  
  - **Řešení**: Izolace CSS pomocí `::deep` nebo CSS modules.  

---

### **6. Nástroje a konfigurace**  

- **Balíčkování komponent**:  
  - Publikování Blazor komponent jako **Razor Class Library (RCL)** nebo **NuGet balíčku**.  
- **Integrace s buildovacím procesem**:  
  - Přidání Blazor projektu do JS build pipeline (např. Webpack, Vite).  
- **Konfigurace CORS**: Pro komunikaci s backendovými API z Blazor WebAssembly.  

---

### **7. Příklady**  

#### **a) Vložení Blazor komponenty do Reactu**  

```javascript  
// React komponenta  
function App() {  
    return (  
        <div>  
            <div id="blazor-container"></div>  
        </div>  
    );  
}  

// Inicializace Blazoru  
Blazor.start().then(() => {  
    Blazor.rootComponents.add("blazor-container", { type: 'counter' });  
});  
```  

#### **b) Komunikace přes události**  

```javascript  
// V JavaScriptu  
document.addEventListener("blazorEvent", (e) => {  
    console.log("Data z Blazor:", e.detail);  
});  
```  
```csharp  
// V Blazoru  
await JSRuntime.InvokeVoidAsync("dispatchEvent",  
    new { EventName = "blazorEvent", Data = "Ahoj!" });  
```  

---

### **8. Best Practices**  

- **Minimalizujte JS Interop**: Časté volání mezi C# a JS může zpomalit aplikaci.  
- **Používejte univerzální komponenty**: Návrh komponent bez závislosti na konkrétním JS frameworku.  
- **Testujte výkon**: Zaměřte se na dobu načítání a odezvu UI.  

---

### **Shrnutí**  

| **Aspekt**               | **Klíčový bod**                                                          |  
|--------------------------|---------------------------------------------------------------------------|  
| **JS Interop**           | Propojení logiky mezi C# a JavaScriptem.                                  |  
| **Hostování komponent**  | Blazor jako mikrofrontend nebo custom element.                            |  
| **Migrace**              | Postupné nahrazování částí JS aplikace Blazorem.                           |  
| **Výkon**                | Optimalizace velikosti WASM a lazy loading.                               |  

**Užitečné zdroje**:  
- [Dokumentace: Blazor JS Interop](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/javascript-interoperability/)  
- [Ukázka integrace Blazor s Reactem](https://github.com/dotnet/blazor-samples/tree/main/samples)
