
### **1. Definice abstraktní třídy**  

- **Nelze instancovat**: Slouží pouze jako **základ pro odvozené třídy**.  
- **Účel**: Definuje společnou strukturu a částečnou implementaci pro podtřídy.  
- **Klíčové vlastnosti**:  
  - Může obsahovat **abstraktní metody** (bez implementace) i **konkrétní metody** (s implementací).  
  - Může mít **pole, vlastnosti, konstruktory**.  
  - Používá klíčové slovo `abstract`.

---

### **2. Syntaxe a příklad**  

```csharp
public abstract class Zvire 
{
    // Abstraktní metoda (nemá tělo)
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

### **3. Klíčové vlastnosti**  

#### **a) Abstraktní metody**  

- **Nemají implementaci**: Jen signaturu.  
- **Odvozené třídy je musí přepsat** (pomocí `override`).  
- Nelze označit jako `private` nebo `static`.

#### **b) Konkrétní metody**  

- Mohou obsahovat výchozí implementaci.  
- Lze je **přepsat** v odvozených třídách (s `virtual` nebo `override`).

#### **c) Konstruktory**  

- Abstraktní třídy **mají konstruktory**, které volají odvozené třídy.  
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

| **Vlastnost**       | **Abstraktní třída**      | **Rozhraní**             |  
|----------------------|---------------------------|--------------------------|  
| **Implementace**     | Může obsahovat kód        | Pouze signatury metod    |  
| **Dědičnost**        | Jedna třída               | Více rozhraní            |  
| **Pole/Vlastnosti**  | Ano (i nekonstantní)      | Jen automatické vlastnosti |  
| **Konstruktory**     | Ano                       | Ne                       |  

---

### **5. Kdy použít abstraktní třídu?**  

1. **Sdílení kódu mezi podtřídami**: Např. základní funkcionalita pro `Zvire`.  
2. **Vynucení společného chování**: Nutnost implementace abstraktních metod.  
3. **Částečná implementace**: Kombinace hotových a nedokončených metod.

---

### **6. Běžné chyby**  

- **Pokus o instanci abstraktní třídy**:  
  ```csharp
  Zvire zvire = new Zvire(); // Chyba kompilace!
  ```  
- **Zapomenuté přepsání abstraktní metody**:  
  ```csharp
  public class Kocka : Zvire { } // Chyba: Neimplementuje VydavejZvuk()
  ```  
- **Použití `virtual` u abstraktních metod**: Abstraktní metody nelze označit jako `virtual` (implicitně virtuální).

---

### **7. Ukázky kódu**  

#### **Abstraktní třída s vlastnostmi**  

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

#### **Abstraktní třída s konkrétními metodami** 

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

### **8. Doporučené zdroje**  

- **Oficiální dokumentace**: [Abstract Classes in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/keywords/abstract)  
