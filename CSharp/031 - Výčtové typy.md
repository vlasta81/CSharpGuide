
### **1. Definice v��tov�ho typu**  

V��tov� typ (`enum`) je **hodnotov� typ**, kter� definuje mno�inu pojmenovan�ch konstant. Slou�� k reprezentaci pevn�ho seznamu hodnot (nap�. dny v t�dnu, stavy, k�dy).  

**Kl��ov� vlastnosti**:  
- Ka�d� konstanta m� **��selnou hodnotu** (v�choz� typ `int`, lze zm�nit).  
- Zlep�uje �itelnost a bezpe�nost k�du (m�sto "magick�ch ��sel").  

---

### **2. Z�kladn� syntaxe**  

```csharp
public enum DenVTydnu 
{
    Pondeli,    // Hodnota 0 (v�choz�)
    Utery,      // Hodnota 1
    Streda,     // Hodnota 2
    Ctvrtek,    // Hodnota 3
    Patek,      // Hodnota 4
    Sobota,     // Hodnota 5
    Nedele      // Hodnota 6
}
```

---

### **3. Explicitn� p�i�azen� hodnot**  

- Lze nastavit vlastn� ��seln� hodnoty.  
```csharp
public enum Stav 
{
    Novy = 10,
    Aktivni = 20,
    Archivovany = 30
}
```

---

### **4. Zm�na z�kladn�ho typu**  

- Lze pou��t `byte`, `short`, `int`, `long`, `sbyte`, `ushort`, `uint`, `ulong`.  
```csharp
public enum Priorita : byte 
{
    Nizka = 1,
    Stredni = 2,
    Vysoka = 3
}
```

---

### **5. Konverze mezi `enum` a ��slem**  

#### **a) Explicitn� p�etypov�n�**  

```csharp
DenVTydnu den = DenVTydnu.Pondeli;
int hodnota = (int)den; // 0
DenVTydnu zpet = (DenVTydnu)hodnota; // Pondeli
```

#### **b) Metody t��dy `Enum`**  

- `Enum.GetValues()`: Vr�t� pole v�ech hodnot.  
- `Enum.Parse()`: P�evod �et�zce na `enum`.  
```csharp
string text = "Pondeli";
DenVTydnu den = (DenVTydnu)Enum.Parse(typeof(DenVTydnu), text);
```

---

### **6. Bitov� vlajky (`Flags`)**  

- Umo��uj� kombinovat hodnoty pomoc� bitov�ch operac�.  
```csharp
[Flags]
public enum Opravneni 
{
    ��dn� = 0,
    �ten� = 1,
    Z�pis = 2,
    Spu�t�n� = 4,
    V�e = �ten� | Z�pis | Spu�t�n� // 7
}

// Pou�it�:
Opravneni userOpravneni = Opravneni.�ten� | Opravneni.Z�pis;
Console.WriteLine(userOpravneni); // "�ten�, Z�pis"
```

---

### **7. B�n� operace**  

- **Kontrola existence hodnoty**:  
```csharp
bool existuje = Enum.IsDefined(typeof(DenVTydnu), 3); // True (Ctvrtek)
```  
- **Z�sk�n� n�zvu hodnoty**:  
```csharp
string nazev = Enum.GetName(typeof(DenVTydnu), 2); // "Streda"
```

---

### **8. Doporu�en� postupy**  

1. **Pou��vejte popisn� n�zvy**: `Stav.Aktivni` m�sto `Stav.A`.  
2. **Nastavte v�choz� hodnotu**: Pokud `enum` reprezentuje stav, p�idejte `Nezn�m�` nebo `Neplatn�`.  
3. **Pro kombinovan� hodnoty pou�ijte `[Flags]`**: Zaji��uje spr�vnou pr�ci s bitov�mi operacemi.  
4. **Nepou��vejte v�choz� ��seln� hodnoty**: Pokud maj� hodnoty specifick� v�znam, explicitn� je p�i�a�te.  

---

### **9. �ast� chyby**  

- **Neo�et�en� konverze**:  
```csharp
int neznamyHodnota = 10;
DenVTydnu den = (DenVTydnu)neznamyHodnota; // Vyvol� v�jimku, pokud hodnota nen� definov�na.
```  
- **Z�m�na s �et�zci**:  
```csharp
if (den == "Pondeli") { ... } // Chyba! Porovn�v� se s enum hodnotou, ne �et�zcem.
```

---

### **10. Uk�zky k�du**  

#### **Pr�ce s `Flags`**  

```csharp
[Flags]
public enum Barvy 
{
    ��dn� = 0,
    �erven� = 1,
    Zelen� = 2,
    Modr� = 4,
    �lut� = �erven� | Zelen� // 3
}

Barvy mix = Barvy.�erven� | Barvy.Modr�;
Console.WriteLine(mix.HasFlag(Barvy.�erven�)); // True
```

#### **Iterace p�es v�echny hodnoty `enum`**  

```csharp
foreach (DenVTydnu den in Enum.GetValues(typeof(DenVTydnu))) 
{
    Console.WriteLine(den);
}
```

---

### **11. Shrnut�**  

- **`enum`** zjednodu�uje pr�ci s pevn�mi sadami hodnot.  
- **Bitov� vlajky** umo��uj� kombinovat hodnoty pomoc� oper�tor� `|`, `&`, `~`.  
- **Konverze** mezi `enum` a ��slem/�et�zcem vy�aduje opatrnost.  
