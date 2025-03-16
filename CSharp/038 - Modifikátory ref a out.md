
### **1. Modifik�tor `ref`**  

#### **Definice**  

- Umo��uje p�edat **parametr odkazem** (m�sto hodnotou).  
- Zm�ny parametru uvnit� metody ovlivn� **p�vodn� prom�nnou**.  
- Prom�nn� **mus� b�t inicializov�na** p�ed p�ed�n�m do metody.  

#### **Syntaxe**  

```csharp
public void Zvyseni(ref int cislo) 
{
    cislo++;
}

// Pou�it�:
int x = 5;
Zvyseni(ref x);
Console.WriteLine(x); // 6
```

#### **Pou�it�**  

- Kdy� pot�ebujete **modifikovat p�vodn� prom�nnou** uvnit� metody.  
- Pro optimalizaci (zamezen� kop�rov�n� velk�ch struktur).  

---

### **2. Modifik�tor `out`**  

#### **Definice**  

- Podobn� `ref`, ale prom�nn� **nemus� b�t inicializov�na** p�ed p�ed�n�m.  
- Metoda **mus� parametru p�i�adit hodnotu** p�ed n�vratem.  

#### **Syntaxe**  

```csharp
public bool TryParseInt(string text, out int vysledek) 
{
    if (int.TryParse(text, out vysledek)) 
    {
        return true;
    }
    vysledek = 0; // Nutn� p�i�adit, i kdy� ne�sp�ch
    return false;
}

// Pou�it�:
int cislo;
bool uspech = TryParseInt("123", out cislo);
```

#### **Pou�it�**  

- Pro metody, kter� vracej� **v�ce v�stup�** (nap�. `TryParse`).  
- Kdy� pot�ebujete **vytvo�it prom�nnou uvnit� metody**.  

---

### **3. Rozd�ly mezi `ref` a `out`**  

| **Krit�rium**       | **`ref`**                     | **`out`**                     |  
|----------------------|-------------------------------|-------------------------------|  
| **Inicializace**     | Povinn� p�ed vol�n�m          | Nen� nutn�                    |  
| **P�i�azen� v metod�** | Nen� vy�adov�no             | Povinn�                       |  
| **Vyu�it�**          | Modifikace existuj�c� prom�nn� | Vytvo�en� nov� prom�nn�      |  

---

### **4. Modifik�tor `in` (C# 7.2+)**  

- P�ed�v� parametr **odkazem, ale pouze pro �ten�**.  
- Zabra�uje necht�n�m zm�n�m a zlep�uje v�kon u velk�ch struktur.  
```csharp
public void VypisVelikost(in VelkaStruktura data) 
{
    Console.WriteLine(data.Velikost); // Nelze data modifikovat
}
```

---

### **5. B�n� sc�n��e**  

#### **a) Prohozen� hodnot (`ref`)**  

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

#### **b) N�vrat v�ce hodnot (`out`)**  

```csharp
public static void Rozdel(string text, out string prvniCast, out string druhaCast) 
{
    prvniCast = text.Substring(0, text.Length / 2);
    druhaCast = text.Substring(text.Length / 2);
}

Rozdel("Ahoj sv�t", out string prvni, out string druha);
Console.WriteLine(prvni); // "Ahoj"
Console.WriteLine(druha); // " sv�t"
```

---

### **6. �ast� chyby**  

- **Zapomenut� inicializace pro `ref`**:  
  ```csharp
  int x;
  Zvyseni(ref x); // Chyba: x nen� inicializov�no!
  ```  
- **Ne p�i�azen� `out` parametru**:  
  ```csharp
  public void Test(out int cislo) { } // Chyba: cislo nen� p�i�azeno!
  ```  

---

### **7. Doporu�en� postupy**  

1. **`ref`/`out` pou��vejte opatrn�**: Mohou zt�it �itelnost k�du.  
2. **Preferujte n�vratov� hodnoty**: Pokud mo�no, vracejte data pomoc� `return`.  
3. **`out` pro metody s vedlej��m ��inkem**: Nap�. `TryParse`.  

---

### **8. Shrnut�**  

- **`ref`**: P�ed�v� prom�nnou odkazem (mus� b�t inicializov�na).  
- **`out`**: P�ed�v� prom�nnou odkazem (inicializace a� uvnit� metody).  
- **`in`**: P�ed�v� prom�nnou pro �ten� (optimalizace v�konu).  
