
### **1. Modifikátor `ref`**  

#### **Definice**  

- Umožňuje předat **parametr odkazem** (místo hodnotou).  
- Změny parametru uvnitř metody ovlivní **původní proměnnou**.  
- Proměnná **musí být inicializována** před předáním do metody.  

#### **Syntaxe**  

```csharp
public void Zvyseni(ref int cislo) 
{
    cislo++;
}

// Použití:
int x = 5;
Zvyseni(ref x);
Console.WriteLine(x); // 6
```

#### **Použití**  

- Když potřebujete **modifikovat původní proměnnou** uvnitř metody.  
- Pro optimalizaci (zamezení kopírování velkých struktur).  

---

### **2. Modifikátor `out`**  

#### **Definice**  

- Podobný `ref`, ale proměnná **nemusí být inicializována** před předáním.  
- Metoda **musí parametru přiřadit hodnotu** před návratem.  

#### **Syntaxe**  

```csharp
public bool TryParseInt(string text, out int vysledek) 
{
    if (int.TryParse(text, out vysledek)) 
    {
        return true;
    }
    vysledek = 0; // Nutné přiřadit, i když neúspěch
    return false;
}

// Použití:
int cislo;
bool uspech = TryParseInt("123", out cislo);
```

#### **Použití**  

- Pro metody, které vracejí **více výstupů** (např. `TryParse`).  
- Když potřebujete **vytvořit proměnnou uvnitř metody**.  

---

### **3. Rozdíly mezi `ref` a `out`**  

| **Kritérium**       | **`ref`**                     | **`out`**                     |  
|----------------------|-------------------------------|-------------------------------|  
| **Inicializace**     | Povinná před voláním          | Není nutná                    |  
| **Přiřazení v metodě** | Není vyžadováno             | Povinné                       |  
| **Využití**          | Modifikace existující proměnné | Vytvoření nové proměnné      |  

---

### **4. Modifikátor `in` (C# 7.2+)**  

- Předává parametr **odkazem, ale pouze pro čtení**.  
- Zabraňuje nechtěným změnám a zlepšuje výkon u velkých struktur.  
```csharp
public void VypisVelikost(in VelkaStruktura data) 
{
    Console.WriteLine(data.Velikost); // Nelze data modifikovat
}
```

---

### **5. Běžné scénáře**  

#### **a) Prohození hodnot (`ref`)**  

```csharp
public static void Prohod(ref int a, ref int b) 
{
    int temp = a;
    a = b;
    b = temp;
}

int x = 1, y = 2;
Prohod(ref x, ref y); // x=2, y=1
```

#### **b) Návrat více hodnot (`out`)**  

```csharp
public static void Rozdel(string text, out string prvniCast, out string druhaCast) 
{
    prvniCast = text.Substring(0, text.Length / 2);
    druhaCast = text.Substring(text.Length / 2);
}

Rozdel("Ahoj svět", out string prvni, out string druha);
Console.WriteLine(prvni); // "Ahoj"
Console.WriteLine(druha); // " svět"
```

---

### **6. Časté chyby**  

- **Zapomenutá inicializace pro `ref`**:  
  ```csharp
  int x;
  Zvyseni(ref x); // Chyba: x není inicializováno!
  ```  
- **Ne přiřazení `out` parametru**:  
  ```csharp
  public void Test(out int cislo) { } // Chyba: cislo není přiřazeno!
  ```  

---

### **7. Doporučené postupy**  

1. **`ref`/`out` používejte opatrně**: Mohou ztížit čitelnost kódu.  
2. **Preferujte návratové hodnoty**: Pokud možno, vracejte data pomocí `return`.  
3. **`out` pro metody s vedlejším účinkem**: Např. `TryParse`.  

---

### **8. Shrnutí**  

- **`ref`**: Předává proměnnou odkazem (musí být inicializována).  
- **`out`**: Předává proměnnou odkazem (inicializace až uvnitř metody).  
- **`in`**: Předává proměnnou pro čtení (optimalizace výkonu).  
