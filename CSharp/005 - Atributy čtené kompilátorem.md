
### **1. Co jsou atributy čtené kompilátorem?**

- **Atributy** poskytují **metadatovou informaci** o kódu, kterou kompilátor využívá pro:
  - Kontrolu kódu (např. upozornění na zastaralé metody).
  - Optimalizace (např. vkládání metod).
  - Generování kódu (např. serializace nebo interoperabilita).
- Na rozdíl od běžných atributů (čtených za běhu) **ovlivňují přímo proces kompilace**.

---

### **2. Klíčové atributy a jejich použití**

#### **a) `[Obsolete]`**

- **Účel**: Označuje zastaralý kód. Vyvolá **varování nebo chybu** při kompilaci.
- **Parametry**:
  - `message`: Zpráva pro vývojáře.
  - `isError`: Pokud `true`, kompilace selže.
  ```csharp
  [Obsolete("Použij metodu NováMetoda()", isError: false)]
  public void StaraMetoda() { }
  ```

#### **b) `[Conditional]`**

- **Účel**: Podmíněná kompilace metod. Volání metody se **ignoruje**, pokud není definován zadaný symbol.
- **Příklad**:
  ```csharp
  #define DEBUG
  
  [Conditional("DEBUG")]
  public void Log(string message) {
      Console.WriteLine(message);
  }
  
  // Volání Log() se kompiluje pouze v DEBUG režimu.
  ```

#### **c) `[CallerMemberName]`, `[CallerFilePath]`, `[CallerLineNumber]`**

- **Účel**: Automatické doplnění informací o volajícím kódu (využito např. v MVVM pro `INotifyPropertyChanged`).
- **Příklad**:
  ```csharp
  public void NotifyPropertyChanged([CallerMemberName] string propertyName = null) {
      PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
  }
  ```

#### **d) `[DllImport]`**

- **Účel**: Pro **interoperabilitu** s nespravovaným kódem (např. volání funkcí z DLL).
  ```csharp
  [DllImport("user32.dll")]
  public static extern int MessageBox(IntPtr hWnd, string text, string caption, int type);
  ```

#### **e) `[Nullable]` a `[NotNull]` (C# 8+)**

- **Účel**: Kontrola nulovatelnosti proměnných v kontextu **nullable reference types**.
  ```csharp
  public void Process([NotNull] string input) {
      // Kompilátor kontroluje, že input není null.
  }
  ```

#### **f) `[InternalsVisibleTo]`**

- **Účel**: Umožňuje **testovacím projektům** přístup k `internal` členům.
  ```csharp
  [assembly: InternalsVisibleTo("TestovaciProjekt")]
  ```

#### **g) `[MethodImpl]`**

- **Účel**: Určuje chování metody (např. optimalizace).
  ```csharp
  [MethodImpl(MethodImplOptions.AggressiveInlining)]
  public int Add(int a, int b) => a + b;
  ```

#### **h) `[FieldOffset]` (pro `StructLayout`)**

- **Účel**: Definuje explicitní rozložení polí ve struktuře (pro interoperabilitu).
  ```csharp
  [StructLayout(LayoutKind.Explicit)]
  public struct Union {
      [FieldOffset(0)] public int Number;
      [FieldOffset(0)] public float Float;
  }
  ```

---

### **3. Jak kompilátor pracuje s atributy?**

1. **Analýza kódu**: Kompilátor hledá atributy během lexikální a syntaktické analýzy.
2. **Validace**: Kontroluje správnost parametrů (např. zda symbol v `[Conditional]` existuje).
3. **Generování metadat**: Atributy se ukládají do assembly (pro runtime) nebo přímo ovlivňují kód (např. `[Conditional]`).
4. **Optimalizace**: Např. vkládání metod (`[MethodImpl]`) nebo odstranění nepotřebných volání.

---

### **4. Rozdíly oproti atributům čteným za běhu**

| **Vlastnost**       | **Atributy čtené kompilátorem** | **Běžné atributy**          |
|----------------------|----------------------------------|-----------------------------|
| Účel                 | Ovlivňují kompilaci             | Metadata pro runtime        |
| Příklady             | `[Obsolete]`, `[Conditional]`   | `[Serializable]`, `[Authorize]` |
| Zpracování           | Při kompilaci                   | Při běhu aplikace (reflekce) |

---

### **5. Běžné scénáře použití**

- **Deprekace kódu**: `[Obsolete]`.
- **Ladění**: `[Conditional("DEBUG")]`.
- **MVVM pattern**: `[CallerMemberName]`.
- **Optimalizace**: `[MethodImpl]`.
- **Interoperabilita**: `[DllImport]`, `[StructLayout]`.

---

### **6. Ukázky kódu**

#### **Použití `[CallerMemberName]` v MVVM**

```csharp
public class ViewModel : INotifyPropertyChanged {
    public event PropertyChangedEventHandler? PropertyChanged;
    
    protected void OnPropertyChanged([CallerMemberName] string propertyName = "") {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
    
    private string _name;
    public string Name {
        get => _name;
        set {
            _name = value;
            OnPropertyChanged(); // Automaticky doplní "Name"
        }
    }
}
```

#### **Podmíněná kompilace s `[Conditional]`**

```csharp
public static class Logger {
    [Conditional("LOGGING")]
    public static void Log(string message) {
        File.AppendAllText("log.txt", message);
    }
}

// Volání se kompiluje pouze, pokud je definován symbol LOGGING
Logger.Log("Aplikace spuštěna");
```

---

### **7. Tipy a varování**

- **Kombinace atributů**: Některé atributy lze kombinovat (např. `[Obsolete]` s `[Conditional]`).
- **Verze C#**: Atributy jako `[Nullable]` fungují až od C# 8.
- **Chyby**: Nesprávné použití může způsobit nečekané chování (např. `[Conditional]` u ne-void metod).
- **Optimalizace**: `[MethodImpl(MethodImplOptions.AggressiveInlining)]` může zvětšit velikost kódu.

---

### **8. Doporučené zdroje**

- Oficiální dokumentace: [Microsoft Learn - Attributes](https://learn.microsoft.com/cs-cz/dotnet/csharp/advanced-topics/reflection-and-attributes/)
- Kniha: *C# in Depth* (Jon Skeet) – kapitoly o metadatech a kompilaci.
