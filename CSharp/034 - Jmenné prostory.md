
### **1. Definice jmenn�ch prostor�**  

Jmenn� prostory slou�� k **logick�mu uspo��d�n� k�du** a **p�edch�zen� konflikt�m n�zv�**. Skupinuj� t��dy, struktury, rozhran�, enumy a dal�� typy do logick�ch celk�.  
- **P��klad**:  
  ```csharp
  namespace MojeAplikace.Data {
      public class Uzivatel { ... }
  }
  ```

---

### **2. Kl��ov� vlastnosti**  

- **Hierarchick� struktura**: Lze vytv��et vno�en� jmenn� prostory.  
- **Unik�tn� identifikace**: Pln� kvalifikovan� n�zev typu zahrnuje jmenn� prostor (nap�. `MojeAplikace.Data.Uzivatel`).  
- **Odd�len� logick�ch vrstev**: Nap�. `MojeAplikace.Models`, `MojeAplikace.Services`.  

---

### **3. Pou�it� jmenn�ch prostor�**  

#### **a) Deklarace jmenn�ho prostoru**  

```csharp
namespace MojeAplikace.Komponenty {
    public class Tla��tko { ... }
}
```

#### **b) Import pomoc� `using`**  

- Zjednodu�uje p��stup k typ�m bez nutnosti uv�d�t pln� n�zev.  
```csharp
using MojeAplikace.Komponenty;

Tla��tko btn = new Tla��tko(); // M�sto MojeAplikace.Komponenty.Tla��tko
```

#### **c) Vno�en� jmenn� prostory**  

- **Syntaxe 1** (pomoc� te�ek):  
  ```csharp
  namespace MojeAplikace.Data.Modely { ... }
  ```  
- **Syntaxe 2** (vno�en� bloky):  
  ```csharp
  namespace MojeAplikace {
      namespace Data {
          public class Uzivatel { ... }
      }
  }
  ```

---

### **4. Glob�ln� jmenn� prostor**  

- Typy bez explicitn�ho jmenn�ho prostoru pat�� do **glob�ln�ho jmenn�ho prostoru**.  
- Lze k nim p�istupovat bez prefixu, ale riskuj� konflikty n�zv�.  

---

### **5. Aliasy pro jmenn� prostory**  

- �e�� konflikty n�zv� mezi stejn� pojmenovan�mi typy z r�zn�ch prostor�.  
```csharp
using UI = MojeAplikace.UserInterface;
using DB = MojeAplikace.Database;

UI.Uzivatel u1 = new UI.Uzivatel();
DB.Uzivatel u2 = new DB.Uzivatel();
```

---

### **6. V�choz� jmenn� prostory v .NET** 

- **System**: Z�kladn� typy (nap�. `Console`, `String`).  
- **System.Collections.Generic**: Generick� kolekce (nap�. `List<T>`).  
- **System.IO**: Pr�ce se soubory (nap�. `File`, `Directory`).  

---

### **7. Doporu�en� postupy**  

1. **Organizujte k�d podle funkcionality**:  
   - `MojeAplikace.Models` (datov� t��dy),  
   - `MojeAplikace.Services` (business logika).  
2. **Pou��vejte konvence pojmenov�n�**:  
   - Za�n�te n�zvem spole�nosti/projektu (nap�. `Company.Product.Modul`).  
3. **Vyhn�te se p��li� hlubok�mu vno�ov�n�**:  
   - `MojeAplikace.Data.Modely` je lep�� ne� `MojeAplikace.Data.Internal.Modely.Core`.  
4. **Neukl�dejte v�echny t��dy do glob�ln�ho prostoru**: Zvy�uje riziko konflikt�.  

---

### **8. B�n� chyby**  

- **Zapomenut� `using`**:  
  ```csharp
  // Chyba: "Uzivatel" nen� nalezen, pokud nen� importov�n
  Uzivatel u = new Uzivatel(); 
  ```  
- **Konflikty n�zv�**:  
  ```csharp
  using MojeAplikace.ModulA;
  using MojeAplikace.ModulB;

  // Chyba: "Trida" existuje v obou jmenn�ch prostorech
  Trida t = new Trida(); 
  ```

---

### **9. Uk�zka k�du**  

#### **Projektov� struktura**  

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

#### **�e�en� konflikt� aliasy**  

```csharp
using SystemConsole = System.Console;
using MyConsole = MojeAplikace.Tools.Console;

SystemConsole.WriteLine("Systemov� konzole");
MyConsole.Log("Vlastn� konzole");
```

---

### **10. Shrnut�**  

- **Jmenn� prostory** organizuj� k�d a chr�n� p�ed konflikty n�zv�.  
- **`using` direktivy** zjednodu�uj� pr�ci s typy.  
- **Aliasy** �e�� konflikty mezi stejn� pojmenovan�mi typy.  
- Dodr�ujte logickou strukturu pro �ist� a udr�ovateln� k�d.  
