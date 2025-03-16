
### **1. Úvod do cyklů**  

Cykly slouží k **opakovanému provádění bloku kódu**, dokud je splněna podmínka.  
**Základní účel**: Iterace přes kolekce, zpracování dat, opakované operace.

---

### **2. Typy cyklů**

#### **a) `for`**  

- **Použití**: Když znáte **počet iterací** (např. procházení pole indexy).  
- **Syntaxe**:  
  ```csharp
  for (inicializace; podmínka; inkrementace) { 
      // Kód 
  }
  ```
- **Příklad**:  
  ```csharp
  for (int i = 0; i < 5; i++) {
      Console.WriteLine(i); // 0, 1, 2, 3, 4
  }
  ```

#### **b) `foreach`**  

- **Použití**: Pro **iteraci přes kolekce** (pole, seznamy, atd.).  
- **Syntaxe**:  
  ```csharp
  foreach (typ proměnná in kolekce) { 
      // Kód 
  }
  ```
- **Příklad**:  
  ```csharp
  string[] jmena = { "Anna", "Karel", "Eva" };
  foreach (string jmeno in jmena) {
      Console.WriteLine(jmeno); // Anna, Karel, Eva
  }
  ```

#### **c) `while`**  

- **Použití**: Když **neznáte počet iterací** a podmínka se kontroluje **před provedením**.  
- **Syntaxe**:  
  ```csharp
  while (podmínka) { 
      // Kód 
  }
  ```
- **Příklad**:  
  ```csharp
  int x = 0;
  while (x < 3) {
      Console.WriteLine(x); // 0, 1, 2
      x++;
  }
  ```

#### **d) `do-while`**  

- **Použití**: Když potřebujete **provést kód alespoň jednou** (podmínka se kontroluje až po provedení).  
- **Syntaxe**:  
  ```csharp
  do { 
      // Kód 
  } while (podmínka);
  ```
- **Příklad**:  
  ```csharp
  int y = 5;
  do {
      Console.WriteLine(y); // 5 (provede se jednou)
  } while (y < 3);
  ```

---

### **3. Řízení cyklů**  

#### **`break`**  

- **Okamžité ukončení cyklu**.  
  ```csharp
  for (int i = 0; i < 10; i++) {
      if (i == 5) break; // Cyklus skončí při i=5
  }
  ```

#### **`continue`**  

- **Přeskočí aktuální iteraci** a pokračuje další.  
  ```csharp
  for (int i = 0; i < 5; i++) {
      if (i == 2) continue; // Přeskočí i=2
      Console.WriteLine(i); // 0, 1, 3, 4
  }
  ```

#### **`goto`** (vzácné použití)  

- Přeskočí na označený výkaz.  
  ```csharp
  for (int i = 0; i < 10; i++) {
      if (i == 5) goto Konec;
  }
  Konec: Console.WriteLine("Konec");
  ```

---

### **4. Vnořené cykly**  

- Cyklus uvnitř jiného cyklu (např. pro práci s 2D poli).  
  ```csharp
  for (int i = 0; i < 3; i++) {
      for (int j = 0; j < 3; j++) {
          Console.Write($"[{i},{j}] ");
      }
      Console.WriteLine();
  }
  // Výstup:
  // [0,0] [0,1] [0,2]
  // [1,0] [1,1] [1,2]
  // [2,0] [2,1] [2,2]
  ```

---

### **5. Běžné chyby**  

- **Nekonečné cykly** (zapomenutá změna podmínky):  
  ```csharp
  int x = 0;
  while (x < 5) { 
      // Zapomenuto x++ → nekonečný cyklus!
  }
  ```

- **Modifikace kolekce v `foreach`**:  
  ```csharp
  List<int> cisla = new List<int> { 1, 2, 3 };
  foreach (int c in cisla) {
      cisla.Remove(c); // Vyvolá InvalidOperationException!
  }
  ```

---

### **6. Porovnání cyklů** 

| **Kritérium**       | `for`                  | `foreach`              | `while`               | `do-while`            |  
|----------------------|------------------------|------------------------|-----------------------|-----------------------|  
| **Počet iterací**    | Známý předem          | Podle kolekce          | Neznámý               | Neznámý (alespoň 1×) |  
| **Přístup k indexu** | Ano (pomocí `i`)      | Ne                     | Ne (lze přidat ručně) | Ne                    |  
| **Výkon**            | Nejrychlejší          | Mírně pomalejší        | Závisí na podmínce    | Závisí na podmínce    |  

---

### **7. Tipy pro efektivní použití**  

1. **Volba správného cyklu**:  
   - `for` pro indexy, `foreach` pro kolekce, `while` pro neznámý počet iterací.  
2. **Používej `break` a `continue` opatrně**: Mohou snížit čitelnost.  
3. **Optimalizace**: Pro velké kolekce preferuj `for` místo `foreach`.  
4. **LINQ jako alternativa**:  
   ```csharp
   var sudaCisla = cisla.Where(x => x % 2 == 0).ToList(); // Místo cyklu
   ```

---

### **8. Ukázky kódu**  

#### **Hledání prvku v poli**  

```csharp
int[] pole = { 10, 20, 30, 40 };
bool nalezeno = false;
for (int i = 0; i < pole.Length; i++) {
    if (pole[i] == 30) {
        nalezeno = true;
        break;
    }
}
Console.WriteLine(nalezeno ? "Nalezeno" : "Nenalezeno"); // "Nalezeno"
```

#### **Čtení souboru s `while`**  

```csharp
using (var reader = new StreamReader("soubor.txt")) {
    string? radek;
    while ((radek = reader.ReadLine()) != null) {
        Console.WriteLine(radek);
    }
}
```

---

### **9. Doporučené zdroje**  

- **Oficiální dokumentace**: [C# Iteration Statements](https://learn.microsoft.com/cs-cz/dotnet/csharp/language-reference/statements/iteration-statements)  
