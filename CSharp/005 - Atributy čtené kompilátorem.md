
### **1. Co jsou atributy ètené kompilátorem?**

- **Atributy** poskytují **metadatovou informaci** o kódu, kterou kompilátor využívá pro:
  - Kontrolu kódu (napø. upozornìní na zastaralé metody).
  - Optimalizace (napø. vkládání metod).
  - Generování kódu (napø. serializace nebo interoperabilita).
- Na rozdíl od bìžných atributù (ètených za bìhu) **ovlivòují pøímo proces kompilace**.

---

### **2. Klíèové atributy a jejich použití**

#### **a) `[Obsolete]`**

- **Úèel**: Oznaèuje zastaralý kód. Vyvolá **varování nebo chybu** pøi kompilaci.
- **Parametry**:
  - `message`: Zpráva pro vývojáøe.
  - `isError`: Pokud `true`, kompilace selže.
  ```csharp
  [Obsolete("Použij metodu NováMetoda()", isError: false)]
  public void StaraMetoda() { }
  ```

#### **b) `[Conditional]`**

- **Úèel**: Podmínìná kompilace metod. Volání metody se **ignoruje**, pokud není definován zadaný symbol.
- **Pøíklad**:
  ```csharp
  #define DEBUG
  
  [Conditional("DEBUG")]
  public void Log(string message) {
      Console.WriteLine(message);
  }
  
  // Volání Log() se kompiluje pouze v DEBUG režimu.
  ```

#### **c) `[CallerMemberName]`, `[CallerFilePath]`, `[CallerLineNumber]`**

- **Úèel**: Automatické doplnìní informací o volajícím kódu (využito napø. v MVVM pro `INotifyPropertyChanged`).
- **Pøíklad**:
  ```csharp
  public void NotifyPropertyChanged([CallerMemberName] string propertyName = null) {
      PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
  }
  ```

#### **d) `[DllImport]`**

- **Úèel**: Pro **interoperabilitu** s nespravovaným kódem (napø. volání funkcí z DLL).
  ```csharp
  [DllImport("user32.dll")]
  public static extern int MessageBox(IntPtr hWnd, string text, string caption, int type);
  ```

#### **e) `[Nullable]` a `[NotNull]` (C# 8+)**

- **Úèel**: Kontrola nulovatelnosti promìnných v kontextu **nullable reference types**.
  ```csharp
  public void Process([NotNull] string input) {
      // Kompilátor kontroluje, že input není null.
  }
  ```

#### **f) `[InternalsVisibleTo]`**

- **Úèel**: Umožòuje **testovacím projektùm** pøístup k `internal` èlenùm.
  ```csharp
  [assembly: InternalsVisibleTo("TestovaciProjekt")]
  ```

#### **g) `[MethodImpl]`**

- **Úèel**: Urèuje chování metody (napø. optimalizace).
  ```csharp
  [MethodImpl(MethodImplOptions.AggressiveInlining)]
  public int Add(int a, int b) => a + b;
  ```

#### **h) `[FieldOffset]` (pro `StructLayout`)**

- **Úèel**: Definuje explicitní rozložení polí ve struktuøe (pro interoperabilitu).
  ```csharp
  [StructLayout(LayoutKind.Explicit)]
  public struct Union {
      [FieldOffset(0)] public int Number;
      [FieldOffset(0)] public float Float;
  }
  ```

---

### **3. Jak kompilátor pracuje s atributy?**

1. **Analýza kódu**: Kompilátor hledá atributy bìhem lexikální a syntaktické analýzy.
2. **Validace**: Kontroluje správnost parametrù (napø. zda symbol v `[Conditional]` existuje).
3. **Generování metadat**: Atributy se ukládají do assembly (pro runtime) nebo pøímo ovlivòují kód (napø. `[Conditional]`).
4. **Optimalizace**: Napø. vkládání metod (`[MethodImpl]`) nebo odstranìní nepotøebných volání.

---

### **4. Rozdíly oproti atributùm èteným za bìhu**

| **Vlastnost**       | **Atributy ètené kompilátorem** | **Bìžné atributy**          |
|----------------------|----------------------------------|-----------------------------|
| Úèel                 | Ovlivòují kompilaci             | Metadata pro runtime        |
| Pøíklady             | `[Obsolete]`, `[Conditional]`   | `[Serializable]`, `[Authorize]` |
| Zpracování           | Pøi kompilaci                   | Pøi bìhu aplikace (reflekce) |

---

### **5. Bìžné scénáøe použití**

- **Deprekace kódu**: `[Obsolete]`.
- **Ladìní**: `[Conditional("DEBUG")]`.
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

#### **Podmínìná kompilace s `[Conditional]`**

```csharp
public static class Logger {
    [Conditional("LOGGING")]
    public static void Log(string message) {
        File.AppendAllText("log.txt", message);
    }
}

// Volání se kompiluje pouze, pokud je definován symbol LOGGING
Logger.Log("Aplikace spuštìna");
```

---

### **7. Tipy a varování**

- **Kombinace atributù**: Nìkteré atributy lze kombinovat (napø. `[Obsolete]` s `[Conditional]`).
- **Verze C#**: Atributy jako `[Nullable]` fungují až od C# 8.
- **Chyby**: Nesprávné použití mùže zpùsobit neèekané chování (napø. `[Conditional]` u ne-void metod).
- **Optimalizace**: `[MethodImpl(MethodImplOptions.AggressiveInlining)]` mùže zvìtšit velikost kódu.

---

### **8. Doporuèené zdroje**

- Oficiální dokumentace: [Microsoft Learn - Attributes](https://learn.microsoft.com/cs-cz/dotnet/csharp/advanced-topics/reflection-and-attributes/)
- Kniha: *C# in Depth* (Jon Skeet) – kapitoly o metadatech a kompilaci.
