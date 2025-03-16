
### **1. Definice abstraktní tøídy**  

- **Nelze instancovat**: Slouží pouze jako **základ pro odvozené tøídy**.  
- **Úèel**: Definuje spoleènou strukturu a èásteènou implementaci pro podtøídy.  
- **Klíèové vlastnosti**:  
  - Mùže obsahovat **abstraktní metody** (bez implementace) i **konkrétní metody** (s implementací).  
  - Mùže mít **pole, vlastnosti, konstruktory**.  
  - Používá klíèové slovo `abstract`.

---

### **2. Syntaxe a pøíklad**  

```csharp
public abstract class Zvire 
{
    // Abstraktní metoda (nemá tìlo)
    public abstract void VydavejZvuk();

    // Konkrétní metoda (má implementaci)
    public void Spi() 
    {
        Console.WriteLine("Zzz...");
    }
}

public class Pes : Zvire 
{
    // Implementace abstraktní metody
    public override void VydavejZvuk() 
    {
        Console.WriteLine("Haf!");
    }
}
```

---

### **3. Klíèové vlastnosti**  

#### **a) Abstraktní metody**  

- **Nemají implementaci**: Jen signaturu.  
- **Odvozené tøídy je musí pøepsat** (pomocí `override`).  
- Nelze oznaèit jako `private` nebo `static`.

#### **b) Konkrétní metody**  

- Mohou obsahovat výchozí implementaci.  
- Lze je **pøepsat** v odvozených tøídách (s `virtual` nebo `override`).

#### **c) Konstruktory**  

- Abstraktní tøídy **mají konstruktory**, které volají odvozené tøídy.  
```csharp
public abstract class Tvar 
{
    protected Tvar(string nazev) 
    {
        Nazev = nazev;
    }
    
    public string Nazev { get; }
}

public class Kruh : Tvar 
{
    public Kruh() : base("Kruh") { }
}
```

---

### **4. Porovnání s rozhraním (`interface`)** 

| **Vlastnost**       | **Abstraktní tøída**      | **Rozhraní**             |  
|----------------------|---------------------------|--------------------------|  
| **Implementace**     | Mùže obsahovat kód        | Pouze signatury metod    |  
| **Dìdiènost**        | Jedna tøída               | Více rozhraní            |  
| **Pole/Vlastnosti**  | Ano (i nekonstantní)      | Jen automatické vlastnosti |  
| **Konstruktory**     | Ano                       | Ne                       |  

---

### **5. Kdy použít abstraktní tøídu?**  

1. **Sdílení kódu mezi podtøídami**: Napø. základní funkcionalita pro `Zvire`.  
2. **Vynucení spoleèného chování**: Nutnost implementace abstraktních metod.  
3. **Èásteèná implementace**: Kombinace hotových a nedokonèených metod.

---

### **6. Bìžné chyby**  

- **Pokus o instanci abstraktní tøídy**:  
  ```csharp
  Zvire zvire = new Zvire(); // Chyba kompilace!
  ```  
- **Zapomenuté pøepsání abstraktní metody**:  
  ```csharp
  public class Kocka : Zvire { } // Chyba: Neimplementuje VydavejZvuk()
  ```  
- **Použití `virtual` u abstraktních metod**: Abstraktní metody nelze oznaèit jako `virtual` (implicitnì virtuální).

---

### **7. Ukázky kódu**  

#### **Abstraktní tøída s vlastnostmi**  

```csharp
public abstract class Ucet 
{
    public string CisloUctu { get; }
    public decimal Zustatek { get; protected set; }

    protected Ucet(string cislo) 
    {
        CisloUctu = cislo;
    }

    public abstract void Vloz(decimal castka);
    public abstract void Vyber(decimal castka);
}

public class SporiciUcet : Ucet 
{
    public SporiciUcet(string cislo) : base(cislo) { }

    public override void Vloz(decimal castka) 
    {
        Zustatek += castka;
    }

    public override void Vyber(decimal castka) 
    {
        if (Zustatek >= castka) 
        {
            Zustatek -= castka;
        }
    }
}
```

#### **Abstraktní tøída s konkrétními metodami** 

```csharp
public abstract class Logovac 
{
    // Abstraktní metoda
    public abstract void Loguj(string zprava);

    // Konkrétní metoda
    public void LogujChybu(string zprava) 
    {
        Loguj($"CHYBA: {zprava}");
    }
}

public class SouborovyLogovac : Logovac 
{
    public override void Loguj(string zprava) 
    {
        File.AppendAllText("log.txt", zprava);
    }
}
```

---

### **8. Doporuèené zdroje**  

- **Oficiální dokumentace**: [Abstract Classes in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/keywords/abstract)  
