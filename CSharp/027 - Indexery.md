
### **1. Definice indexeru**  

Indexer umo��uje **p�istupovat k objektu jako k poli** pomoc� indexu. Je to speci�ln� vlastnost, kter� umo��uje t��d� nebo struktu�e chovat se jako pole (nap�. kolekce, seznamy, vlastn� datov� struktury).  

**Kl��ov� slovo**: `this`.  

---

### **2. Z�kladn� syntaxe**  

```csharp
public class MojSeznam 
{
    private string[] _data = new string[10]; // Priv�tn� pole

    // Indexer s celo��seln�m indexem
    public string this[int index] 
    {
        get => _data[index];
        set => _data[index] = value;
    }
}

// Pou�it�:
MojSeznam seznam = new MojSeznam();
seznam[0] = "Ahoj"; // Z�pis p�es indexer
Console.WriteLine(seznam[0]); // �ten�: "Ahoj"
```

---

### **3. Vlastnosti indexeru**  

- **Parametry**: M��e m�t jeden nebo v�ce parametr� (i r�zn�ch typ�).  
- **P��stupov� metody**: `get` a `set` (podobn� jako u vlastnost�).  
- **Typ indexu**: Lze pou��t libovoln� typ (nap�. `int`, `string`, `enum`).  
- **P�et�ov�n�**: T��da m��e m�t v�ce indexer� s r�zn�mi parametry.  

---

### **4. P��klady pou�it�**  

#### **a) Indexer s v�ce parametry**  

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

// Pou�it�:
Matice matice = new Matice();
matice[0, 0] = 5;
Console.WriteLine(matice[0, 0]); // 5
```

#### **b) Indexer s �et�zcov�m kl��em**  

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

// Pou�it�:
Slovnik slovnik = new Slovnik();
slovnik["jm�no"] = "Anna";
Console.WriteLine(slovnik["jm�no"]); // "Anna"
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

### **6. Porovn�n� s poli a vlastnostmi**  

| **Krit�rium**       | **Indexer**                  | **Pole**                   | **Vlastnost**              |  
|----------------------|------------------------------|----------------------------|----------------------------|  
| **Parametry**        | Ano (jeden nebo v�ce)        | Ano (jen pole)             | Ne                         |  
| **Pou�it�**          | Pro kolekce a struktury      | Statick� data              | P��stup k jednotliv�m hodnot�m |  
| **Flexibilita**      | Validace, logika v get/set   | P��m� p��stup              | Validace, logika           |  

---

### **7. B�n� chyby a tipy**  

- **Nevalidovan� indexy**: V�dy kontrolujte platnost indexu v `get`/`set`.  
- **P�et�ov�n� indexer�**:  
  ```csharp
  public class Data 
  {
      public string this[int index] { ... }
      public string this[string key] { ... } // P�et�en� s jin�m typem
  }
  ```
- **Pou�it� s rozhran�mi**: Indexery lze definovat v rozhran�ch.  
- **Statick� indexery**: Nejsou podporov�ny.  

---

### **8. Uk�zka re�ln�ho pou�it�**  

#### **Vlastn� kolekce s indexerem**  

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

// Pou�it�:
MojeKolekce<int> cisla = new MojeKolekce<int>();
cisla[0] = 42;
Console.WriteLine(cisla[0]); // 42
```

---

### **9. Doporu�en� zdroje**  

- **Ofici�ln� dokumentace**: [Indexers (C#)](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/indexers/)  
