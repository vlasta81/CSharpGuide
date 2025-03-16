
### **1. Definice rozšiřujících metod**  

Rozšiřující metody umožňují **přidávat nové metody k existujícím typům** (třídám, strukturám, rozhraním) **bez modifikace původního kódu**.  
- **Klíčové vlastnosti**:  
  - Jsou definovány jako **statické metody** ve **statických třídách**.  
  - První parametr metody je označen klíčovým slovem `this`.  
  - Volají se jako instance metody původního typu.  

---

### **2. Syntaxe**  

```csharp
public static class StringExtensions 
{
    // Rozšíření typu string o metodu ProVelkaPismena()
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

1. **Statická třída a metoda**: Rozšiřující metody musí být v statické třídě a samy statické.  
2. **První parametr s `this`**: Určuje typ, který se rozšiřuje.  
3. **Viditelnost jmenného prostoru**: Aby metoda byla dostupná, musí být importován jmenný prostor třídy s rozšířením (`using`).  
4. **Nelze přepsat existující metody**: Pokud metoda se stejným názvem již existuje, volá se původní implementace.  

---

### **4. Kde se používají?**  

- **LINQ (Language Integrated Query)**: Všechny LINQ metody (např. `Where`, `Select`) jsou rozšiřující metody pro `IEnumerable<T>`.  
- **Rozšíření třetích stran**: Přidání funkcionality ke třídám, které nelze upravit (např. `string`, `int`, třídy z knihoven).  
- **Fluent rozhraní**: Zřetězení volání metod pro čitelnější kód.  

---

### **5. Příklady**  

#### **Rozšíření pro čísla**  

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

#### **Rozšíření pro kolekce**  

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
| Rozšíření existujících typů bez dědičnosti | Nelze přistupovat k privátním členům |  
| Čistší a intuitivnější kód         | Riziko konfliktů názvů metod          |  
| Podpora pro LINQ a fluent API      | Omezení na veřejné členy              |  

---

### **7. Běžné chyby**  

- **Zapomenuté `this` u prvního parametru**: Metoda se nestane rozšiřující.  
- **Ignorování jmenného prostoru**: Bez `using` není rozšíření viditelné.  
- **Záměna s instančními metodami**: Pokud třída již má metodu se stejným názvem, rozšíření se **nevolá**.  

---

### **8. Doporučené postupy**  

1. **Pojmenovávejte metody intuitivně**: Např. `ToJson()`, `IsValid()`.  
2. **Seskupujte rozšíření logicky**: Např. `DateTimeExtensions`, `StringExtensions`.  
3. **Dokumentujte rozšíření**: Popište účel a chování metod.  
4. **Vyhněte se nadužívání**: Rozšiřujte jen tam, kde to přidává hodnotu.  

---

### **9. Shrnutí**  

- **Rozšiřující metody** přidávají nové chování k existujícím typům.  
- Jsou definovány jako **statické metody** s parametrem `this`.  
- Používají se pro **LINQ**, **fluent API** a rozšíření neupravitelných tříd.  
- Udržujte je jednoduché a intuitivní.  
