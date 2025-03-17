
## **Správa relací a stavu v ASP.NET Core MVC**  

Metody pro uchovávání uživatelských dat mezi požadavky.

---

### **1. Úvod**  

- **Stav aplikace**: Data specifická pro konkrétního uživatele nebo relaci (např. košík, přihlášení).  
- **HTTP je bezstavový**: Každý požadavek je nezávislý. Pro uchování stavu se používají externí mechanismy.  

---

### **2. Metody správy stavu**  

#### **a) Cookies**  

- **Malá data uložená v prohlížeči** (max 4 KB).  
- **Typy**:  
  - **Session cookies**: Platné do zavření prohlížeče.  
  - **Persistentní cookies**: Platné do expirace (`Expires`).  
- **Použití**: Uchování preferencí, tokenů přihlášení.  
- **Bezpečnostní atributy**:  
  - `HttpOnly`: Blokuje přístup přes JavaScript.  
  - `Secure`: Odesílá pouze přes HTTPS.  
  - `SameSite`: Omezuje cross-site requesty.  

**Příklad (nastavení cookie):**  
```csharp  
Response.Cookies.Append("uzivatel", "Jan", new CookieOptions  
{  
    Expires = DateTime.Now.AddDays(7),  
    HttpOnly = true,  
    Secure = true  
});  
```  

**Čtení cookie:**  
```csharp  
var hodnota = Request.Cookies["uzivatel"];  
```  

---

#### **b) Session**  

- **Data uložená na serveru**, identifikovaná session ID (uloženým v cookie).  
- **Konfigurace**:  
  1. Přidejte službu v `Program.cs`:  
     ```csharp  
     builder.Services.AddSession(options =>  
     {  
         options.IdleTimeout = TimeSpan.FromMinutes(20);  
         options.Cookie.HttpOnly = true;  
     });  
     ```  
  2. Povolte middleware:  
     ```csharp  
     app.UseSession();  
     ```  

**Příklad (práce se session):**  
```csharp  
// Uložení  
HttpContext.Session.SetString("Jmeno", "Anna");  

// Čtení  
var jmeno = HttpContext.Session.GetString("Jmeno");  
```  

- **Ukládání komplexních objektů**: Serializace do JSON nebo binárního formátu.  

---

#### **c) TempData**  

- **Dočasná data přežívající jeden přesměrování** (pomocí cookies nebo session).  
- **Použití**: Předání stavu mezi akcemi (např. chybové zprávy).  

**Příklad:**  
```csharp  
// V akci  
TempData["Zprava"] = "Úspěšně uloženo!";  

// Ve view  
@if (TempData["Zprava"] != null)  
{  
    <div>@TempData["Zprava"]</div>  
}  
```  

---

#### **d) Query String a Hidden Fields**  

- **Query String**: Data v URL (např. `?id=5`).  
  - Rychlé, ale viditelné a nebezpečné pro citlivá data.  
- **Hidden Fields**: Skrytá pole ve formulářích.  
  - `<input type="hidden" name="token" value="abc" />`.  

---

### **3. Konfigurace session storage**  

- **Ve výchozím stavu**: Data se ukládají do paměti (neodolné proti restartu).  
- **Distribuovaná úložiště**:  
  - **SQL Server**: `AddSqlServerCache()`.  
  - **Redis**: `AddStackExchangeRedisCache()`.  
  - **Soubory**: `AddDistributedMemoryCache()`.  

---

### **4. Bezpečnostní aspekty**  

- **Cookies**:  
  - Nikdy neukládejte citlivá data (hesla) do cookies.  
  - Vždy nastavte `Secure` a `HttpOnly`.  
- **Session Fixation**: Regenerujte session ID po přihlášení.  
- **GDPR**: Vyžaduje souhlas uživatele pro ukládání cookies.  

---

### **5. Srovnání metod**  

| **Metoda**       | **Kapacita** | **Doba platnosti**      | **Bezpečnost**         |  
|------------------|--------------|-------------------------|------------------------|  
| Cookies          | 4 KB         | Dle nastavení           | Střední (client-side)  |  
| Session          | Neomezená*   | Do konce relace         | Vysoká (server-side)   |  
| TempData         | 4 KB         | 1 přesměrování          | Střední                |  
| Query String     | Dle URL      | Okamžitá                | Nízká                  |  

*Závisí na úložišti (paměť, SQL Server, Redis).

---

### **6. Příklady**  

#### **Uložení košíku do session**  

```csharp  
public class Kosik  
{  
    public List<Produkt> Produkty { get; set; }  
}  

// Uložení  
var kosik = new Kosik { Produkty = produkty };  
HttpContext.Session.Set("Kosik", kosik);  

// Čtení  
var kosik = HttpContext.Session.Get<Kosik>("Kosik");  
```  

---

### **7. Shrnutí**  

- **Cookies**: Ideální pro malá data a dlouhodobé preference.  
- **Session**: Bezpečné pro větší nebo citlivá data (vyžaduje server-side úložiště).  
- **TempData**: Rychlé řešení pro jednorázové zprávy mezi akcemi.  
- **Query String/Hidden Fields**: Pro jednoduché scénáře bez bezpečnostních rizik.  

**Doporučení**:  
- Pro citlivá data vždy používejte server-side úložiště.  
- Respektujte GDPR při práci s cookies.  
- Testujte časové limity session a cookies.  

---

**Doporučené zdroje**:  
- Oficiální dokumentace: *Session and state management in ASP.NET Core*  
