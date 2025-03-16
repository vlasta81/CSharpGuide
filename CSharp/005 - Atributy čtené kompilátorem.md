
### **1. Co jsou atributy �ten� kompil�torem?**

- **Atributy** poskytuj� **metadatovou informaci** o k�du, kterou kompil�tor vyu��v� pro:
  - Kontrolu k�du (nap�. upozorn�n� na zastaral� metody).
  - Optimalizace (nap�. vkl�d�n� metod).
  - Generov�n� k�du (nap�. serializace nebo interoperabilita).
- Na rozd�l od b�n�ch atribut� (�ten�ch za b�hu) **ovliv�uj� p��mo proces kompilace**.

---

### **2. Kl��ov� atributy a jejich pou�it�**

#### **a) `[Obsolete]`**

- **��el**: Ozna�uje zastaral� k�d. Vyvol� **varov�n� nebo chybu** p�i kompilaci.
- **Parametry**:
  - `message`: Zpr�va pro v�voj��e.
  - `isError`: Pokud `true`, kompilace sel�e.
  ```csharp
  [Obsolete("Pou�ij metodu Nov�Metoda()", isError: false)]
  public void StaraMetoda() { }
  ```

#### **b) `[Conditional]`**

- **��el**: Podm�n�n� kompilace metod. Vol�n� metody se **ignoruje**, pokud nen� definov�n zadan� symbol.
- **P��klad**:
  ```csharp
  #define DEBUG
  
  [Conditional("DEBUG")]
  public void Log(string message) {
      Console.WriteLine(message);
  }
  
  // Vol�n� Log() se kompiluje pouze v DEBUG re�imu.
  ```

#### **c) `[CallerMemberName]`, `[CallerFilePath]`, `[CallerLineNumber]`**

- **��el**: Automatick� dopln�n� informac� o volaj�c�m k�du (vyu�ito nap�. v MVVM pro `INotifyPropertyChanged`).
- **P��klad**:
  ```csharp
  public void NotifyPropertyChanged([CallerMemberName] string propertyName = null) {
      PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
  }
  ```

#### **d) `[DllImport]`**

- **��el**: Pro **interoperabilitu** s nespravovan�m k�dem (nap�. vol�n� funkc� z DLL).
  ```csharp
  [DllImport("user32.dll")]
  public static extern int MessageBox(IntPtr hWnd, string text, string caption, int type);
  ```

#### **e) `[Nullable]` a `[NotNull]` (C# 8+)**

- **��el**: Kontrola nulovatelnosti prom�nn�ch v kontextu **nullable reference types**.
  ```csharp
  public void Process([NotNull] string input) {
      // Kompil�tor kontroluje, �e input nen� null.
  }
  ```

#### **f) `[InternalsVisibleTo]`**

- **��el**: Umo��uje **testovac�m projekt�m** p��stup k `internal` �len�m.
  ```csharp
  [assembly: InternalsVisibleTo("TestovaciProjekt")]
  ```

#### **g) `[MethodImpl]`**

- **��el**: Ur�uje chov�n� metody (nap�. optimalizace).
  ```csharp
  [MethodImpl(MethodImplOptions.AggressiveInlining)]
  public int Add(int a, int b) => a + b;
  ```

#### **h) `[FieldOffset]` (pro `StructLayout`)**

- **��el**: Definuje explicitn� rozlo�en� pol� ve struktu�e (pro interoperabilitu).
  ```csharp
  [StructLayout(LayoutKind.Explicit)]
  public struct Union {
      [FieldOffset(0)] public int Number;
      [FieldOffset(0)] public float Float;
  }
  ```

---

### **3. Jak kompil�tor pracuje s atributy?**

1. **Anal�za k�du**: Kompil�tor hled� atributy b�hem lexik�ln� a syntaktick� anal�zy.
2. **Validace**: Kontroluje spr�vnost parametr� (nap�. zda symbol v `[Conditional]` existuje).
3. **Generov�n� metadat**: Atributy se ukl�daj� do assembly (pro runtime) nebo p��mo ovliv�uj� k�d (nap�. `[Conditional]`).
4. **Optimalizace**: Nap�. vkl�d�n� metod (`[MethodImpl]`) nebo odstran�n� nepot�ebn�ch vol�n�.

---

### **4. Rozd�ly oproti atribut�m �ten�m za b�hu**

| **Vlastnost**       | **Atributy �ten� kompil�torem** | **B�n� atributy**          |
|----------------------|----------------------------------|-----------------------------|
| ��el                 | Ovliv�uj� kompilaci             | Metadata pro runtime        |
| P��klady             | `[Obsolete]`, `[Conditional]`   | `[Serializable]`, `[Authorize]` |
| Zpracov�n�           | P�i kompilaci                   | P�i b�hu aplikace (reflekce) |

---

### **5. B�n� sc�n��e pou�it�**

- **Deprekace k�du**: `[Obsolete]`.
- **Lad�n�**: `[Conditional("DEBUG")]`.
- **MVVM pattern**: `[CallerMemberName]`.
- **Optimalizace**: `[MethodImpl]`.
- **Interoperabilita**: `[DllImport]`, `[StructLayout]`.

---

### **6. Uk�zky k�du**

#### **Pou�it� `[CallerMemberName]` v MVVM**

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
            OnPropertyChanged(); // Automaticky dopln� "Name"
        }
    }
}
```

#### **Podm�n�n� kompilace s `[Conditional]`**

```csharp
public static class Logger {
    [Conditional("LOGGING")]
    public static void Log(string message) {
        File.AppendAllText("log.txt", message);
    }
}

// Vol�n� se kompiluje pouze, pokud je definov�n symbol LOGGING
Logger.Log("Aplikace spu�t�na");
```

---

### **7. Tipy a varov�n�**

- **Kombinace atribut�**: N�kter� atributy lze kombinovat (nap�. `[Obsolete]` s `[Conditional]`).
- **Verze C#**: Atributy jako `[Nullable]` funguj� a� od C# 8.
- **Chyby**: Nespr�vn� pou�it� m��e zp�sobit ne�ekan� chov�n� (nap�. `[Conditional]` u ne-void metod).
- **Optimalizace**: `[MethodImpl(MethodImplOptions.AggressiveInlining)]` m��e zv�t�it velikost k�du.

---

### **8. Doporu�en� zdroje**

- Ofici�ln� dokumentace: [Microsoft Learn - Attributes](https://learn.microsoft.com/cs-cz/dotnet/csharp/advanced-topics/reflection-and-attributes/)
- Kniha: *C# in Depth* (Jon Skeet) � kapitoly o metadatech a kompilaci.
