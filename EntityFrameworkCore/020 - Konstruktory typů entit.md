
### Konstruktory typů entit v Entity Framework Core

---

#### **1. Co jsou konstruktory typů entit?**  

- **Definice**: Entity v EF Core mohou mít **vlastní konstruktory**, které se používají při vytváření instancí během dotazování nebo operací.  
- **Účel**:  
  - Zajištění **validního stavu entity** při vytvoření.  
  - Inicializace povinných vlastností nebo vztahů.  
  - Podpora **neměnitelných (immutable) entit** (vlastnosti pouze pro čtení).  

---

#### **2. Klíčové vlastnosti**  

1. **Parametrizované konstruktory**:  
   - EF Core dokáže použít konstruktor s parametry odpovídajícími vlastnostem entity.  
2. **Privátní konstruktory**:  
   - Lze použít pro omezení vytváření instancí mimo EF Core.  
3. **Inicializace vazeb (binding)**:  
   - Parametry konstruktoru se mapují na sloupce nebo navigační vlastnosti.  

---

#### **3. Příklad entity s konstruktorem**  

```csharp  
public class Student  
{  
    // Vlastnosti  
    public int Id { get; private set; }  
    public string Jmeno { get; private set; }  
    public int Vek { get; private set; }  

    // Konstruktor pro EF Core  
    private Student() { } // Privátní pro interní použití EF Core  

    // Veřejný konstruktor pro aplikaci  
    public Student(string jmeno, int vek)  
    {  
        Jmeno = jmeno ?? throw new ArgumentNullException(nameof(jmeno));  
        Vek = vek >= 0 ? vek : throw new ArgumentException("Věk nesmí být záporný.");  
    }  
}  
```  

---

#### **4. Konfigurace konstruktorů**  

- **Fluent API**:  
  ```csharp  
  protected override void OnModelCreating(ModelBuilder modelBuilder)  
  {  
      modelBuilder.Entity<Student>(entity =>  
      {  
          entity.HasKey(s => s.Id);  
          entity.Property(s => s.Jmeno).IsRequired();  
          entity.Property(s => s.Vek).IsRequired();  

          // Explicitní konfigurace konstruktoru (EF Core 7+)  
          entity.HasConstructorBinding(  
              typeof(Student).GetConstructor(new[] { typeof(string), typeof(int) }),  
              new[] { nameof(Student.Jmeno), nameof(Student.Vek) }  
          );  
      });  
  }  
  ```  

- **Požadavky**:  
  - Názvy parametrů konstruktoru musí odpovídat názvům vlastností nebo sloupců (lze přepsat konfigurací).  
  - Pokud existuje více konstruktorů, EF Core vybere ten s největší shodou parametrů.  

---

#### **5. Výhody použití konstruktorů**  

- **Validace dat**: Zajištění, že entity nelze vytvořit v neplatném stavu.  
- **Encapsulace**: Vlastnosti mohou mít privátní settery.  
- **Immutabilita**: Vytvoření entit pouze s požadovanými daty.  

---

#### **6. Běžné scénáře**  

1. **Neměnitelné entity**:  
   ```csharp  
   public class Adresa  
   {  
       public string Ulice { get; }  
       public string Mesto { get; }  

       public Adresa(string ulice, string mesto)  
       {  
           Ulice = ulice;  
           Mesto = mesto;  
       }  
   }  
   ```  

2. **Vytváření entit s vazbami**:  
   ```csharp  
   public class Objednavka  
   {  
       public int Id { get; }  
       public Produkt Produkt { get; }  
       public int Mnozstvi { get; }  

       public Objednavka(Produkt produkt, int mnozstvi)  
       {  
           Produkt = produkt ?? throw new ArgumentNullException(nameof(produkt));  
           Mnozstvi = mnozstvi > 0 ? mnozstvi : throw new ArgumentException("Množství musí být kladné.");  
       }  
   }  
   ```  

---

#### **7. Omezení a problémy**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|---------------------------------------------|  
| Parametry konstruktoru neodpovídají vlastnostem | Použijte `HasConstructorBinding` v Fluent API. |  
| Chybějící privátní konstruktor       | EF Core vyžaduje přístup k konstruktoru (alespoň privátnímu). |  
| Neinicializované navigační vlastnosti | Inicializujte je v konstruktoru nebo přes `Include()`. |  

---

#### **8. Doporučené postupy**  

1. **Používejte konstruktory pro validaci**: Zabraňte neplatným stavům entit.  
2. **Privátní konstruktor pro EF Core**: Chraňte logiku vytváření entit.  
3. **Konfigurace přes Fluent API**: Pro explicitní mapování parametrů.  

---

**Shrnutí**:  
- Konstruktory entit umožňují **kontrolovanou inicializaci** a **validaci dat**.  
- EF Core podporuje **parametrizované i privátní konstruktory**.  
- Pro složitější scénáře použijte Fluent API k explicitní konfiguraci.  

**Zdroje**:  
- [EF Core Dokumentace – Konstruktory entit](https://learn.microsoft.com/en-us/ef/core/modeling/constructors)  
- [EF Core – Immutable Entities](https://learn.microsoft.com/en-us/ef/core/modeling/constructors#immutable-entities)
