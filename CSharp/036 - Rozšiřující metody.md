
### **1. Definice rozšiøujících metod**  

Rozšiøující metody umožòují **pøidávat nové metody k existujícím typùm** (tøídám, strukturám, rozhraním) **bez modifikace pùvodního kódu**.  
- **Klíèové vlastnosti**:  
  - Jsou definovány jako **statické metody** ve **statických tøídách**.  
  - První parametr metody je oznaèen klíèovým slovem `this`.  
  - Volají se jako instance metody pùvodního typu.  

---

### **2. Syntaxe**  

```csharp
public static class StringExtensions 
{
    // Rozšíøení typu string o metodu ProVelkaPismena()
    public static string ProVelkaPismena(this string text) 
    {
        return text.ToUpper();
    }
}

// Použití:
string jmeno = "Anna";
Console.WriteLine(jmeno.ProVelkaPismena()); // "ANNA"
```

---

### **3. Vlastnosti a pravidla**  

1. **Statická tøída a metoda**: Rozšiøující metody musí být v statické tøídì a samy statické.  
2. **První parametr s `this`**: Urèuje typ, který se rozšiøuje.  
3. **Viditelnost jmenného prostoru**: Aby metoda byla dostupná, musí být importován jmenný prostor tøídy s rozšíøením (`using`).  
4. **Nelze pøepsat existující metody**: Pokud metoda se stejným názvem již existuje, volá se pùvodní implementace.  

---

### **4. Kde se používají?**  

- **LINQ (Language Integrated Query)**: Všechny LINQ metody (napø. `Where`, `Select`) jsou rozšiøující metody pro `IEnumerable<T>`.  
- **Rozšíøení tøetích stran**: Pøidání funkcionality ke tøídám, které nelze upravit (napø. `string`, `int`, tøídy z knihoven).  
- **Fluent rozhraní**: Zøetìzení volání metod pro èitelnìjší kód.  

---

### **5. Pøíklady**  

#### **Rozšíøení pro èísla**  

```csharp
public static class IntExtensions 
{
    public static bool JeSude(this int cislo) 
    {
        return cislo % 2 == 0;
    }
}

// Použití:
int x = 10;
Console.WriteLine(x.JeSude()); // True
```

#### **Rozšíøení pro kolekce**  

```csharp
public static class CollectionExtensions 
{
    public static void Vypis<T>(this IEnumerable<T> kolekce) 
    {
        foreach (var polozka in kolekce)
            Console.WriteLine(polozka);
    }
}

// Použití:
List<int> cisla = new List<int> { 1, 2, 3 };
cisla.Vypis(); // Vypíše 1, 2, 3
```

---

### **6. Výhody a nevýhody**  

| **Výhody**                        | **Nevýhody**                          |  
|------------------------------------|----------------------------------------|  
| Rozšíøení existujících typù bez dìdiènosti | Nelze pøistupovat k privátním èlenùm |  
| Èistší a intuitivnìjší kód         | Riziko konfliktù názvù metod          |  
| Podpora pro LINQ a fluent API      | Omezení na veøejné èleny              |  

---

### **7. Bìžné chyby**  

- **Zapomenuté `this` u prvního parametru**: Metoda se nestane rozšiøující.  
- **Ignorování jmenného prostoru**: Bez `using` není rozšíøení viditelné.  
- **Zámìna s instanèními metodami**: Pokud tøída již má metodu se stejným názvem, rozšíøení se **nevolá**.  

---

### **8. Doporuèené postupy**  

1. **Pojmenovávejte metody intuitivnì**: Napø. `ToJson()`, `IsValid()`.  
2. **Seskupujte rozšíøení logicky**: Napø. `DateTimeExtensions`, `StringExtensions`.  
3. **Dokumentujte rozšíøení**: Popište úèel a chování metod.  
4. **Vyhnìte se nadužívání**: Rozšiøujte jen tam, kde to pøidává hodnotu.  

---

### **9. Shrnutí**  

- **Rozšiøující metody** pøidávají nové chování k existujícím typùm.  
- Jsou definovány jako **statické metody** s parametrem `this`.  
- Používají se pro **LINQ**, **fluent API** a rozšíøení neupravitelných tøíd.  
- Udržujte je jednoduché a intuitivní.  
