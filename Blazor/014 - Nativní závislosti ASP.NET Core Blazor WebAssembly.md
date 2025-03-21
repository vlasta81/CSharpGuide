
## **Nativní závislosti v ASP.NET Core Blazor WebAssembly**

---

### **1. Definice**  

Nativní závislosti v Blazor WebAssembly označují použití knihoven nebo kódu, které nejsou napsány v .NET, ale jsou využity v aplikaci. Nejčastěji jde o:  
- **JavaScript (JS) knihovny** (např. pro práci s localStorage, třetí strany API).  
- **Kód v C/C++ nebo Rustu** kompilovaný do WebAssembly (WASM) a integrovaný do aplikace.  
- **Nízkoúrovňové API prohlížeče**, ke kterým .NET přímo nepřistupuje.

---

### **2. Výhody**  

- **Přístup k funkcím prohlížeče**: Využití JS API (např. geolokace, kamera).  
- **Využití existujících knihoven**: Integrace populárních JS knihoven (např. Chart.js, Moment.js).  
- **Optimalizace výkonu**: Kritické části kódu lze napsat v C/C++ a kompilovat do WebAssembly.  
- **Flexibilita**: Kombinace .NET s nativními technologiemi.

---

### **3. Implementace**  

#### **A. JavaScript Interoperabilita (JS interop)**  

Blazor umožňuje volat JS funkce z C# a naopak.  
- **Krok 1: Přidání JS souboru**  
  Vložte JS soubor do složky `wwwroot` a přidejte referenci do `index.html`:  
  ```html
  <script src="js/nazevKnihovny.js"></script>
  ```

- **Krok 2: Volání JS z C#**  
  Použijte službu `IJSRuntime`:  
  ```csharp
  @inject IJSRuntime JSRuntime

  private async Task ZavolatJSFunkci() {
      var result = await JSRuntime.InvokeAsync<string>("nazevFunkce", parametr);
  }
  ```

- **Krok 3: Volání C# z JS**  
  Vytvořte statickou metodu s atributem `[JSInvokable]`:  
  ```csharp
  [JSInvokable]
  public static Task<string> MetodaVolanaZJS(int param) {
      return Task.FromResult("Výsledek z .NET");
  }
  ```

#### **B. Integrace C/C++ kódu jako WebAssembly**  

1. Kompilujte C/C++ kód do WASM pomocí nástrojů jako **Emscripten**.  
2. Výsledné soubory (`*.wasm`, `*.js`) vložte do `wwwroot`.  
3. Načtěte modul přes JS interop:  
   ```javascript
   // V JS
   async function loadWasm() {
       const module = await import('./nazevModulu.js');
       return module;
   }
   ```

---

### **4. Omezení a výzvy**  

- **Výkon**: Časté volání JS interop zpomaluje aplikaci.  
- **Velikost aplikace**: Nativní závislosti zvětšují stažená data.  
- **Bezpečnost**: JS interop může vést k XSS útokům, pokud nejsou data validována.  
- **Trimming**: Při publikování může linker odstranit nepoužívaný kód, což způsobí chyby u dynamicky volaných metod.  
- **Kompatibilita**: Nativní kód (C/C++) může vyžadovat složitou konfiguraci.

---

### **5. Doporučené postupy**  

- **Minimalizujte JS interop**: Snižte počet volání mezi .NET a JS.  
- **Používejte NuGet balíčky**: Např. `BlazorExtensions.Storage` pro localStorage místo vlastní implementace.  
- **Ošetřete chyby**: Používejte `try-catch` při volání JS funkcí.  
- **Optimalizujte WASM moduly**: Komprimujte soubory a využívejte lazy loading.  
- **Testujte na různých prohlížečích**: Zajistěte kompatibilitu nativních závislostí.

---

### **6. Typické chyby**  

- **Chybějící reference na JS soubory**: Zapomenutí přidat `<script>` v `index.html`.  
- **Nesprávné typy parametrů**: Rozdíly mezi typy v JS a .NET (např. `int` vs. `number`).  
- **Memory leaks**: Neukončené event listenery nebo neuklizené objekty v JS.  
- **Ignorování asynchronního chování**: Zapomenutí použít `await` u asynchronních volání.

---

### **7. Shrnutí**  

Nativní závislosti umožňují Blazor WebAssembly aplikacím využívat široké možnosti webového ekosystému. Klíčové je:  
- Správná implementace JS interop pro komunikaci s prohlížečem.  
- Opatrnost při integraci C/C++ kódu (optimalizace velikosti a výkonu).  
- Důraz na bezpečnost a testování kompatibility.  
Používejte je tam, kde .NET sám nestačí, ale vždy zvažte alternativy v čistém .NET pro udržení jednoduchosti.
