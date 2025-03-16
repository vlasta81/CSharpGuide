
### **1. Definice vıètového typu**  

Vıètovı typ (`enum`) je **hodnotovı typ**, kterı definuje mnoinu pojmenovanıch konstant. Slouí k reprezentaci pevného seznamu hodnot (napø. dny v tıdnu, stavy, kódy).  

**Klíèové vlastnosti**:  
- Kadá konstanta má **èíselnou hodnotu** (vıchozí typ `int`, lze zmìnit).  
- Zlepšuje èitelnost a bezpeènost kódu (místo "magickıch èísel").  

---

### **2. Základní syntaxe**  

```csharp
public enum DenVTydnu 
{
    Pondeli,    // Hodnota 0 (vıchozí)
    Utery,      // Hodnota 1
    Streda,     // Hodnota 2
    Ctvrtek,    // Hodnota 3
    Patek,      // Hodnota 4
    Sobota,     // Hodnota 5
    Nedele      // Hodnota 6
}
```

---

### **3. Explicitní pøiøazení hodnot**  

- Lze nastavit vlastní èíselné hodnoty.  
```csharp
public enum Stav 
{
    Novy = 10,
    Aktivni = 20,
    Archivovany = 30
}
```

---

### **4. Zmìna základního typu**  

- Lze pouít `byte`, `short`, `int`, `long`, `sbyte`, `ushort`, `uint`, `ulong`.  
```csharp
public enum Priorita : byte 
{
    Nizka = 1,
    Stredni = 2,
    Vysoka = 3
}
```

---

### **5. Konverze mezi `enum` a èíslem**  

#### **a) Explicitní pøetypování**  

```csharp
DenVTydnu den = DenVTydnu.Pondeli;
int hodnota = (int)den; // 0
DenVTydnu zpet = (DenVTydnu)hodnota; // Pondeli
```

#### **b) Metody tøídy `Enum`**  

- `Enum.GetValues()`: Vrátí pole všech hodnot.  
- `Enum.Parse()`: Pøevod øetìzce na `enum`.  
```csharp
string text = "Pondeli";
DenVTydnu den = (DenVTydnu)Enum.Parse(typeof(DenVTydnu), text);
```

---

### **6. Bitové vlajky (`Flags`)**  

- Umoòují kombinovat hodnoty pomocí bitovıch operací.  
```csharp
[Flags]
public enum Opravneni 
{
    ádné = 0,
    Ètení = 1,
    Zápis = 2,
    Spuštìní = 4,
    Vše = Ètení | Zápis | Spuštìní // 7
}

// Pouití:
Opravneni userOpravneni = Opravneni.Ètení | Opravneni.Zápis;
Console.WriteLine(userOpravneni); // "Ètení, Zápis"
```

---

### **7. Bìné operace**  

- **Kontrola existence hodnoty**:  
```csharp
bool existuje = Enum.IsDefined(typeof(DenVTydnu), 3); // True (Ctvrtek)
```  
- **Získání názvu hodnoty**:  
```csharp
string nazev = Enum.GetName(typeof(DenVTydnu), 2); // "Streda"
```

---

### **8. Doporuèené postupy**  

1. **Pouívejte popisné názvy**: `Stav.Aktivni` místo `Stav.A`.  
2. **Nastavte vıchozí hodnotu**: Pokud `enum` reprezentuje stav, pøidejte `Neznámı` nebo `Neplatnı`.  
3. **Pro kombinované hodnoty pouijte `[Flags]`**: Zajišuje správnou práci s bitovımi operacemi.  
4. **Nepouívejte vıchozí èíselné hodnoty**: Pokud mají hodnoty specifickı vıznam, explicitnì je pøiøaïte.  

---

### **9. Èasté chyby**  

- **Neošetøené konverze**:  
```csharp
int neznamyHodnota = 10;
DenVTydnu den = (DenVTydnu)neznamyHodnota; // Vyvolá vıjimku, pokud hodnota není definována.
```  
- **Zámìna s øetìzci**:  
```csharp
if (den == "Pondeli") { ... } // Chyba! Porovnává se s enum hodnotou, ne øetìzcem.
```

---

### **10. Ukázky kódu**  

#### **Práce s `Flags`**  

```csharp
[Flags]
public enum Barvy 
{
    ádná = 0,
    Èervená = 1,
    Zelená = 2,
    Modrá = 4,
    lutá = Èervená | Zelená // 3
}

Barvy mix = Barvy.Èervená | Barvy.Modrá;
Console.WriteLine(mix.HasFlag(Barvy.Èervená)); // True
```

#### **Iterace pøes všechny hodnoty `enum`**  

```csharp
foreach (DenVTydnu den in Enum.GetValues(typeof(DenVTydnu))) 
{
    Console.WriteLine(den);
}
```

---

### **11. Shrnutí**  

- **`enum`** zjednodušuje práci s pevnımi sadami hodnot.  
- **Bitové vlajky** umoòují kombinovat hodnoty pomocí operátorù `|`, `&`, `~`.  
- **Konverze** mezi `enum` a èíslem/øetìzcem vyaduje opatrnost.  
