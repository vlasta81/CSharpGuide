
## **Modely hostování ASP.NET Core Blazor**  

ASP.NET Core Blazor nabízí různé hostingové modely pro tvorbu webových i desktopových aplikací. Každý model má odlišnou architekturu, výhody a nevýhody. Zde je přehled hlavních modelů:

---

### **1. Blazor Server**  

- **Princip fungování**:  
  - Aplikace běží na serveru.  
  - UI se generuje na straně klienta (prohlížeče) pomocí **SignalR** (real-time komunikace).  
  - Uživatelské akce (kliknutí, formuláře) se odesílají na server, který aktualizuje stav a vrací změny do prohlížeče.  

- **Výhody**:  
  - Rychlé načítání (malá velikost stažených dat).  
  - Plná kompatibilita se staršími prohlížeči.  
  - Bezpečnost (logika zůstává na serveru).  
  - Snadná integrace s existujícími .NET službami (např. Entity Framework).  

- **Nevýhody**:  
  - Vyšší latence (závislost na připojení k serveru).  
  - Větší zátěž serveru při vysokém počtu uživatelů.  
  - Omezená offline funkčnost.  

- **Vhodné pro**:  
  - Aplikace s rychlým vývojem a menším počtem uživatelů.  
  - Projekty, kde je klíčová bezpečnost a centralizovaná správa dat.  

---

### **2. Blazor WebAssembly (WASM)**  

- **Princip fungování**:  
  - Aplikace se kompiluje do **WebAssembly** a spouští přímo v prohlížeči.  
  - Veškerá logika (C#) běží na straně klienta.  
  - Komunikace se serverem probíhá přes API (REST, gRPC).  

- **Výhody**:  
  - Offline režim (až 100% nezávislost na serveru).  
  - Menší zatížení serveru.  
  - Možnost tvorby **Progressive Web Apps (PWA)**.  
  - Lepší škálovatelnost (uživatelé nezatěžují server).  

- **Nevýhody**:  
  - Delší inicializace (stahování většího objemu dat).  
  - Omezená podpora některých prohlížečů (starší verze).  
  - Bezpečnostní rizika (logika je vystavena na straně klienta).  

- **Vhodné pro**:  
  - Aplikace vyžadující offline funkčnost (např. mobilní nástroje).  
  - Projekty s vysokou interaktivitou a nezávislostí na serveru.  

---

### **3. Blazor Hybrid**  

- **Princip fungování**:  
  - Kombinuje webové komponenty Blazor s **nativními frameworky** (např. .NET MAUI, WPF, WinForms).  
  - UI se renderuje pomocí webových technologií (HTML/CSS), ale aplikace běží jako nativní desktop/mobilní aplikace.  

- **Výhody**:  
  - Přístup k nativním funkcím zařízení (souborový systém, senzory).  
  - Sdílení kódu mezi webem a desktopem/mobilem.  
  - Využití stávajících Blazor komponent v hybridních aplikacích.  

- **Nevýhody**:  
  - Vyšší nároky na vývoj (nutnost znalosti nativních frameworků).  
  - Aplikace nelze spustit přímo v prohlížeči.  

- **Vhodné pro**:  
  - Desktopové/mobilní aplikace s webovým UI.  
  - Projekty, kde je třeba integrovat webové a nativní prvky.  

---

### **4. Blazor United (experimentální/plánovaný model)**  

- **Princip fungování**:  
  - Kombinuje výhody Blazor Server a WebAssembly v jedné aplikaci.  
  - Umožňuje **dynamické přepínání** mezi renderingem na serveru a na straně klienta.  
  - Cíl: Sjednotit vývoj pro různé scénáře pod jednu architekturu.  

- **Výhody** (očekávané):  
  - Optimalizace výkonu (načtení pomocí serveru, poté přechod na WASM).  
  - Jednotný kódový základ pro všechny hostingové modely.  

- **Nevýhody**:  
  - Model je ve fázi návrhu (není plně dostupný v aktuálních verzích .NET).  

---

### **Srovnání modelů** 

| **Parametr**         | **Blazor Server**       | **Blazor WebAssembly** | **Blazor Hybrid**      |  
|----------------------|-------------------------|-------------------------|------------------------|  
| **Místo běhu**       | Server                  | Prohlížeč (WASM)       | Nativní aplikace       |  
| **Offline režim**    | Ne                      | Ano                    | Ano                    |  
| **Latence**          | Vyšší                  | Nízká                  | Nízká                  |  
| **Nároky na server** | Vysoké                 | Nízké                  | Žádné                  |  
| **Přístup k HW**     | Ne                      | Omezený (přes JS)      | Plný (nativní API)     |  

---

### **Kdy použít který model?** 

1. **Blazor Server**:  
   - Pro interní firemní systémy s rychlým vývojem a stabilním připojením.  
2. **Blazor WebAssembly**:  
   - Pro veřejné webové aplikace s offline režimem nebo PWA.  
3. **Blazor Hybrid**:  
   - Pro aplikace potřebující nativní funkce (desktop/mobil) s webovým UI.  

---

**Shrnutí**:  
Volba modelu závisí na požadavcích projektu:  
- **Server** = rychlý vývoj + centralizovaná logika.  
- **WebAssembly** = offline režim + nezávislost na serveru.  
- **Hybrid** = kombinace webového UI s nativními možnostmi.
