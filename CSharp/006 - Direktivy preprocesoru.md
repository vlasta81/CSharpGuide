
### **1. Co jsou direktivy preprocesoru?**

- **Instrukce pro kompilátor**, které se vyhodnocují **před kompilací** kódu.
- **Účel**:
  - Podmíněná kompilace (výběr kódu dle kontextu).
  - Generování varování/chyb.
  - Správa kódových oblastí (např. pro lepší čitelnost).
  - Řízení chování kompilátoru (např. potlačení varování).
- **Syntaxe**: Začínají znakem `#` a neukončují se středníkem.

---

### **2. Klíčové direktivy a jejich použití**

#### **a) Podmíněná kompilace**

- **`#define` / `#undef`**: Definuje/ruší symbol pro podmíněnou kompilaci.
  ```csharp
  #define DEBUG
  #undef RELEASE
  ```
  
- **`#if`**, **`#elif`**, **`#else`**, **`#endif`**: Podmíněně zahrne nebo vynechá kód.
  ```csharp
  #if DEBUG
      Console.WriteLine("Debug režim");
  #elif RELEASE
      Console.WriteLine("Produkční režim");
  #else
      Console.WriteLine("Neznámý režim");
  #endif
  ```

#### **b) Generování zpráv**

- **`#warning`**: Vygeneruje varování při kompilaci.
  ```csharp
  #warning Tato metoda je experimentální!
  ```
  
- **`#error`**: Vyvolá chybu kompilace.
  ```csharp
  #error Neplatná konfigurace!
  ```

#### **c) Organizace kódu**

- **`#region` / `#endregion`**: Skupinuje kód do sbalitelných oblastí (např. v IDE).
  ```csharp
  #region Inicializace
  public void Init() { /* ... */ }
  #endregion
  ```

#### **d) Řízení kompilátoru**

- **`#pragma warning`**: Potlačí nebo obnoví varování.
  ```csharp
  #pragma warning disable CS0168 // Potlačí varování o nepoužité proměnné
  string unused;
  #pragma warning restore CS0168
  ```
  
- **`#line`**: Upraví číslo řádku v chybových hláškách (užitečné pro generované kódy).
  ```csharp
  #line 200 "SpecialniSoubor.cs"
  ```

#### **e) Nullable kontext (C# 8+)**

- **`#nullable enable` / `#nullable disable`**: Zapne/vypne kontrolu nullable referenčních typů.
  ```csharp
  #nullable enable
  string? text = null; // Povoleno
  #nullable disable
  ```

---

### **3. Běžné scénáře použití**

- **Podmíněný kód pro různé prostředí** (DEBUG vs. RELEASE).
- **Dočasné potlačení varování** u nehotového kódu.
- **Více verzí kódu** (např. podpora pro různé platformy).
- **Zvýraznění částí kódu** pomocí regionů.

---

### **4. Ukázky kódu**

#### **Podmíněná kompilace pro ladění**

```csharp
#define LOGGING

using System;

public class Program {
    public static void Main() {
        #if LOGGING
            Log("Aplikace spuštěna");
        #endif
    }
    
    #if LOGGING
    private static void Log(string message) {
        Console.WriteLine($"[LOG] {message}");
    }
    #endif
}
```

#### **Potlačení varování**

```csharp
#pragma warning disable CS0219 // Nepoužitá proměnná
int test = 10;
#pragma warning restore CS0219
```

#### **Nullable kontext**

```csharp
#nullable enable
public class Example {
    public string? Name { get; set; } // Povoleno
}
#nullable disable
```

---

### **5. Omezení a varování**

- **Žádné makra**: Na rozdíl od C/C++ nelze definovat makra s hodnotami.
- **Lokální platnost symbolů**: Symboly definované pomocí `#define` platí **pouze v aktuálním souboru**.
- **Kombinace s konstantami**: Pro složitější podmínky použijte `#if` s `const`.
  ```csharp
  const int Version = 2;
  #if Version > 1
      // Kód pro verzi 2+
  #endif
  ```

---

### **6. Tipy pro praxi**

- **Debug vs. Release**: Využijte předdefinované symboly `DEBUG` a `TRACE`.
- **IDE podpora**: Vizuální zvýraznění regionů a podmíněného kódu v editoru.
- **Alternativy k `#if`**: Pro podmíněné chování za běhu použijte `if` s konstantami.

---

### **7. Rozdíly oproti jiným jazykům**

| **Vlastnost**       | **C#**                            | **C/C++**                  |
|----------------------|-----------------------------------|----------------------------|
| Makra                | Nejsou podporována               | Podporována (`#define`)    |
| Podmíněná kompilace  | Podpora `#if`, `#define`         | Podpora komplexních maker  |
| `#pragma`            | Řízení varování a optimalizací   | Širší použití (např. `once`) |
