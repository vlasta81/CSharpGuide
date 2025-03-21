
## **Zachování stavu prvků, komponent a modelů v ASP.NET Core Blazor pomocí `@key`**

---

### **1. Úvod do `@key`**  

- **Definice**: Direktiva `@key` slouží k jednoznačné identifikaci prvků nebo komponent v dynamických kolekcích (např. seznamech) nebo při podmíněném renderování.  
- **Účel**:  
  - Umožňuje Blazoru efektivně sledovat změny v UI.  
  - Zachovává stav prvků/komponent (např. hodnoty formulářů, pozice v DOM).  

---

### **2. Jak `@key` funguje?**  

- Blazor při renderování porovnává starý a nový stav UI (diffing algoritmus).  
- **Bez `@key`**: Blazor identifikuje prvky podle pozice v kolekci → při změně pořadí může dojít k přerušení stavu.  
- **S `@key`**: Blazor identifikuje prvky podle unikátního klíče → zachovává jejich stav i při změnách pořadí.  

---

### **3. Kdy použít `@key`?**  

- **Dynamické seznamy** (např. `@foreach` s přidáváním/odebíráním položek).  
- **Podmíněné renderování** (např. `@if` nebo `@switch` s měnícími se komponentami).  
- **Komponenty s vnitřním stavem** (např. formuláře, které nesmí ztratit data při aktualizaci rodiče).  

---

### **4. Syntaxe a příklady**  

#### **a) Seznam prvků s `@key`**  

```razor  
@foreach (var item in Items)
{
    <div @key="item.Id"> <!-- Unikátní klíč (např. ID modelu) -->
        <input @bind="item.Text" />
    </div>
}
```  
**Výhoda**: Pokud se přidá nová položka na začátek seznamu, stávající prvky neztratí stav (např. hodnoty inputů).  

#### **b) Podmíněné komponenty**  

```razor  
@if (ShowComponent)
{
    <MyComponent @key="currentId" /> <!-- Klíč zajišťuje novou instanci při změně -->
}
```  

#### **c) Zachování stavu komponenty**  

```razor  
<ChildComponent @key="User.Id" User="@User" />
```  
- Při změně `User.Id` se vytvoří nová instance komponenty (starý stav se resetuje).  
- Bez `@key` by komponenta pouze aktualizovala vlastnosti, ale zachovala stav.  

---

### **5. Doporučené postupy**  

- **Používejte unikátní a stabilní klíče**:  
  - **Špatně**: `@key="index"` (index v kolekci není stabilní při změnách pořadí).  
  - **Správně**: `@key="item.Id"` (unikátní identifikátor modelu).  
- **Vyhněte se zbytečnému použití**: Nepoužívejte `@key` na statické kolekce bez změn pořadí.  

---

### **6. Časté chyby**  

| **Chyba**                                | **Důsledek**                              | **Řešení**                          |  
|------------------------------------------|-------------------------------------------|--------------------------------------|  
| Klíč není unikátní                       | Neočekávané chování při změnách kolekce.  | Použijte unikátní hodnoty (ID).      |  
| Klíč založený na indexu seznamu          | Ztráta stavu při přerovnání položek.      | Nahraďte index identifikátorem modelu.|  
| Zbytečné použití `@key`                  | Zvýšení režie bez přínosu.                | Používejte pouze tam, kde je potřeba.|  

---

### **7. Výkonové aspekty**  

- **Správné použití `@key`**: Minimalizuje počet operací v DOM → zlepšuje výkon.  
- **Nesprávné použití**: Může vést k častému vytváření/rušení prvků → snížení výkonu.  

---

### **8. Ukázka: Zachování stavu formuláře**  

```razor  
@foreach (var user in Users)
{
    <div @key="user.Id">
        <input @bind="user.Name" />
        <input @bind="user.Email" />
    </div>
}

<button @onclick="AddUser">Přidat uživatele</button>

@code {
    private List<User> Users = new();

    private void AddUser()
    {
        // Přidání nového uživatele na začátek seznamu
        Users.Insert(0, new User { Id = Guid.NewGuid() });
    }
}
```  
**Výsledek**: Bez `@key` by se při přidání nového uživatele resetovaly hodnoty existujících inputů. S `@key` zůstane stav zachován.

---

### **Shrnutí**  

| **Aspekt**               | **Popis**                                                                 |  
|--------------------------|---------------------------------------------------------------------------|  
| **Účel `@key`**          | Zachování stavu prvků/komponent při dynamických změnách.                  |  
| **Doporučené klíče**     | Unikátní a stabilní identifikátory (ID modelu).                           |  
| **Výkon**                | Správné použití zlepšuje efektivitu renderování.                          |  
| **Typické scénáře**      | Seznamy s možností přidávání/odebírání, formuláře s vnitřním stavem.      |  

**Klíčové poučení**:  
- `@key` je nezbytný pro aplikace s dynamickým obsahem, kde je důležitá konzistence stavu.  
- Vždy používejte smysluplné klíče založené na datech, nikoli na pozici v kolekci.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor @key Directive](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/#key).  
