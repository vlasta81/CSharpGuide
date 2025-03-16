
### **1. Definice indexeru**  

Indexer umožòuje **pøistupovat k objektu jako k poli** pomocí indexu. Je to speciální vlastnost, která umožòuje tøídì nebo struktuøe chovat se jako pole (napø. kolekce, seznamy, vlastní datové struktury).  

**Klíèové slovo**: `this`.  

---

### **2. Základní syntaxe**  

```csharp
public class MojSeznam 
{
    private string[] _data = new string[10]; // Privátní pole

    // Indexer s celoèíselným indexem
    public string this[int index] 
    {
        get => _data[index];
        set => _data[index] = value;
    }
}

// Použití:
MojSeznam seznam = new MojSeznam();
seznam[0] = "Ahoj"; // Zápis pøes indexer
Console.WriteLine(seznam[0]); // Ètení: "Ahoj"
```

---

### **3. Vlastnosti indexeru**  

- **Parametry**: Mùže mít jeden nebo více parametrù (i rùzných typù).  
- **Pøístupové metody**: `get` a `set` (podobnì jako u vlastností).  
- **Typ indexu**: Lze použít libovolný typ (napø. `int`, `string`, `enum`).  
- **Pøetìžování**: Tøída mùže mít více indexerù s rùznými parametry.  

---

### **4. Pøíklady použití**  

#### **a) Indexer s více parametry**  

```csharp
public class Matice 
{
    private int[,] _data = new int[3, 3];

    // Indexer pro 2D pole
    public int this[int radek, int sloupec] 
    {
        get => _data[radek, sloupec];
        set => _data[radek, sloupec] = value;
    }
}

// Použití:
Matice matice = new Matice();
matice[0, 0] = 5;
Console.WriteLine(matice[0, 0]); // 5
```

#### **b) Indexer s øetìzcovým klíèem**  

```csharp
public class Slovnik 
{
    private Dictionary<string, string> _data = new Dictionary<string, string>();

    public string this[string klic] 
    {
        get => _data.ContainsKey(klic) ? _data[klic] : null;
        set => _data[klic] = value;
    }
}

// Použití:
Slovnik slovnik = new Slovnik();
slovnik["jméno"] = "Anna";
Console.WriteLine(slovnik["jméno"]); // "Anna"
```

---

### **5. Validace v indexeru**  

```csharp
public class BezpecnySeznam 
{
    private string[] _data = new string[10];

    public string this[int index] 
    {
        get 
        {
            if (index < 0 || index >= _data.Length)
                throw new IndexOutOfRangeException();
            return _data[index];
        }
        set 
        {
            if (index < 0 || index >= _data.Length)
                throw new IndexOutOfRangeException();
            _data[index] = value;
        }
    }
}
```

---

### **6. Porovnání s poli a vlastnostmi**  

| **Kritérium**       | **Indexer**                  | **Pole**                   | **Vlastnost**              |  
|----------------------|------------------------------|----------------------------|----------------------------|  
| **Parametry**        | Ano (jeden nebo více)        | Ano (jen pole)             | Ne                         |  
| **Použití**          | Pro kolekce a struktury      | Statická data              | Pøístup k jednotlivým hodnotám |  
| **Flexibilita**      | Validace, logika v get/set   | Pøímý pøístup              | Validace, logika           |  

---

### **7. Bìžné chyby a tipy**  

- **Nevalidované indexy**: Vždy kontrolujte platnost indexu v `get`/`set`.  
- **Pøetìžování indexerù**:  
  ```csharp
  public class Data 
  {
      public string this[int index] { ... }
      public string this[string key] { ... } // Pøetížení s jiným typem
  }
  ```
- **Použití s rozhraními**: Indexery lze definovat v rozhraních.  
- **Statické indexery**: Nejsou podporovány.  

---

### **8. Ukázka reálného použití**  

#### **Vlastní kolekce s indexerem**  

```csharp
public class MojeKolekce<T> 
{
    private T[] _prvky = new T[10];

    public T this[int index] 
    {
        get => _prvky[index];
        set => _prvky[index] = value;
    }

    public int Pocet => _prvky.Length;
}

// Použití:
MojeKolekce<int> cisla = new MojeKolekce<int>();
cisla[0] = 42;
Console.WriteLine(cisla[0]); // 42
```

---

### **9. Doporuèené zdroje**  

- **Oficiální dokumentace**: [Indexers (C#)](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/indexers/)  
