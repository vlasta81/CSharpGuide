
### **1. Modifikátor `ref`**  

#### **Definice**  

- Umožòuje pøedat **parametr odkazem** (místo hodnotou).  
- Zmìny parametru uvnitø metody ovlivní **pùvodní promìnnou**.  
- Promìnná **musí být inicializována** pøed pøedáním do metody.  

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

- Když potøebujete **modifikovat pùvodní promìnnou** uvnitø metody.  
- Pro optimalizaci (zamezení kopírování velkých struktur).  

---

### **2. Modifikátor `out`**  

#### **Definice**  

- Podobný `ref`, ale promìnná **nemusí být inicializována** pøed pøedáním.  
- Metoda **musí parametru pøiøadit hodnotu** pøed návratem.  

#### **Syntaxe**  

```csharp
public bool TryParseInt(string text, out int vysledek) 
{
    if (int.TryParse(text, out vysledek)) 
    {
        return true;
    }
    vysledek = 0; // Nutné pøiøadit, i když neúspìch
    return false;
}

// Použití:
int cislo;
bool uspech = TryParseInt("123", out cislo);
```

#### **Použití**  

- Pro metody, které vracejí **více výstupù** (napø. `TryParse`).  
- Když potøebujete **vytvoøit promìnnou uvnitø metody**.  

---

### **3. Rozdíly mezi `ref` a `out`**  

| **Kritérium**       | **`ref`**                     | **`out`**                     |  
|----------------------|-------------------------------|-------------------------------|  
| **Inicializace**     | Povinná pøed voláním          | Není nutná                    |  
| **Pøiøazení v metodì** | Není vyžadováno             | Povinné                       |  
| **Využití**          | Modifikace existující promìnné | Vytvoøení nové promìnné      |  

---

### **4. Modifikátor `in` (C# 7.2+)**  

- Pøedává parametr **odkazem, ale pouze pro ètení**.  
- Zabraòuje nechtìným zmìnám a zlepšuje výkon u velkých struktur.  
```csharp
public void VypisVelikost(in VelkaStruktura data) 
{
    Console.WriteLine(data.Velikost); // Nelze data modifikovat
}
```

---

### **5. Bìžné scénáøe**  

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

Rozdel("Ahoj svìt", out string prvni, out string druha);
Console.WriteLine(prvni); // "Ahoj"
Console.WriteLine(druha); // " svìt"
```

---

### **6. Èasté chyby**  

- **Zapomenutá inicializace pro `ref`**:  
  ```csharp
  int x;
  Zvyseni(ref x); // Chyba: x není inicializováno!
  ```  
- **Ne pøiøazení `out` parametru**:  
  ```csharp
  public void Test(out int cislo) { } // Chyba: cislo není pøiøazeno!
  ```  

---

### **7. Doporuèené postupy**  

1. **`ref`/`out` používejte opatrnì**: Mohou ztížit èitelnost kódu.  
2. **Preferujte návratové hodnoty**: Pokud možno, vracejte data pomocí `return`.  
3. **`out` pro metody s vedlejším úèinkem**: Napø. `TryParse`.  

---

### **8. Shrnutí**  

- **`ref`**: Pøedává promìnnou odkazem (musí být inicializována).  
- **`out`**: Pøedává promìnnou odkazem (inicializace až uvnitø metody).  
- **`in`**: Pøedává promìnnou pro ètení (optimalizace výkonu).  
