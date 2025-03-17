
### Podkladová pole (Backing Fields) v Entity Framework Core

---

#### **1. Co jsou podkladová pole?**  

- **Definice**: Privátní pole v entitě, které EF Core používá pro **ukládání dat** místo veřejných vlastností.  
- **Účel**:  
  - Encapsulace stavu entity (kontrola přístupu k datům).  
  - Podpora logiky validace nebo transformace při čtení/zápisu hodnot.  
  - Umožnění použití **read-only vlastností** nebo vlastností s privátním setterem.  

---

#### **2. Jak fungují?**  

- EF Core přeskočí veřejné vlastnosti a **pracuje přímo s podkladovým polem**.  
- Vlastnosti mohou obsahovat dodatečnou logiku (např. validaci), ale data se ukládají do privátního pole.  

**Příklad entity**:  
```csharp  
public class Student  
{  
    private string _jmeno; // Podkladové pole  

    public int Id { get; set; }  

    public string Jmeno // Veřejná vlastnost  
    {  
        get => _jmeno;  
        set => _jmeno = value?.Trim(); // Automatické ořezání mezer  
    }  
}  
```  

---

#### **3. Konfigurace podkladových polí**  

- **Data Annotations**:  
  ```csharp  
  public class Student  
  {  
      private string _jmeno;  

      [BackingField(nameof(_jmeno))]  
      public string Jmeno { get; set; }  
  }  
  ```  

- **Fluent API** (doporučeno pro větší kontrolu):  
  ```csharp  
  protected override void OnModelCreating(ModelBuilder modelBuilder)  
  {  
      modelBuilder.Entity<Student>()  
          .Property(s => s.Jmeno)  
          .HasField("_jmeno") // Nastavení podkladového pole  
          .UsePropertyAccessMode(PropertyAccessMode.Field); // Přímo pracuj s polem  
  }  
  ```  

---

#### **4. Kdy použít podkladová pole?**  

| **Scénář**                     | **Výhoda**                                  |  
|--------------------------------|--------------------------------------------|  
| **Read-only vlastnosti**        | Umožní EF Core ukládat data bez veřejného setteru. |  
| **Validace/transformace dat**  | Např. ořezání mezer, formátování hodnot.   |  
| **Kompatibilita s legacy kódem** | Mapování privátních polí z existující databáze. |  

---

#### **5. Pokročilé techniky**  

- **Přístup k podkladovým polím v LINQ**:  
  ```csharp  
  var studenti = context.Students  
      .Where(s => EF.Property<string>(s, "_jmeno") == "Jan")  
      .ToList();  
  ```  

- **Implicitní podkladová pole**:  
  - Pokud vlastnost nemá setter, EF Core automaticky hledá pole se stejným názvem v _camelCase_ nebo s prefixem `_`.  

---

#### **6. Běžné problémy**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|---------------------------------------------|  
| Chyba při migraci (`InvalidOperationException`) | Explicitně nakonfigurujte pole přes Fluent API. |  
| Neočekávané `null` hodnoty           | Ověřte, zda podkladové pole není `readonly`. |  
| Nezmapované vlastnosti               | Použijte `[NotMapped]` pro vlastnosti bez podkladového pole. |  

---

#### **7. Doporučené postupy**  

1. **Fluent API pro konfiguraci**: Zajistí explicitní a čitelnou správu polí.  
2. **Komentáře v kódu**: Vysvětlete účel podkladových polí pro budoucí vývojáře.  
3. **Testování**: Ověřte chování při čtení/zápisu hodnot.  

---

**Shrnutí**:  
- Podkladová pole umožňují **plnou kontrolu nad ukládáním dat** v entitách.  
- Jsou klíčová pro implementaci read-only vlastností nebo vlastní logiky při přístupu k datům.  
- Konfigurují se přes **Data Annotations** nebo **Fluent API**.  

**Zdroje**:  
- [EF Core Dokumentace – Podkladová pole](https://learn.microsoft.com/en-us/ef/core/modeling/backing-field)  
- [EF Core Property Access Modes](https://learn.microsoft.com/en-us/ef/core/modeling/backing-field#accessing-fields)
