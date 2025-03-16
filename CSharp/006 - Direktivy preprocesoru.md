
### **1. Co jsou direktivy preprocesoru?**

- **Instrukce pro kompilátor**, které se vyhodnocují **pøed kompilací** kódu.
- **Úèel**:
  - Podmínìná kompilace (výbìr kódu dle kontextu).
  - Generování varování/chyb.
  - Správa kódových oblastí (napø. pro lepší èitelnost).
  - Øízení chování kompilátoru (napø. potlaèení varování).
- **Syntaxe**: Zaèínají znakem `#` a neukonèují se støedníkem.

---

### **2. Klíèové direktivy a jejich použití**

#### **a) Podmínìná kompilace**

- **`#define` / `#undef`**: Definuje/ruší symbol pro podmínìnou kompilaci.
  ```csharp
  #define DEBUG
  #undef RELEASE
  ```
  
- **`#if`**, **`#elif`**, **`#else`**, **`#endif`**: Podmínìnì zahrne nebo vynechá kód.
  ```csharp
  #if DEBUG
      Console.WriteLine("Debug režim");
  #elif RELEASE
      Console.WriteLine("Produkèní režim");
  #else
      Console.WriteLine("Neznámý režim");
  #endif
  ```

#### **b) Generování zpráv**

- **`#warning`**: Vygeneruje varování pøi kompilaci.
  ```csharp
  #warning Tato metoda je experimentální!
  ```
  
- **`#error`**: Vyvolá chybu kompilace.
  ```csharp
  #error Neplatná konfigurace!
  ```

#### **c) Organizace kódu**

- **`#region` / `#endregion`**: Skupinuje kód do sbalitelných oblastí (napø. v IDE).
  ```csharp
  #region Inicializace
  public void Init() { /* ... */ }
  #endregion
  ```

#### **d) Øízení kompilátoru**

- **`#pragma warning`**: Potlaèí nebo obnoví varování.
  ```csharp
  #pragma warning disable CS0168 // Potlaèí varování o nepoužité promìnné
  string unused;
  #pragma warning restore CS0168
  ```
  
- **`#line`**: Upraví èíslo øádku v chybových hláškách (užiteèné pro generované kódy).
  ```csharp
  #line 200 "SpecialniSoubor.cs"
  ```

#### **e) Nullable kontext (C# 8+)**

- **`#nullable enable` / `#nullable disable`**: Zapne/vypne kontrolu nullable referenèních typù.
  ```csharp
  #nullable enable
  string? text = null; // Povoleno
  #nullable disable
  ```

---

### **3. Bìžné scénáøe použití**

- **Podmínìný kód pro rùzné prostøedí** (DEBUG vs. RELEASE).
- **Doèasné potlaèení varování** u nehotového kódu.
- **Více verzí kódu** (napø. podpora pro rùzné platformy).
- **Zvýraznìní èástí kódu** pomocí regionù.

---

### **4. Ukázky kódu**

#### **Podmínìná kompilace pro ladìní**

```csharp
#define LOGGING

using System;

public class Program {
    public static void Main() {
        #if LOGGING
            Log("Aplikace spuštìna");
        #endif
    }
    
    #if LOGGING
    private static void Log(string message) {
        Console.WriteLine($"[LOG] {message}");
    }
    #endif
}
```

#### **Potlaèení varování**

```csharp
#pragma warning disable CS0219 // Nepoužitá promìnná
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
- **Lokální platnost symbolù**: Symboly definované pomocí `#define` platí **pouze v aktuálním souboru**.
- **Kombinace s konstantami**: Pro složitìjší podmínky použijte `#if` s `const`.
  ```csharp
  const int Version = 2;
  #if Version > 1
      // Kód pro verzi 2+
  #endif
  ```

---

### **6. Tipy pro praxi**

- **Debug vs. Release**: Využijte pøeddefinované symboly `DEBUG` a `TRACE`.
- **IDE podpora**: Vizuální zvýraznìní regionù a podmínìného kódu v editoru.
- **Alternativy k `#if`**: Pro podmínìné chování za bìhu použijte `if` s konstantami.

---

### **7. Rozdíly oproti jiným jazykùm**

| **Vlastnost**       | **C#**                            | **C/C++**                  |
|----------------------|-----------------------------------|----------------------------|
| Makra                | Nejsou podporována               | Podporována (`#define`)    |
| Podmínìná kompilace  | Podpora `#if`, `#define`         | Podpora komplexních maker  |
| `#pragma`            | Øízení varování a optimalizací   | Širší použití (napø. `once`) |
