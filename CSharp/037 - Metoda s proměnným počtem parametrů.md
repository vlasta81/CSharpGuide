
### **1. Definice**  

Metody s prom�nn�m po�tem parametr� umo��uj� **p�edat libovoln� po�et argument� stejn�ho typu** do jedin�ho parametru. Vyu��vaj� kl��ov� slovo `params`, kter� ozna�uje parametr jako pole prom�nn� d�lky.

---

### **2. Syntaxe**  

```csharp
public static int Soucet(params int[] cisla) 
{
    int suma = 0;
    foreach (int c in cisla) 
    {
        suma += c;
    }
    return suma;
}

// Pou�it�:
int vysledek1 = Soucet(1, 2, 3);    // 6
int vysledek2 = Soucet(10, 20);      // 30
int vysledek3 = Soucet();            // 0 (pr�zdn� pole)
```

---

### **3. Kl��ov� vlastnosti** 

1. **`params` mus� b�t posledn� parametr**: Metoda m��e m�t i dal�� parametry, ale `params` mus� b�t na konci.  
   ```csharp
   public static void Vypis(string zprava, params object[] hodnoty) { ... }
   ```  
2. **Typ parametru**: V�dy pole (`T[]`).  
3. **Voliteln� argumenty**: Lze p�edat 0 a� N hodnot.  
4. **Kombinace s pevn�mi parametry**:  
   ```csharp
   public static string SpojRetezce(string oddelovac, params string[] slova) 
   {
       return string.Join(oddelovac, slova);
   }

   // Pou�it�:
   string veta = SpojRetezce(" ", "Ahoj", "sv�te!"); // "Ahoj sv�te!"
   ```

---

### **4. Omezen�**  

- **Pouze jeden `params` parametr** na metodu.  
- **Nelze kombinovat s `ref`/`out`**:  
  ```csharp
  // Chyba: Nelze pou��t params s ref/out
  public static void Test(params ref int[] cisla) { ... }
  ```  
- **Neplat� pro v�cerozm�rn� pole**: `params` funguje pouze pro 1D pole.  

---

### **5. B�n� sc�n��e pou�it�**  

- **Matematick� operace**: Nap�. s��t�n� N ��sel.  
- **Form�tov�n� textu**: Spojov�n� �et�zc� s r�zn�mi hodnotami.  
- **Logov�n�**: Metody p�ij�maj�c� prom�nliv� po�et parametr� pro v�stup.  

---

### **6. P��klad s r�zn�mi typy**  

```csharp
public static void Loguj(string format, params object[] args) 
{
    Console.WriteLine(format, args);
}

// Pou�it�:
Loguj("U�ivatel {0} m� {1} let.", "Anna", 25); // "U�ivatel Anna m� 25 let."
```

---

### **7. Rozd�l mezi `params` a p�ed�n�m pole**  

- **`params`**: Umo��uje p�edat hodnoty jako samostatn� argumenty.  
- **Explicitn� pole**: Pokud metoda nem� `params`, mus�te p�edat pole.  
  ```csharp
  // Bez params:
  public static int SoucetPole(int[] cisla) { ... }
  SoucetPole(new int[] { 1, 2, 3 }); // Nutn� vytvo�it pole

  // S params:
  Soucet(1, 2, 3); // Pole se vytvo�� automaticky
  ```

---

### **8. Doporu�en� postupy**  

1. **Pou��vejte pro logicky souvisej�c� hodnoty**: Nap�. seznam ��sel, slov.  
2. **Vyhn�te se nadu��v�n�**: Pokud je po�et parametr� p�edv�dateln�, pou�ijte p�et�en� metod.  
3. **Dokumentujte chov�n�**: Uve�te, jak metoda zpracov�v� prom�nn� po�et argument�.  

---

### **9. B�n� chyby**  

- **Um�st�n� `params` na �patn�m m�st�**:  
  ```csharp
  public static void Chyba(params int[] cisla, string text) { ... } // Chyba: params mus� b�t posledn�
  ```  
- **Neo�et�en� pr�zdn� pole**: Pokud nen� zaji�t�na validace, m��e metoda selhat.  

---

### **10. Shrnut�**  

| **Vlastnost**       | **Popis**                                  |  
|----------------------|--------------------------------------------|  
| **Kl��ov� slovo**    | `params`                                   |  
| **Typ parametru**    | Jednorozm�rn� pole (`T[]`)                 |  
| **Vol�n�**           | Lze p�edat 0 a� N hodnot nebo explicitn� pole |  
| **Omezen�**          | Pouze jeden `params` parametr na metodu    |  
