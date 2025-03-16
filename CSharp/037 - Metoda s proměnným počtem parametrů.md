
### **1. Definice**  

Metody s proměnným počtem parametrů umožňují **předat libovolný počet argumentů stejného typu** do jediného parametru. Využívají klíčové slovo `params`, které označuje parametr jako pole proměnné délky.

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

### **3. Klíčové vlastnosti** 

1. **`params` musí být poslední parametr**: Metoda může mít i další parametry, ale `params` musí být na konci.  
   ```csharp
   public static void Vypis(string zprava, params object[] hodnoty) { ... }
   ```  
2. **Typ parametru**: Vždy pole (`T[]`).  
3. **Volitelné argumenty**: Lze předat 0 až N hodnot.  
4. **Kombinace s pevnými parametry**:  
   ```csharp
   public static string SpojRetezce(string oddelovac, params string[] slova) 
   {
       return string.Join(oddelovac, slova);
   }

   // Použití:
   string veta = SpojRetezce(" ", "Ahoj", "světe!"); // "Ahoj světe!"
   ```

---

### **4. Omezení**  

- **Pouze jeden `params` parametr** na metodu.  
- **Nelze kombinovat s `ref`/`out`**:  
  ```csharp
  // Chyba: Nelze použít params s ref/out
  public static void Test(params ref int[] cisla) { ... }
  ```  
- **Neplatí pro vícerozměrná pole**: `params` funguje pouze pro 1D pole.  

---

### **5. Běžné scénáře použití**  

- **Matematické operace**: Např. sčítání N čísel.  
- **Formátování textu**: Spojování řetězců s různými hodnotami.  
- **Logování**: Metody přijímající proměnlivý počet parametrů pro výstup.  

---

### **6. Příklad s různými typy**  

```csharp
public static void Loguj(string format, params object[] args) 
{
    Console.WriteLine(format, args);
}

// Použití:
Loguj("Uživatel {0} má {1} let.", "Anna", 25); // "Uživatel Anna má 25 let."
```

---

### **7. Rozdíl mezi `params` a předáním pole**  

- **`params`**: Umožňuje předat hodnoty jako samostatné argumenty.  
- **Explicitní pole**: Pokud metoda nemá `params`, musíte předat pole.  
  ```csharp
  // Bez params:
  public static int SoucetPole(int[] cisla) { ... }
  SoucetPole(new int[] { 1, 2, 3 }); // Nutné vytvořit pole

  // S params:
  Soucet(1, 2, 3); // Pole se vytvoří automaticky
  ```

---

### **8. Doporučené postupy**  

1. **Používejte pro logicky související hodnoty**: Např. seznam čísel, slov.  
2. **Vyhněte se nadužívání**: Pokud je počet parametrů předvídatelný, použijte přetížení metod.  
3. **Dokumentujte chování**: Uveďte, jak metoda zpracovává proměnný počet argumentů.  

---

### **9. Běžné chyby**  

- **Umístění `params` na špatném místě**:  
  ```csharp
  public static void Chyba(params int[] cisla, string text) { ... } // Chyba: params musí být poslední
  ```  
- **Neošetřené prázdné pole**: Pokud není zajištěna validace, může metoda selhat.  

---

### **10. Shrnutí**  

| **Vlastnost**       | **Popis**                                  |  
|----------------------|--------------------------------------------|  
| **Klíčové slovo**    | `params`                                   |  
| **Typ parametru**    | Jednorozměrné pole (`T[]`)                 |  
| **Volání**           | Lze předat 0 až N hodnot nebo explicitní pole |  
| **Omezení**          | Pouze jeden `params` parametr na metodu    |  
