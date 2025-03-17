
## **Přehled Razor Syntaxe v ASP.NET Core MVC**  

Základy kombinace HTML a C# pro dynamické generování obsahu.

---

### **1. Co je Razor?**  

- **Templatovací engine** pro kombinování HTML s C# kódem.  
- **Použití**: Vytváření dynamických webových stránek (soubory `.cshtml`).  
- **Výhody**: Čitelnost, flexibilita, podpora IntelliSense v IDE.  

---

### **2. Základní Syntaxe**  

#### **a) Inline C# výrazy**  

- Použití `@` pro vložení C# kódu do HTML:  
  ```html  
  <p>Aktuální čas: @DateTime.Now.ToString("HH:mm")</p>  
  ```  
- **Výstup hodnoty**: `@variable`, `@Metoda()`, `@Model.Vlastnost`.  

#### **b) Kódové bloky**  

- Víceřádkový C# kód v `@{ ... }`:  
  ```html  
  @{  
      var jmeno = "Karel";  
      var cislo = 42;  
  }  
  <p>@jmeno má číslo @cislo</p>  
  ```  

#### **c) Smíšený obsah**  

- Kombinace HTML a C# s `@:` nebo `<text>`:  
  ```html  
  @if (DateTime.Now.Hour < 12)  
  {  
      @: Dobré ráno!  
  }  
  else  
  {  
      <text>Dobrý den!</text>  
  }  
  ```  

---

### **3. Direktivy**  

- **@model**: Určuje typ modelu předaného do View.  
  ```html  
  @model List<Uzivatel>  
  ```  
- **@using**: Importuje namespace.  
  ```html  
  @using MojeAplikace.Models  
  ```  
- **@inject**: Injektuje službu do View (např. `@inject IEmailService Email`).  

---

### **4. Řídicí struktury**  

#### **a) Podmínky**  

```html  
@if (Model.Pocet > 0)  
{  
    <p>Počet: @Model.Pocet</p>  
}  
else  
{  
    <p>Žádné položky.</p>  
}  
```  

#### **b) Cykly**  

```html  
<ul>  
@foreach (var uzivatel in Model)  
{  
    <li>@uzivatel.Jmeno (@uzivatel.Email)</li>  
}  
</ul>  
```  

#### **c) Switch**  

```html  
@switch (Model.Status)  
{  
    case "Active":  
        <span class="active">Aktivní</span>  
        break;  
    case "Inactive":  
        <span class="inactive">Neaktivní</span>  
        break;  
}  
```  

---

### **5. Tag Helpers**  

- **Rozšíření HTML elementů** pro interakci s C# kódem.  
- Příklad formuláře:  
  ```html  
  <form asp-action="Ulozit" method="post">  
      <input asp-for="Jmeno" class="form-control" />  
      <span asp-validation-for="Jmeno"></span>  
      <button type="submit">Uložit</button>  
  </form>  
  ```  

---

### **6. Sekce (Sections)**  

- **Definice v Layoutu**:  
  ```html  
  @RenderSection("Scripts", required: false)  
  ```  
- **Implementace ve View**:  
  ```html  
  @section Scripts {  
      <script>alert("Ahoj!");</script>  
  }  
  ```  

---

### **7. Escaping a Komentáře**  

- **Escaping `@`**: Použijte `@@`.  
  ```html  
  <p>Email: petr@@example.com</p>  
  ```  
- **Komentáře**:  
  ```html  
  @* Toto je komentář v Razor *@  
  <!-- Toto je HTML komentář (viditelný v prohlížeči) -->  
  ```  

---

### **8. Časté chyby**  

- **Záměna `@Model` a `@model`**:  
  - `@model` definuje typ modelu (direktiva).  
  - `@Model` přistupuje k instanci modelu.  
- **Chybějící středníky v kódových blocích**.  
- **Špatné uzavření složených závorek** (`{}`).  

---

### **9. Příklady**  

#### **Komplexní View s modelem**  

```html  
@model Uzivatel  
@{  
    ViewData["Title"] = "Profil uživatele";  
}  

<h1>@Model.Jmeno</h1>  
@if (Model.JeAdmin)  
{  
    <p class="alert alert-warning">Toto je administrátorský účet.</p>  
}  

<ul>  
    @for (int i = 0; i < Model.Pocty.Length; i++)  
    {  
        <li>Počet @(i + 1): @Model.Pocty[i]</li>  
    }  
</ul>  
```  

---

### **10. Shrnutí**  

- Razor spojuje **HTML a C#** pro dynamické generování obsahu.  
- **@** slouží k vložení kódu, **@{ ... }** pro víceřádkové bloky.  
- **Tag Helpers** zjednodušují tvorbu interaktivních prvků.  
- **Sekce** umožňují rozšíření layoutů o specifické zdroje.  
- Dodržování konvencí zajišťuje **přehlednost a údržbu** kódu.  

--- 

**Doporučené zdroje**:  
- Oficiální dokumentace: *Razor syntax reference*  
