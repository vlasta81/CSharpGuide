
### **1. Definice roz�i�uj�c�ch metod**  

Roz�i�uj�c� metody umo��uj� **p�id�vat nov� metody k existuj�c�m typ�m** (t��d�m, struktur�m, rozhran�m) **bez modifikace p�vodn�ho k�du**.  
- **Kl��ov� vlastnosti**:  
  - Jsou definov�ny jako **statick� metody** ve **statick�ch t��d�ch**.  
  - Prvn� parametr metody je ozna�en kl��ov�m slovem `this`.  
  - Volaj� se jako instance metody p�vodn�ho typu.  

---

### **2. Syntaxe**  

```csharp
public static class StringExtensions 
{
    // Roz���en� typu string o metodu ProVelkaPismena()
    public static string ProVelkaPismena(this string text) 
    {
        return text.ToUpper();
    }
}

// Pou�it�:
string jmeno = "Anna";
Console.WriteLine(jmeno.ProVelkaPismena()); // "ANNA"
```

---

### **3. Vlastnosti a pravidla**  

1. **Statick� t��da a metoda**: Roz�i�uj�c� metody mus� b�t v statick� t��d� a samy statick�.  
2. **Prvn� parametr s `this`**: Ur�uje typ, kter� se roz�i�uje.  
3. **Viditelnost jmenn�ho prostoru**: Aby metoda byla dostupn�, mus� b�t importov�n jmenn� prostor t��dy s roz���en�m (`using`).  
4. **Nelze p�epsat existuj�c� metody**: Pokud metoda se stejn�m n�zvem ji� existuje, vol� se p�vodn� implementace.  

---

### **4. Kde se pou��vaj�?**  

- **LINQ (Language Integrated Query)**: V�echny LINQ metody (nap�. `Where`, `Select`) jsou roz�i�uj�c� metody pro `IEnumerable<T>`.  
- **Roz���en� t�et�ch stran**: P�id�n� funkcionality ke t��d�m, kter� nelze upravit (nap�. `string`, `int`, t��dy z knihoven).  
- **Fluent rozhran�**: Z�et�zen� vol�n� metod pro �iteln�j�� k�d.  

---

### **5. P��klady**  

#### **Roz���en� pro ��sla**  

```csharp
public static class IntExtensions 
{
    public static bool JeSude(this int cislo) 
    {
        return cislo % 2 == 0;
    }
}

// Pou�it�:
int x = 10;
Console.WriteLine(x.JeSude()); // True
```

#### **Roz���en� pro kolekce**  

```csharp
public static class CollectionExtensions 
{
    public static void Vypis<T>(this IEnumerable<T> kolekce) 
    {
        foreach (var polozka in kolekce)
            Console.WriteLine(polozka);
    }
}

// Pou�it�:
List<int> cisla = new List<int> { 1, 2, 3 };
cisla.Vypis(); // Vyp�e 1, 2, 3
```

---

### **6. V�hody a nev�hody**  

| **V�hody**                        | **Nev�hody**                          |  
|------------------------------------|----------------------------------------|  
| Roz���en� existuj�c�ch typ� bez d�di�nosti | Nelze p�istupovat k priv�tn�m �len�m |  
| �ist�� a intuitivn�j�� k�d         | Riziko konflikt� n�zv� metod          |  
| Podpora pro LINQ a fluent API      | Omezen� na ve�ejn� �leny              |  

---

### **7. B�n� chyby**  

- **Zapomenut� `this` u prvn�ho parametru**: Metoda se nestane roz�i�uj�c�.  
- **Ignorov�n� jmenn�ho prostoru**: Bez `using` nen� roz���en� viditeln�.  
- **Z�m�na s instan�n�mi metodami**: Pokud t��da ji� m� metodu se stejn�m n�zvem, roz���en� se **nevol�**.  

---

### **8. Doporu�en� postupy**  

1. **Pojmenov�vejte metody intuitivn�**: Nap�. `ToJson()`, `IsValid()`.  
2. **Seskupujte roz���en� logicky**: Nap�. `DateTimeExtensions`, `StringExtensions`.  
3. **Dokumentujte roz���en�**: Popi�te ��el a chov�n� metod.  
4. **Vyhn�te se nadu��v�n�**: Roz�i�ujte jen tam, kde to p�id�v� hodnotu.  

---

### **9. Shrnut�**  

- **Roz�i�uj�c� metody** p�id�vaj� nov� chov�n� k existuj�c�m typ�m.  
- Jsou definov�ny jako **statick� metody** s parametrem `this`.  
- Pou��vaj� se pro **LINQ**, **fluent API** a roz���en� neupraviteln�ch t��d.  
- Udr�ujte je jednoduch� a intuitivn�.  
