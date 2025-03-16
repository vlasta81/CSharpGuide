
### **1. Definice jmenných prostorù**  

Jmenné prostory slouží k **logickému uspoøádání kódu** a **pøedcházení konfliktùm názvù**. Skupinují tøídy, struktury, rozhraní, enumy a další typy do logických celkù.  
- **Pøíklad**:  
  ```csharp
  namespace MojeAplikace.Data {
      public class Uzivatel { ... }
  }
  ```

---

### **2. Klíèové vlastnosti**  

- **Hierarchická struktura**: Lze vytváøet vnoøené jmenné prostory.  
- **Unikátní identifikace**: Plnì kvalifikovaný název typu zahrnuje jmenný prostor (napø. `MojeAplikace.Data.Uzivatel`).  
- **Oddìlení logických vrstev**: Napø. `MojeAplikace.Models`, `MojeAplikace.Services`.  

---

### **3. Použití jmenných prostorù**  

#### **a) Deklarace jmenného prostoru**  

```csharp
namespace MojeAplikace.Komponenty {
    public class Tlaèítko { ... }
}
```

#### **b) Import pomocí `using`**  

- Zjednodušuje pøístup k typùm bez nutnosti uvádìt plný název.  
```csharp
using MojeAplikace.Komponenty;

Tlaèítko btn = new Tlaèítko(); // Místo MojeAplikace.Komponenty.Tlaèítko
```

#### **c) Vnoøené jmenné prostory**  

- **Syntaxe 1** (pomocí teèek):  
  ```csharp
  namespace MojeAplikace.Data.Modely { ... }
  ```  
- **Syntaxe 2** (vnoøené bloky):  
  ```csharp
  namespace MojeAplikace {
      namespace Data {
          public class Uzivatel { ... }
      }
  }
  ```

---

### **4. Globální jmenný prostor**  

- Typy bez explicitního jmenného prostoru patøí do **globálního jmenného prostoru**.  
- Lze k nim pøistupovat bez prefixu, ale riskují konflikty názvù.  

---

### **5. Aliasy pro jmenné prostory**  

- Øeší konflikty názvù mezi stejnì pojmenovanými typy z rùzných prostorù.  
```csharp
using UI = MojeAplikace.UserInterface;
using DB = MojeAplikace.Database;

UI.Uzivatel u1 = new UI.Uzivatel();
DB.Uzivatel u2 = new DB.Uzivatel();
```

---

### **6. Výchozí jmenné prostory v .NET** 

- **System**: Základní typy (napø. `Console`, `String`).  
- **System.Collections.Generic**: Generické kolekce (napø. `List<T>`).  
- **System.IO**: Práce se soubory (napø. `File`, `Directory`).  

---

### **7. Doporuèené postupy**  

1. **Organizujte kód podle funkcionality**:  
   - `MojeAplikace.Models` (datové tøídy),  
   - `MojeAplikace.Services` (business logika).  
2. **Používejte konvence pojmenování**:  
   - Zaènìte názvem spoleènosti/projektu (napø. `Company.Product.Modul`).  
3. **Vyhnìte se pøíliš hlubokému vnoøování**:  
   - `MojeAplikace.Data.Modely` je lepší než `MojeAplikace.Data.Internal.Modely.Core`.  
4. **Neukládejte všechny tøídy do globálního prostoru**: Zvyšuje riziko konfliktù.  

---

### **8. Bìžné chyby**  

- **Zapomenutý `using`**:  
  ```csharp
  // Chyba: "Uzivatel" není nalezen, pokud není importován
  Uzivatel u = new Uzivatel(); 
  ```  
- **Konflikty názvù**:  
  ```csharp
  using MojeAplikace.ModulA;
  using MojeAplikace.ModulB;

  // Chyba: "Trida" existuje v obou jmenných prostorech
  Trida t = new Trida(); 
  ```

---

### **9. Ukázka kódu**  

#### **Projektová struktura**  

```csharp
// Soubor: Models/Uzivatel.cs
namespace MojeAplikace.Models {
    public class Uzivatel { ... }
}

// Soubor: Services/UzivatelService.cs
namespace MojeAplikace.Services {
    using MojeAplikace.Models;

    public class UzivatelService {
        public void UlozUzivatele(Uzivatel u) { ... }
    }
}
```

#### **Øešení konfliktù aliasy**  

```csharp
using SystemConsole = System.Console;
using MyConsole = MojeAplikace.Tools.Console;

SystemConsole.WriteLine("Systemová konzole");
MyConsole.Log("Vlastní konzole");
```

---

### **10. Shrnutí**  

- **Jmenné prostory** organizují kód a chrání pøed konflikty názvù.  
- **`using` direktivy** zjednodušují práci s typy.  
- **Aliasy** øeší konflikty mezi stejnì pojmenovanými typy.  
- Dodržujte logickou strukturu pro èistý a udržovatelný kód.  
