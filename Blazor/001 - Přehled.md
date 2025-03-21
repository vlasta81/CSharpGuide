
## **Přehled .NET Core Blazor – Studijní souhrn**

**1. Úvod do Blazor**  

- **Definice**: Blazor je open-source framework od Microsoftu pro tvorbu interaktivních webových aplikací pomocí C# a .NET Core.  
- **Hlavní cíl**: Umožnit vývoj webových aplikací bez nutnosti používat JavaScript – logika i UI se píší v C#.  
- **Typy aplikací**: Vhodné pro SPA (Single-Page Applications), dynamické weby, enterprise systémy.

**2. Hostingové modely**  

- **Blazor Server**:  
  - Aplikace běží na serveru. UI aktualizace se přenášejí přes SignalR (real-time komunikace).  
  - **Výhody**: Rychlé načítání, bezpečnost, kompatibilita se staršími prohlížeči.  
  - **Nevýhody**: Závislost na stabilním připojení (latence), vyšší zátěž serveru.  

- **Blazor WebAssembly (WASM)**:  
  - Aplikace se spouští přímo v prohlížeči pomocí WebAssembly.  
  - **Výhody**: Offline funkčnost, menší zátěž serveru.  
  - **Nevýhody**: Delší inicializace, větší velikost stahovaných souborů, omezená podpora v některých prohlížečích.

**3. Klíčové vlastnosti**  

- **Komponentová architektura**: UI se skládá z přehledných komponent (např. `NavMenu.razor`), které lze znovu použít.  
- **Routing**: Definice cest pomocí direktivy `@page` (např. `@page "/users"`).  
- **Datová vazba**: Dvoucestné bindování (two-way binding) pomocí `@bind`.  
- **Dependency Injection (DI)**: Integrovaná podpora pro správu závislostí (např. přidání služby přes `services.AddSingleton<IMyService, MyService>()`).  
- **JavaScript Interoperabilita**: Volání JavaScriptu z C# a naopak pomocí `IJSRuntime`.  
- **Server-Side Rendering (SSR)**: Možnost generovat statický obsah na serveru (v Blazor Server).

**4. Srovnání s jinými frameworky**  

- **vs JavaScript (React, Angular)**:  
  - **Výhody Blazor**: Jednotný jazyk (C# pro frontend i backend), přístup k .NET knihovnám.  
  - **Nevýhody**: Menší komunita, WebAssembly může být pomalejší než optimalizovaný JS.  
- **vs ASP.NET MVC**: Blazor je vhodnější pro interaktivní aplikace, zatímco MVC pro tradiční weby s server-side renderingem.

**5. Výhody a nevýhody**  

- **Výhody**:  
  - C# po celém stacku (frontend + backend).  
  - Komponentový model pro přehledný kód.  
  - Integrace s .NET ekosystémem (Entity Framework, Azure).  
- **Nevýhody**:  
  - Náročnější učení pro vývojáře bez zkušeností s .NET.  
  - WebAssembly aplikace mají větší velikost.  
  - Omezené nástroje pro debugování WASM.

**6. Příklady použití**  

- Enterprise aplikace (správa dat, interní systémy).  
- Administrativní rozhraní (dashboardy, reporty).  
- Prototypy nebo menší projekty, kde se využije C#.  
- Progressive Web Apps (PWA) s offline režimem.

**7. Vývojové nástroje**  

- **IDE**: Visual Studio 2022+ (plná podpora), Visual Studio Code s extenzí C#.  
- **Browser Tools**: Nástroje pro vývojáře v prohlížeči (např. sledování WebAssembly).  
- **Balíčky**: NuGet pro správu knihoven, npm pro případnou integraci JS.

**8. Užitečné zdroje**  

- Oficiální dokumentace: [Microsoft Learn – Blazor](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/)  
- Komunita: Fóra jako Stack Overflow, české skupiny na Facebooku (např. "C# a .NET CZ/SK").  
- Tutoriály: Blazor University, YouTube kanály (např. "Raw Coding").  

**Shrnutí**: Blazor je moderní framework, který rozšiřuje možnosti .NET vývoje do frontendu. Volba mezi Server a WebAssembly závisí na požadavcích projektu (škálovatelnost, offline režim). Vhodný pro týmy, které chtějí využít C# i na straně klienta.
