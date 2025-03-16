
### **1. Definice výčtového typu**  

Výčtový typ (`enum`) je **hodnotový typ**, který definuje množinu pojmenovaných konstant. Slouží k reprezentaci pevného seznamu hodnot (např. dny v týdnu, stavy, kódy).  

**Klíčové vlastnosti**:  
- Každá konstanta má **číselnou hodnotu** (výchozí typ `int`, lze změnit).  
- Zlepšuje čitelnost a bezpečnost kódu (místo "magických čísel").  

---

### **2. Základní syntaxe**  

```csharp
public enum DenVTydnu 
{
    Pondeli,    // Hodnota 0 (výchozí)
    Utery,      // Hodnota 1
    Streda,     // Hodnota 2
    Ctvrtek,    // Hodnota 3
    Patek,      // Hodnota 4
    Sobota,     // Hodnota 5
    Nedele      // Hodnota 6
}
```

---

### **3. Explicitní přiřazení hodnot**  

- Lze nastavit vlastní číselné hodnoty.  
```csharp
public enum Stav 
{
    Novy = 10,
    Aktivni = 20,
    Archivovany = 30
}
```

---

### **4. Změna základního typu**  

- Lze použít `byte`, `short`, `int`, `long`, `sbyte`, `ushort`, `uint`, `ulong`.  
```csharp
public enum Priorita : byte 
{
    Nizka = 1,
    Stredni = 2,
    Vysoka = 3
}
```

---

### **5. Konverze mezi `enum` a číslem**  

#### **a) Explicitní přetypování**  

```csharp
DenVTydnu den = DenVTydnu.Pondeli;
int hodnota = (int)den; // 0
DenVTydnu zpet = (DenVTydnu)hodnota; // Pondeli
```

#### **b) Metody třídy `Enum`**  

- `Enum.GetValues()`: Vrátí pole všech hodnot.  
- `Enum.Parse()`: Převod řetězce na `enum`.  
```csharp
string text = "Pondeli";
DenVTydnu den = (DenVTydnu)Enum.Parse(typeof(DenVTydnu), text);
```

---

### **6. Bitové vlajky (`Flags`)**  

- Umožňují kombinovat hodnoty pomocí bitových operací.  
```csharp
[Flags]
public enum Opravneni 
{
    Žádné = 0,
    Čtení = 1,
    Zápis = 2,
    Spuštění = 4,
    Vše = Čtení | Zápis | Spuštění // 7
}

// Použití:
Opravneni userOpravneni = Opravneni.Čtení | Opravneni.Zápis;
Console.WriteLine(userOpravneni); // "Čtení, Zápis"
```

---

### **7. Běžné operace**  

- **Kontrola existence hodnoty**:  
```csharp
bool existuje = Enum.IsDefined(typeof(DenVTydnu), 3); // True (Ctvrtek)
```  
- **Získání názvu hodnoty**:  
```csharp
string nazev = Enum.GetName(typeof(DenVTydnu), 2); // "Streda"
```

---

### **8. Doporučené postupy**  

1. **Používejte popisné názvy**: `Stav.Aktivni` místo `Stav.A`.  
2. **Nastavte výchozí hodnotu**: Pokud `enum` reprezentuje stav, přidejte `Neznámý` nebo `Neplatný`.  
3. **Pro kombinované hodnoty použijte `[Flags]`**: Zajišťuje správnou práci s bitovými operacemi.  
4. **Nepoužívejte výchozí číselné hodnoty**: Pokud mají hodnoty specifický význam, explicitně je přiřaďte.  

---

### **9. Časté chyby**  

- **Neošetřené konverze**:  
```csharp
int neznamyHodnota = 10;
DenVTydnu den = (DenVTydnu)neznamyHodnota; // Vyvolá výjimku, pokud hodnota není definována.
```  
- **Záměna s řetězci**:  
```csharp
if (den == "Pondeli") { ... } // Chyba! Porovnává se s enum hodnotou, ne řetězcem.
```

---

### **10. Ukázky kódu**  

#### **Práce s `Flags`**  

```csharp
[Flags]
public enum Barvy 
{
    Žádná = 0,
    Červená = 1,
    Zelená = 2,
    Modrá = 4,
    Žlutá = Červená | Zelená // 3
}

Barvy mix = Barvy.Červená | Barvy.Modrá;
Console.WriteLine(mix.HasFlag(Barvy.Červená)); // True
```

#### **Iterace přes všechny hodnoty `enum`**  

```csharp
foreach (DenVTydnu den in Enum.GetValues(typeof(DenVTydnu))) 
{
    Console.WriteLine(den);
}
```

---

### **11. Shrnutí**  

- **`enum`** zjednodušuje práci s pevnými sadami hodnot.  
- **Bitové vlajky** umožňují kombinovat hodnoty pomocí operátorů `|`, `&`, `~`.  
- **Konverze** mezi `enum` a číslem/řetězcem vyžaduje opatrnost.  
