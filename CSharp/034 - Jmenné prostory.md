
### **1. Definice jmenných prostorů**  

Jmenné prostory slouží k **logickému uspořádání kódu** a **předcházení konfliktům názvů**. Skupinují třídy, struktury, rozhraní, enumy a další typy do logických celků.  
- **Příklad**:  
  ```csharp
  namespace MojeAplikace.Data {
      public class Uzivatel { ... }
  }
  ```

---

### **2. Klíčové vlastnosti**  

- **Hierarchická struktura**: Lze vytvářet vnořené jmenné prostory.  
- **Unikátní identifikace**: Plně kvalifikovaný název typu zahrnuje jmenný prostor (např. `MojeAplikace.Data.Uzivatel`).  
- **Oddělení logických vrstev**: Např. `MojeAplikace.Models`, `MojeAplikace.Services`.  

---

### **3. Použití jmenných prostorů**  

#### **a) Deklarace jmenného prostoru**  

```csharp
namespace MojeAplikace.Komponenty {
    public class Tlačítko { ... }
}
```

#### **b) Import pomocí `using`**  

- Zjednodušuje přístup k typům bez nutnosti uvádět plný název.  
```csharp
using MojeAplikace.Komponenty;

Tlačítko btn = new Tlačítko(); // Místo MojeAplikace.Komponenty.Tlačítko
```

#### **c) Vnořené jmenné prostory**  

- **Syntaxe 1** (pomocí teček):  
  ```csharp
  namespace MojeAplikace.Data.Modely { ... }
  ```  
- **Syntaxe 2** (vnořené bloky):  
  ```csharp
  namespace MojeAplikace {
      namespace Data {
          public class Uzivatel { ... }
      }
  }
  ```

---

### **4. Globální jmenný prostor**  

- Typy bez explicitního jmenného prostoru patří do **globálního jmenného prostoru**.  
- Lze k nim přistupovat bez prefixu, ale riskují konflikty názvů.  

---

### **5. Aliasy pro jmenné prostory**  

- Řeší konflikty názvů mezi stejně pojmenovanými typy z různých prostorů.  
```csharp
using UI = MojeAplikace.UserInterface;
using DB = MojeAplikace.Database;

UI.Uzivatel u1 = new UI.Uzivatel();
DB.Uzivatel u2 = new DB.Uzivatel();
```

---

### **6. Výchozí jmenné prostory v .NET** 

- **System**: Základní typy (např. `Console`, `String`).  
- **System.Collections.Generic**: Generické kolekce (např. `List<T>`).  
- **System.IO**: Práce se soubory (např. `File`, `Directory`).  

---

### **7. Doporučené postupy**  

1. **Organizujte kód podle funkcionality**:  
   - `MojeAplikace.Models` (datové třídy),  
   - `MojeAplikace.Services` (business logika).  
2. **Používejte konvence pojmenování**:  
   - Začněte názvem společnosti/projektu (např. `Company.Product.Modul`).  
3. **Vyhněte se příliš hlubokému vnořování**:  
   - `MojeAplikace.Data.Modely` je lepší než `MojeAplikace.Data.Internal.Modely.Core`.  
4. **Neukládejte všechny třídy do globálního prostoru**: Zvyšuje riziko konfliktů.  

---

### **8. Běžné chyby**  

- **Zapomenutý `using`**:  
  ```csharp
  // Chyba: "Uzivatel" není nalezen, pokud není importován
  Uzivatel u = new Uzivatel(); 
  ```  
- **Konflikty názvů**:  
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

#### **Řešení konfliktů aliasy**  

```csharp
using SystemConsole = System.Console;
using MyConsole = MojeAplikace.Tools.Console;

SystemConsole.WriteLine("Systemová konzole");
MyConsole.Log("Vlastní konzole");
```

---

### **10. Shrnutí**  

- **Jmenné prostory** organizují kód a chrání před konflikty názvů.  
- **`using` direktivy** zjednodušují práci s typy.  
- **Aliasy** řeší konflikty mezi stejně pojmenovanými typy.  
- Dodržujte logickou strukturu pro čistý a udržovatelný kód.  
