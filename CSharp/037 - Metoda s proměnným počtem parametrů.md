
### **1. Definice**  

Metody s promìnným poètem parametrù umožòují **pøedat libovolný poèet argumentù stejného typu** do jediného parametru. Využívají klíèové slovo `params`, které oznaèuje parametr jako pole promìnné délky.

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

// Použití:
int vysledek1 = Soucet(1, 2, 3);    // 6
int vysledek2 = Soucet(10, 20);      // 30
int vysledek3 = Soucet();            // 0 (prázdné pole)
```

---

### **3. Klíèové vlastnosti** 

1. **`params` musí být poslední parametr**: Metoda mùže mít i další parametry, ale `params` musí být na konci.  
   ```csharp
   public static void Vypis(string zprava, params object[] hodnoty) { ... }
   ```  
2. **Typ parametru**: Vždy pole (`T[]`).  
3. **Volitelné argumenty**: Lze pøedat 0 až N hodnot.  
4. **Kombinace s pevnými parametry**:  
   ```csharp
   public static string SpojRetezce(string oddelovac, params string[] slova) 
   {
       return string.Join(oddelovac, slova);
   }

   // Použití:
   string veta = SpojRetezce(" ", "Ahoj", "svìte!"); // "Ahoj svìte!"
   ```

---

### **4. Omezení**  

- **Pouze jeden `params` parametr** na metodu.  
- **Nelze kombinovat s `ref`/`out`**:  
  ```csharp
  // Chyba: Nelze použít params s ref/out
  public static void Test(params ref int[] cisla) { ... }
  ```  
- **Neplatí pro vícerozmìrná pole**: `params` funguje pouze pro 1D pole.  

---

### **5. Bìžné scénáøe použití**  

- **Matematické operace**: Napø. sèítání N èísel.  
- **Formátování textu**: Spojování øetìzcù s rùznými hodnotami.  
- **Logování**: Metody pøijímající promìnlivý poèet parametrù pro výstup.  

---

### **6. Pøíklad s rùznými typy**  

```csharp
public static void Loguj(string format, params object[] args) 
{
    Console.WriteLine(format, args);
}

// Použití:
Loguj("Uživatel {0} má {1} let.", "Anna", 25); // "Uživatel Anna má 25 let."
```

---

### **7. Rozdíl mezi `params` a pøedáním pole**  

- **`params`**: Umožòuje pøedat hodnoty jako samostatné argumenty.  
- **Explicitní pole**: Pokud metoda nemá `params`, musíte pøedat pole.  
  ```csharp
  // Bez params:
  public static int SoucetPole(int[] cisla) { ... }
  SoucetPole(new int[] { 1, 2, 3 }); // Nutné vytvoøit pole

  // S params:
  Soucet(1, 2, 3); // Pole se vytvoøí automaticky
  ```

---

### **8. Doporuèené postupy**  

1. **Používejte pro logicky související hodnoty**: Napø. seznam èísel, slov.  
2. **Vyhnìte se nadužívání**: Pokud je poèet parametrù pøedvídatelný, použijte pøetížení metod.  
3. **Dokumentujte chování**: Uveïte, jak metoda zpracovává promìnný poèet argumentù.  

---

### **9. Bìžné chyby**  

- **Umístìní `params` na špatném místì**:  
  ```csharp
  public static void Chyba(params int[] cisla, string text) { ... } // Chyba: params musí být poslední
  ```  
- **Neošetøené prázdné pole**: Pokud není zajištìna validace, mùže metoda selhat.  

---

### **10. Shrnutí**  

| **Vlastnost**       | **Popis**                                  |  
|----------------------|--------------------------------------------|  
| **Klíèové slovo**    | `params`                                   |  
| **Typ parametru**    | Jednorozmìrné pole (`T[]`)                 |  
| **Volání**           | Lze pøedat 0 až N hodnot nebo explicitní pole |  
| **Omezení**          | Pouze jeden `params` parametr na metodu    |  
