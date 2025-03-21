
## **ASP.NET Core Blazor – Režimy vykreslování**  

ASP.NET Core Blazor (od verze .NET 8) umožňuje volbu **režimu vykreslování** pro komponenty. Tato volba určuje, kde a jak se komponenta zpracovává (na serveru nebo na klientovi) a zda je interaktivní. Zde je přehled dostupných režimů:

---

### **1. Režimy vykreslování**  

#### **a) Statické vykreslování (Static Server Rendering)**  

- **Princip**:  
  - Komponenta se vygeneruje na serveru **jednou** jako statické HTML.  
  - Žádná interaktivita (např. tlačítka nereagují na kliknutí).  
- **Použití**:  
  - Pro čistě informační obsah (např. textové stránky, blogy).  
  - Optimalizace výkonu pro neinteraktivní části aplikace.  
- **Nastavení**:  
  ```razor
  @rendermode RenderMode.Static
  ```

#### **b) Interaktivní serverové vykreslování (Interactive Server Rendering)**  

- **Princip**:  
  - UI se generuje na serveru.  
  - Interakce uživatele se přenášejí přes **SignalR** (real-time spojení).  
  - Stav komponenty je uložen na serveru.  
- **Výhody**:  
  - Menší velikost staženého kódu (logika běží na serveru).  
  - Kompatibilita se staršími prohlížeči.  
- **Nevýhody**:  
  - Závislost na stabilním připojení (latence).  
  - Vyšší zátěž serveru.  
- **Nastavení**:  
  ```razor
  @rendermode RenderMode.InteractiveServer
  ```

#### **c) Interaktivní vykreslování pomocí WebAssembly (Interactive WebAssembly Rendering)**  

- **Princip**:  
  - Komponenta se stáhne do prohlížeče a běží jako **WebAssembly**.  
  - Veškerá logika (C#) se zpracovává na straně klienta.  
- **Výhody**:  
  - Offline funkčnost.  
  - Nízká latence po inicializaci.  
- **Nevýhody**:  
  - Delší počáteční načítání (stahování WASM).  
  - Omezená podpora v některých prohlížečích.  
- **Nastavení**:  
  ```razor
  @rendermode RenderMode.InteractiveWebAssembly
  ```

#### **d) Automatický režim (Auto Rendering)**  

- **Princip**:  
  - Komponenta se **nejprve vykreslí na serveru** (pro rychlé zobrazení).  
  - Následně se automaticky přepne na **WebAssembly**, pokud je podporováno.  
- **Výhody**:  
  - Kombinuje rychlé načtení (Server) s offline schopnostmi (WASM).  
- **Nastavení**:  
  ```razor
  @rendermode RenderMode.InteractiveAuto
  ```

---

### **2. Jak nastavit režim vykreslování?**  

- **Globálně**: V `App.razor` nebo konfigurací projektu.  
- **Lokálně**: Přímo v komponentě pomocí direktivy `@rendermode`.  
  ```razor
  @page "/"
  @rendermode InteractiveServer

  <h1>Ahoj, Blazor!</h1>
  ```

---

### **3. Srovnání režimů**  

| **Parametr**               | **Static Server** | **Interactive Server** | **Interactive WebAssembly** | **Auto** |  
|----------------------------|-------------------|------------------------|-----------------------------|----------|  
| **Interaktivita**          | Ne                | Ano                    | Ano                         | Ano      |  
| **Místo běhu logiky**      | Server            | Server                 | Klient (WASM)               | Klient   |  
| **Offline režim**          | Ne                | Ne                     | Ano                         | Ano      |  
| **Počáteční načtení**      | Rychlé            | Rychlé                 | Pomalé                      | Rychlé   |  
| **Nároky na server**       | Nízké             | Vysoké                 | Nízké                       | Střední  |  

---

### **4. Prerendering (Předvykreslování)** 

- **Princip**:  
  - Komponenta se nejprve vygeneruje na serveru jako statické HTML (pro rychlé zobrazení).  
  - Po načtení stránky se přepne do interaktivního režimu.  
- **Použití**:  
  - Zlepšení vnímání rychlosti (SEO-friendly).  
- **Nastavení**:  
  ```razor
  @page "/"
  @rendermode InteractiveServer
  @attribute [Prerender]
  ```

---

### **5. Doporučení pro výběr režimu**  

1. **Static Server**:  
   - Pro statický obsah bez interakce (např. dokumentace).  
2. **Interactive Server**:  
   - Pro interní aplikace s rychlým vývojem a stabilním připojením.  
3. **Interactive WebAssembly**:  
   - Pro veřejné aplikace s offline režimem nebo PWA.  
4. **Auto**:  
   - Pro univerzální použití (např. kombinace rychlosti a offline funkcí).  

---

### **6. Ukázka kombinace režimů v aplikaci**  

- **HomePage.razor** (statická):  
  ```razor
  @page "/"
  @rendermode RenderMode.Static

  <Article Content="@staticText" />
  ```  
- **Dashboard.razor** (interaktivní):  
  ```razor
  @page "/dashboard"
  @rendermode RenderMode.InteractiveWebAssembly

  <ChartComponent Data="@salesData" />
  ```

---

### **Shrnutí**  

- **Režimy vykreslování** určují, kde a jak se komponenta zpracovává.  
- **Interaktivní režimy** (Server/WASM/Auto) umožňují reakce na uživatelské akce.  
- **Prerendering** zlepšuje SEO a vnímání rychlosti.  
- Volba režimu závisí na požadavcích projektu (výkon, offline režim, škálovatelnost).  

**Užitečné zdroje**:  
- Oficiální dokumentace: [Blazor Rendering Modes](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/render-modes).  
