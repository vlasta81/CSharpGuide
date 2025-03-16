
### **1. Definice abstraktn� t��dy**  

- **Nelze instancovat**: Slou�� pouze jako **z�klad pro odvozen� t��dy**.  
- **��el**: Definuje spole�nou strukturu a ��ste�nou implementaci pro podt��dy.  
- **Kl��ov� vlastnosti**:  
  - M��e obsahovat **abstraktn� metody** (bez implementace) i **konkr�tn� metody** (s implementac�).  
  - M��e m�t **pole, vlastnosti, konstruktory**.  
  - Pou��v� kl��ov� slovo `abstract`.

---

### **2. Syntaxe a p��klad**  

```csharp
public abstract class Zvire 
{
    // Abstraktn� metoda (nem� t�lo)
    public abstract void VydavejZvuk();

    // Konkr�tn� metoda (m� implementaci)
    public void Spi() 
    {
        Console.WriteLine("Zzz...");
    }
}

public class Pes : Zvire 
{
    // Implementace abstraktn� metody
    public override void VydavejZvuk() 
    {
        Console.WriteLine("Haf!");
    }
}
```

---

### **3. Kl��ov� vlastnosti**  

#### **a) Abstraktn� metody**  

- **Nemaj� implementaci**: Jen signaturu.  
- **Odvozen� t��dy je mus� p�epsat** (pomoc� `override`).  
- Nelze ozna�it jako `private` nebo `static`.

#### **b) Konkr�tn� metody**  

- Mohou obsahovat v�choz� implementaci.  
- Lze je **p�epsat** v odvozen�ch t��d�ch (s `virtual` nebo `override`).

#### **c) Konstruktory**  

- Abstraktn� t��dy **maj� konstruktory**, kter� volaj� odvozen� t��dy.  
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

### **4. Porovn�n� s rozhran�m (`interface`)** 

| **Vlastnost**       | **Abstraktn� t��da**      | **Rozhran�**             |  
|----------------------|---------------------------|--------------------------|  
| **Implementace**     | M��e obsahovat k�d        | Pouze signatury metod    |  
| **D�di�nost**        | Jedna t��da               | V�ce rozhran�            |  
| **Pole/Vlastnosti**  | Ano (i nekonstantn�)      | Jen automatick� vlastnosti |  
| **Konstruktory**     | Ano                       | Ne                       |  

---

### **5. Kdy pou��t abstraktn� t��du?**  

1. **Sd�len� k�du mezi podt��dami**: Nap�. z�kladn� funkcionalita pro `Zvire`.  
2. **Vynucen� spole�n�ho chov�n�**: Nutnost implementace abstraktn�ch metod.  
3. **��ste�n� implementace**: Kombinace hotov�ch a nedokon�en�ch metod.

---

### **6. B�n� chyby**  

- **Pokus o instanci abstraktn� t��dy**:  
  ```csharp
  Zvire zvire = new Zvire(); // Chyba kompilace!
  ```  
- **Zapomenut� p�eps�n� abstraktn� metody**:  
  ```csharp
  public class Kocka : Zvire { } // Chyba: Neimplementuje VydavejZvuk()
  ```  
- **Pou�it� `virtual` u abstraktn�ch metod**: Abstraktn� metody nelze ozna�it jako `virtual` (implicitn� virtu�ln�).

---

### **7. Uk�zky k�du**  

#### **Abstraktn� t��da s vlastnostmi**  

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

#### **Abstraktn� t��da s konkr�tn�mi metodami** 

```csharp
public abstract class Logovac 
{
    // Abstraktn� metoda
    public abstract void Loguj(string zprava);

    // Konkr�tn� metoda
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

### **8. Doporu�en� zdroje**  

- **Ofici�ln� dokumentace**: [Abstract Classes in C#](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/keywords/abstract)  
