
### **1. Parci�ln� t��dy (Partial Classes)**  

#### **Definice**  

Umo��uj� rozd�lit definici t��dy do **v�ce soubor�**. V�echny ��sti mus� b�t ozna�eny kl��ov�m slovem `partial` a spojeny p�i kompilaci.  

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

- V�echny ��sti mus� b�t ve **stejn�m namespace** a **sestaven� (assembly)**.  
- Lze kombinovat s d�di�nost�, rozhran�mi a atributy.  
- Pokud jedna ��st ozna�� t��du jako `abstract` nebo `sealed`, plat� pro celou t��du.  

#### **Pou�it�**  

- Organizace velk�ch t��d (nap�. odd�len� UI logiky od datov�ch pol�).  
- Pr�ce s automaticky generovan�m k�dem (nap�. WinForms, WPF, Entity Framework).  

---

### **2. Parci�ln� metody (Partial Methods)**  

#### **Definice**  

Metody, kter� jsou **deklarov�ny v jedn� ��sti t��dy** a **implementov�ny v jin�**. Pokud nejsou implementov�ny, kompil�tor je odstran�.  

#### **Syntaxe**  

```csharp
// Deklarace (nap�. v generovan� ��sti)
public partial class Uzivatel {
    partial void PredUlozenim(); // Bez t�la
}

// Implementace (nap�. v ru�n� psan� ��sti)
public partial class Uzivatel {
    partial void PredUlozenim() {
        Console.WriteLine("Kontrola dat...");
    }
}
```

#### **Vlastnosti**  

- Mus� m�t n�vratov� typ `void`.  
- Nelze pou��t `public`, `private` atd. � implicitn� jsou `private`.  
- Pokud nen� implementov�na, kompil�tor ji odstran� (��dn� chyba).  

#### **Pou�it�**  

- Voliteln� roz���en� funkcionality v generovan�m k�du.  
- "Hooky" pro vlastn� logiku (nap�. validace p�ed ulo�en�m).  

---

### **3. Parci�ln� struktury (Partial Structs)**  

- Podobn� parci�ln�m t��d�m, ale pro struktury.  
- Lze rozd�lit definici struktury do v�ce soubor�.  
```csharp
public partial struct Bod { ... }
```

---

### **4. V�hody parci�ln�ch prvk�**  

- **Organizace k�du**: Rozd�len� t��d do logick�ch celk�.  
- **Odd�len� generovan�ho a ru�n�ho k�du**: Nap�. v EF, WPF.  
- **T�mov� spolupr�ce**: V�ce v�voj��� m��e pracovat na jedn� t��d�.  

---

### **5. B�n� chyby a omezen�**  

- **Nekonzistentn� modifik�tory**:  
  ```csharp
  public partial class Trida { } 
  internal partial class Trida { } // Chyba: R�zn� �rovn� p��stupu!
  ```  
- **Rozd�len� mezi assembly**: Nelze rozd�lit t��du mezi r�zn� projekty.  
- **Parci�ln� metody nelze pou��t v rozhran�ch nebo jako ve�ejn�**.  

---

### **6. Uk�zky re�ln�ho pou�it�**  

#### **WPF / WinForms � odd�len� UI a logiky**  

```csharp
// Automaticky generovan� k�d (nap�. MainWindow.designer.cs)
public partial class MainWindow : Window {
    // Inicializace komponent
}

// Ru�n� psan� k�d (MainWindow.xaml.cs)
public partial class MainWindow : Window {
    private void Button_Click(object sender, EventArgs e) { ... }
}
```

#### **Entity Framework � roz���en� generovan� t��dy**  

```csharp
// Generovan� t��da (User.cs)
public partial class User {
    public int Id { get; set; }
}

// Ru�n� roz���en� (UserExtensions.cs)
public partial class User {
    public string FullName => $"{FirstName} {LastName}";
}
```

---

### **7. Shrnut�**  

| **Prvek**          | **Popis**                                  | **Pou�it�**                          |  
|---------------------|--------------------------------------------|---------------------------------------|  
| **Parci�ln� t��da** | Rozd�len� t��dy do v�ce soubor�            | Velk� t��dy, generovan� k�d           |  
| **Parci�ln� metoda**| Voliteln� metoda s mo�nou implementac�      | Hooky v generovan�m k�du              |  
| **Parci�ln� struktura** | Rozd�len� struktury                  | Organizace velk�ch struktur           |  

---

### **8. Doporu�en� zdroje**  

- **Ofici�ln� dokumentace**: [Partial Classes and Methods](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/classes-and-structs/partial-classes-and-methods)  
