
## **Blazor ASP.NET Progressive Webová Aplikace (PWA)**

---

### **1. Definice**  

Progresivní webová aplikace (PWA) v Blazor WebAssembly kombinuje funkcionalitu webu a nativní aplikace:  
- **Instalovatelnost**: Uživatelé ji mohou přidat na domovskou obrazovku.  
- **Offline podpora**: Funguje bez internetu díky service workeru a cachování.  
- **Push oznámení**: Real-time aktualizace i při zavřené aplikaci.  
- **Vysoký výkon**: Optimalizované načítání a práce se zdroji.

---

### **2. Vytvoření projektu ze šablony PWA**  

#### **Krok 1: Nový projekt**  

Použijte příkaz v CLI:  
```bash
dotnet new blazorwasm -o NázevProjektu --pwa
```  
Nebo v Visual Studio zaškrtněte **Progressive Web Application** při vytváření projektu.  

#### **Krok 2: Struktura šablony**  

- **`wwwroot/manifest.json`**: Definuje metadata pro instalaci (název, ikony, barvy).  
- **`wwwroot/service-worker.js`**: Service worker pro offline podporu a cachování.  
- **`wwwroot/service-worker.published.js`**: Service worker pro publikovanou verzi.  
- **`index.html`**: Obsahuje odkaz na manifest a registraci service workeru.

---

### **3. Převod existující Blazor WebAssembly aplikace na PWA**  

#### **Krok 1: Přidání PWA funkcí**  

1. Upravte `.csproj` soubor:  
   ```xml
   <PropertyGroup>
     <ServiceWorkerAssetsManifest>service-worker-assets.js</ServiceWorkerAssetsManifest>
   </PropertyGroup>
   <ItemGroup>
     <ServiceWorker Include="wwwroot\service-worker.js" PublishedContent="wwwroot\service-worker.published.js" />
   </ItemGroup>
   ```  
2. Vytvořte `manifest.json` v `wwwroot`.  
3. Přidejte service worker soubory (`service-worker.js`, `service-worker.published.js`).  

#### **Krok 2: Registrace Service Workeru** 

V `wwwroot/index.html` přidejte:  
```html
<script>
  if ('serviceWorker' in navigator) {
    navigator.serviceWorker.register('/service-worker.js');
  }
</script>
```

---

### **4. Manifest instalace**  

**`manifest.json`** definuje:  
- `name`, `short_name`: Název aplikace.  
- `start_url`: Výchozí cesta po spuštění.  
- `icons`: Ikony pro různé rozlišení.  
- `display`: Režim zobrazení (`standalone`, `fullscreen`).  
- `theme_color`, `background_color`: Barvy pro vizuální integraci.  

**Příklad:**  
```json
{
  "name": "Moje Blazor PWA",
  "short_name": "PWA",
  "start_url": "/",
  "icons": [
    { "src": "icon-192.png", "type": "image/png", "sizes": "192x192" },
    { "src": "icon-512.png", "type": "image/png", "sizes": "512x512" }
  ],
  "display": "standalone",
  "theme_color": "#2c3e50",
  "background_color": "#ffffff"
}
```

---

### **5. Offline podpora**  

#### **Service Worker**  

- **Cachování zdrojů**: Při prvním načtení uloží klíčové soubory (HTML, CSS, DLL).  
- **Strategie**:  
  - **Cache First**: Použije cache, pokud je dostupná.  
  - **Network First**: Aktualizuje data ze sítě, při chybě použije cache.  

#### **Úprava service-worker.js**  

Výchozí šablona automaticky cachuje:  
- Statické soubory (`index.html`, `css`, `js`).  
- Sestavení Blazor (`_framework/blazor.webassembly.js`).  

**Rozšíření offline režimu**:  
- Přidejte vlastní cachování API volání nebo dynamických dat.  

---

### **6. Nabízená oznámení (Push Notifications)**  

#### **Krok 1: Požádání o povolení** 

```csharp
// V C# pomocí JS interop
var permission = await JSRuntime.InvokeAsync<string>("requestNotificationPermission");
```  
```javascript
// V JS
function requestNotificationPermission() {
  return Notification.requestPermission();
}
```

#### **Krok 2: Registrace Push API**  

- Vyžaduje backend pro generování klíčů VAPID a odesílání oznámení.  
- Příklad: Použijte knihovnu **Firebase Cloud Messaging** nebo vlastní řešení.  

#### **Krok 3: Zpracování oznámení v Service Workeru**  

```javascript
// V service-worker.js
self.addEventListener('push', event => {
  const data = event.data.json();
  event.waitUntil(
    self.registration.showNotification(data.title, { body: data.body })
  );
});
```

---

### **7. Upozornění pro offline PWA**  

#### **Detekce offline stavu**  

```csharp
// Pomocí JS interop
var isOnline = await JSRuntime.InvokeAsync<bool>("isOnline");
```  
```javascript
function isOnline() {
  return navigator.onLine;
}
```

#### **Zobrazení varování**  

- Vytvořte komponentu `OfflineAlert.razor`:  
  ```razor
  @if (!IsOnline)
  {
      <div class="alert alert-warning">Aplikace je offline!</div>
  }
  @code {
      [Inject] IJSRuntime JSRuntime { get; set; }
      private bool IsOnline = true;

      protected override async Task OnInitializedAsync() {
          IsOnline = await JSRuntime.InvokeAsync<bool>("isOnline");
          // Aktualizujte při změně stavu
          JSRuntime.InvokeVoidAsync("registerOnlineStatusHandler", 
              DotNetObjectReference.Create(this));
      }

      [JSInvokable]
      public void UpdateOnlineStatus(bool isOnline) {
          IsOnline = isOnline;
          StateHasChanged();
      }
  }
  ```

---

### **8. Omezení**  

- **HTTPS**: PWA vyžaduje HTTPS v produkčním prostředí.  
- **Browser Support**: Ne všechny prohlížeče plně podporují PWA (např. Safari má omezení).  
- **Velikost aplikace**: Offline cachování může zvýšit spotřebu paměti.  

---

### **9. Doporučené postupy** 

- **Testujte offline scénáře**: Simulujte síťové chyby (Chrome DevTools -> Network -> Offline).  
- **Optimalizujte manifest**: Přidejte všechny potřebné ikony a metadata pro instalaci.  
- **Aktualizujte service worker**: Po nasazení nové verze informujte uživatele.  
- **Používejte kompresi**: Brotli/GZIP pro statické soubory.  

---

### **10. Typické chyby**  

- **Chybějící HTTPS**: Service worker nefunguje na HTTP (kromě localhostu).  
- **Neaktualizovaný service worker**: Změny v `service-worker.js` vyžadují novou registraci.  
- **Nedostatečné cachování**: Chybějící soubory v offline režimu.  

---

### **11. Shrnutí**  

Blazor PWA umožňuje vytvářet aplikace s nativními funkcemi přímo v .NET:  
- **Instalovatelnost**: Nastavte `manifest.json`.  
- **Offline režim**: Service worker cachuje klíčové zdroje.  
- **Push oznámení**: Vyžadují integraci s backendem a JS interop.  
- **Offline detekce**: Komunikujte stav sítě s uživatelem.  

Vždy testujte na cílových prohlížečích a dbejte na bezpečnost (HTTPS). PWA v Blazor je ideální pro aplikace vyžadující offline práci a vyšší uživatelský komfort.
