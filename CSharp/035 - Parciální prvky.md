
### **1. Parciální tøídy (Partial Classes)**  

#### **Definice**  

Umožòují rozdìlit definici tøídy do **více souborù**. Všechny èásti musí být oznaèeny klíèovým slovem `partial` a spojeny pøi kompilaci.  

#### **Syntaxe**  

```csharp
// Soubor: Uzivatel.cs
public partial class Uzivatel {
    public string Jmeno { get; set; }
}

// Soubor: UzivatelLogika.cs
public partial class Uzivatel {
    public void UlozDoDB() { /* ... */ }
}
```

#### **Vlastnosti**  

- Všechny èásti musí být ve **stejném namespace** a **sestavení (assembly)**.  
- Lze kombinovat s dìdièností, rozhraními a atributy.  
- Pokud jedna èást oznaèí tøídu jako `abstract` nebo `sealed`, platí pro celou tøídu.  

#### **Použití**  

- Organizace velkých tøíd (napø. oddìlení UI logiky od datových polí).  
- Práce s automaticky generovaným kódem (napø. WinForms, WPF, Entity Framework).  

---

### **2. Parciální metody (Partial Methods)**  

#### **Definice**  

Metody, které jsou **deklarovány v jedné èásti tøídy** a **implementovány v jiné**. Pokud nejsou implementovány, kompilátor je odstraní.  

#### **Syntaxe**  

```csharp
// Deklarace (napø. v generované èásti)
public partial class Uzivatel {
    partial void PredUlozenim(); // Bez tìla
}

// Implementace (napø. v ruènì psané èásti)
public partial class Uzivatel {
    partial void PredUlozenim() {
        Console.WriteLine("Kontrola dat...");
    }
}
```

#### **Vlastnosti**  

- Musí mít návratový typ `void`.  
- Nelze použít `public`, `private` atd. – implicitnì jsou `private`.  
- Pokud není implementována, kompilátor ji odstraní (žádná chyba).  

#### **Použití**  

- Volitelné rozšíøení funkcionality v generovaném kódu.  
- "Hooky" pro vlastní logiku (napø. validace pøed uložením).  

---

### **3. Parciální struktury (Partial Structs)**  

- Podobné parciálním tøídám, ale pro struktury.  
- Lze rozdìlit definici struktury do více souborù.  
```csharp
public partial struct Bod { ... }
```

---

### **4. Výhody parciálních prvkù**  

- **Organizace kódu**: Rozdìlení tøíd do logických celkù.  
- **Oddìlení generovaného a ruèního kódu**: Napø. v EF, WPF.  
- **Týmová spolupráce**: Více vývojáøù mùže pracovat na jedné tøídì.  

---

### **5. Bìžné chyby a omezení**  

- **Nekonzistentní modifikátory**:  
  ```csharp
  public partial class Trida { } 
  internal partial class Trida { } // Chyba: Rùzné úrovnì pøístupu!
  ```  
- **Rozdìlení mezi assembly**: Nelze rozdìlit tøídu mezi rùzné projekty.  
- **Parciální metody nelze použít v rozhraních nebo jako veøejné**.  

---

### **6. Ukázky reálného použití**  

#### **WPF / WinForms – oddìlení UI a logiky**  

```csharp
// Automaticky generovaný kód (napø. MainWindow.designer.cs)
public partial class MainWindow : Window {
    // Inicializace komponent
}

// Ruènì psaný kód (MainWindow.xaml.cs)
public partial class MainWindow : Window {
    private void Button_Click(object sender, EventArgs e) { ... }
}
```

#### **Entity Framework – rozšíøení generované tøídy**  

```csharp
// Generovaná tøída (User.cs)
public partial class User {
    public int Id { get; set; }
}

// Ruèní rozšíøení (UserExtensions.cs)
public partial class User {
    public string FullName => $"{FirstName} {LastName}";
}
```

---

### **7. Shrnutí**  

| **Prvek**          | **Popis**                                  | **Použití**                          |  
|---------------------|--------------------------------------------|---------------------------------------|  
| **Parciální tøída** | Rozdìlení tøídy do více souborù            | Velké tøídy, generovaný kód           |  
| **Parciální metoda**| Volitelná metoda s možnou implementací      | Hooky v generovaném kódu              |  
| **Parciální struktura** | Rozdìlení struktury                  | Organizace velkých struktur           |  

---

### **8. Doporuèené zdroje**  

- **Oficiální dokumentace**: [Partial Classes and Methods](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)  
