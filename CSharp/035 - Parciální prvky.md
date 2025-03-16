
### **1. Parciální třídy (Partial Classes)**  

#### **Definice**  

Umožňují rozdělit definici třídy do **více souborů**. Všechny části musí být označeny klíčovým slovem `partial` a spojeny při kompilaci.  

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

- Všechny části musí být ve **stejném namespace** a **sestavení (assembly)**.  
- Lze kombinovat s dědičností, rozhraními a atributy.  
- Pokud jedna část označí třídu jako `abstract` nebo `sealed`, platí pro celou třídu.  

#### **Použití**  

- Organizace velkých tříd (např. oddělení UI logiky od datových polí).  
- Práce s automaticky generovaným kódem (např. WinForms, WPF, Entity Framework).  

---

### **2. Parciální metody (Partial Methods)**  

#### **Definice**  

Metody, které jsou **deklarovány v jedné části třídy** a **implementovány v jiné**. Pokud nejsou implementovány, kompilátor je odstraní.  

#### **Syntaxe**  

```csharp
// Deklarace (např. v generované části)
public partial class Uzivatel {
    partial void PredUlozenim(); // Bez těla
}

// Implementace (např. v ručně psané části)
public partial class Uzivatel {
    partial void PredUlozenim() {
        Console.WriteLine("Kontrola dat...");
    }
}
```

#### **Vlastnosti**  

- Musí mít návratový typ `void`.  
- Nelze použít `public`, `private` atd. – implicitně jsou `private`.  
- Pokud není implementována, kompilátor ji odstraní (žádná chyba).  

#### **Použití**  

- Volitelné rozšíření funkcionality v generovaném kódu.  
- "Hooky" pro vlastní logiku (např. validace před uložením).  

---

### **3. Parciální struktury (Partial Structs)**  

- Podobné parciálním třídám, ale pro struktury.  
- Lze rozdělit definici struktury do více souborů.  
```csharp
public partial struct Bod { ... }
```

---

### **4. Výhody parciálních prvků**  

- **Organizace kódu**: Rozdělení tříd do logických celků.  
- **Oddělení generovaného a ručního kódu**: Např. v EF, WPF.  
- **Týmová spolupráce**: Více vývojářů může pracovat na jedné třídě.  

---

### **5. Běžné chyby a omezení**  

- **Nekonzistentní modifikátory**:  
  ```csharp
  public partial class Trida { } 
  internal partial class Trida { } // Chyba: Různé úrovně přístupu!
  ```  
- **Rozdělení mezi assembly**: Nelze rozdělit třídu mezi různé projekty.  
- **Parciální metody nelze použít v rozhraních nebo jako veřejné**.  

---

### **6. Ukázky reálného použití**  

#### **WPF / WinForms – oddělení UI a logiky**  

```csharp
// Automaticky generovaný kód (např. MainWindow.designer.cs)
public partial class MainWindow : Window {
    // Inicializace komponent
}

// Ručně psaný kód (MainWindow.xaml.cs)
public partial class MainWindow : Window {
    private void Button_Click(object sender, EventArgs e) { ... }
}
```

#### **Entity Framework – rozšíření generované třídy**  

```csharp
// Generovaná třída (User.cs)
public partial class User {
    public int Id { get; set; }
}

// Ruční rozšíření (UserExtensions.cs)
public partial class User {
    public string FullName => $"{FirstName} {LastName}";
}
```

---

### **7. Shrnutí**  

| **Prvek**          | **Popis**                                  | **Použití**                          |  
|---------------------|--------------------------------------------|---------------------------------------|  
| **Parciální třída** | Rozdělení třídy do více souborů            | Velké třídy, generovaný kód           |  
| **Parciální metoda**| Volitelná metoda s možnou implementací      | Hooky v generovaném kódu              |  
| **Parciální struktura** | Rozdělení struktury                  | Organizace velkých struktur           |  

---

### **8. Doporučené zdroje**  

- **Oficiální dokumentace**: [Partial Classes and Methods](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)  
