
### **1. Definice**

- **Boxing**: Proces převodu **hodnotového typu** (např. `int`, `struct`) na **referenční typ** (`object` nebo `System.ValueType`).  
- **Unboxing**: Opačný proces – převod **boxovaného referenčního typu** zpět na **hodnotový typ**.  

---

### **2. Jak to funguje?**

#### **Boxing**  

1. Hodnotový typ je **zkopírován** z oblasti zásobníku (stack) do haldy (heap).  
2. Vytvoří se **nový objekt** na haldě, který obaluje hodnotu.  
3. Proměnná referenčního typu (např. `object`) odkazuje na tento objekt.  

**Příklad**:  
```csharp
int hodnota = 42;
object boxed = hodnota; // Boxing (implicitní)
```

#### **Unboxing**  

1. Kontrola kompatibility typů – pokud boxovaný objekt neodpovídá cílovému typu, vyvolá se `InvalidCastException`.  
2. Hodnota se **zkopíruje** z haldy zpět do zásobníku.  

**Příklad**:  
```csharp
int unboxed = (int)boxed; // Unboxing (explicitní přetypování)
```

---

### **3. Klíčové vlastnosti**

| **Vlastnost**       | **Boxing**                     | **Unboxing**                   |  
|----------------------|--------------------------------|--------------------------------|  
| **Směr**             | Hodnotový → referenční typ    | Referenční → hodnotový typ    |  
| **Náklady**          | Vytvoření objektu na haldě    | Kontrola typu + kopie hodnoty |  
| **Implicitní/Explicitní** | Implicitní                | Explicitní (vyžaduje přetypování) |  

---

### **4. Problémy a rizika**

- **Výkon**: Boxing/unboxing vytváří **režii** (alokace paměti, kopírování).  
- **Chyby při unboxingu**:  
  ```csharp
  object boxed = 42;
  double chyba = (double)boxed; // InvalidCastException!
  ```  
- **Zbytečné kopírování**: Při častém boxingu (např. v cyklech) roste tlak na garbage collector.  

---

### **5. Kdy k nim dochází?**

- **Boxing**:  
  - Přiřazení hodnotového typu do proměnné typu `object`.  
  - Přidání hodnotového typu do negenerické kolekce (např. `ArrayList`).  
  - Volání metody, která očekává parametr typu `object`.  
- **Unboxing**:  
  - Explicitní přetypování boxované hodnoty zpět na původní typ.  

---

### **6. Jak se vyhnout boxingu/unboxingu?**

1. **Používejte generické kolekce** (např. `List<int>` místo `ArrayList`).  
2. **Vyhněte se `object` pro hodnotové typy** v kritickém kódu.  
3. **Implementujte rozhraní přímo na struktury**:  
   ```csharp
   public struct Bod : IComparable<Bod> 
   {
       public int CompareTo(Bod other) { ... } // Žádný boxing!
   }
   ```

---

### **7. Příklady**

#### **Boxing v negenerické kolekci**  

```csharp
ArrayList list = new ArrayList();
list.Add(10); // Boxing: int → object
int cislo = (int)list[0]; // Unboxing
```

#### **Generická kolekce bez boxingu**  

```csharp
List<int> list = new List<int>();
list.Add(10); // Žádný boxing
int cislo = list[0]; // Žádný unboxing
```

---

### **8. Shrnutí**

- **Boxing/Unboxing** zahrnuje konverzi mezi hodnotovými a referenčními typy.  
- **Nákladné operace**: Vytvářejí režii a mohou způsobit chyby.  
- **Řešení**: Generické typy, přímá práce s hodnotovými typy, optimalizace kódu.  
