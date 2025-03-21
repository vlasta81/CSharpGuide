
## **Zpracování událostí v aplikacích ASP.NET Core Blazor**

Události (kliknutí, změna hodnoty, stisk klávesy) jsou v Blazor zpracovávány pomocí **C# metod**, nikoli JavaScriptu. Umožňují reakci na interakci uživatele s UI komponentami.

---

### **1. Základní syntaxe**  

- Události se vážou pomocí direktivy **`@on{název_události}`** (např. `@onclick`, `@oninput`).  
- Metoda může být **synchronní** nebo **asynchronní** (`async`).  

**Příklad**:  
```razor  
<button @onclick="HandleClick">Klikni</button>

@code {
    private void HandleClick()
    {
        Console.WriteLine("Tlačítko bylo kliknuto!");
    }
}
```

---

### **2. Typy událostí a argumenty**  

#### **a) Běžné události**  

| **Událost**       | **Popis**                         | **Argument**               |  
|--------------------|-----------------------------------|----------------------------|  
| `@onclick`         | Kliknutí myší                     | `MouseEventArgs`           |  
| `@onchange`        | Změna hodnoty (input, select)     | `ChangeEventArgs`          |  
| `@onkeydown`       | Stisk klávesy                     | `KeyboardEventArgs`        |  
| `@oninput`         | Změna hodnoty v reálném čase      | `ChangeEventArgs`          |  

**Příklad s argumenty**:  
```razor  
<input @oninput="HandleInput" />

@code {
    private void HandleInput(ChangeEventArgs e)
    {
        string hodnota = e.Value.ToString();
        Console.WriteLine($"Zadaná hodnota: {hodnota}");
    }
}
```

#### **b) Asynchronní zpracování**  

```razor  
<button @onclick="HandleClickAsync">Načti data</button>

@code {
    private async Task HandleClickAsync()
    {
        var data = await HttpClient.GetFromJsonAsync(...);
        // ...
    }
}
```

---

### **3. Lambda výrazy v událostech**  

- Pro jednoduché akce lze použít **lambda výrazy přímo v markupu**.  
```razor  
<button @onclick="() => Console.WriteLine("Klik!")">OK</button>
```

---

### **4. Zabránění výchozímu chování**  

- **`@preventDefault`**: Zabrání výchozí akci prohlížeče (např. odeslání formuláře).  
```razor  
<form @onsubmit="HandleSubmit" @preventDefault>
    <button type="submit">Odeslat</button>
</form>
```

---

### **5. Zastavení propagace události**  

- **`@stopPropagation`**: Zastaví šíření události do nadřazených prvků (např. kliknutí uvnitř vnořeného elementu).  
```razor  
<div @onclick="ParentClick">
    <button @onclick="ChildClick" @stopPropagation>Klikni</button>
</div>
```

---

### **6. Vlastní události v komponentách** 

- **`EventCallback`**: Umožňuje definovat vlastní události pro přizpůsobené komponenty.  
**Příklad**:  
```razor  
<!-- Dětská komponenta -->
<button @onclick="() => OnClick.InvokeAsync(Value)">Odeslat</button>

@code {
    [Parameter]
    public string Value { get; set; }

    [Parameter]
    public EventCallback<string> OnClick { get; set; }
}

<!-- Rodičovská komponenta -->
<ChildComponent Value="Ahoj" OnClick="HandleChildClick" />

@code {
    private void HandleChildClick(string value)
    {
        Console.WriteLine($"Přijato: {value}");
    }
}
```

---

### **7. Omezení a doporučení** 

- **Výkon**: Nepoužívejte blokující operace v synchronních událostech (např. `Thread.Sleep`).  
- **Stav UI**: Po změně dat volejte `StateHasChanged()`, pokud nedojde k automatické aktualizaci.  
- **Chyby**: Ošetřujte výjimky v asynchronních událostech pomocí `try-catch`.  

---

### **8. Časté chyby**  

| **Chyba**                          | **Řešení**                                  |  
|------------------------------------|---------------------------------------------|  
| Zapomenutí `async` u metody        | Přidejte `async Task` místo `void`.         |  
| Nesprávný název události          | Použijte správný formát (např. `@onclick`). |  
| Nevolání `StateHasChanged()`       | Zavolejte metodu po změně stavu.            |  

---

### **Shrnutí**  

| **Prvek**               | **Popis**                                                                 |  
|-------------------------|---------------------------------------------------------------------------|  
| **Direktivy událostí**  | `@on{událost}`, např. `@onclick`, `@onkeydown`.                           |  
| **Argumenty**           | Typy jako `MouseEventArgs`, `ChangeEventArgs`.                            |  
| **Asynchronní zpracování** | Používejte `async Task` pro operace s `await`.                          |  
| **Vlastní události**    | Definujte pomocí `EventCallback<T>`.                                      |  

**Klíčové výhody**:  
- Jednotný kód v C# pro logiku i UI.  
- Podpora asynchronních operací pro plynulé uživatelské rozhraní.  

**Užitečné zdroje**:  
- Dokumentace: [Blazor Event Handling](https://learn.microsoft.com/cs-cz/aspnet/core/blazor/components/event-handling).  
- Ukázky: [Blazor Events Samples](https://github.com/dotnet/blazor-samples).
