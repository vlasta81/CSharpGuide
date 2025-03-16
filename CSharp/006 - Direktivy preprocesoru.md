
### **1. Co jsou direktivy preprocesoru?**

- **Instrukce pro kompil�tor**, kter� se vyhodnocuj� **p�ed kompilac�** k�du.
- **��el**:
  - Podm�n�n� kompilace (v�b�r k�du dle kontextu).
  - Generov�n� varov�n�/chyb.
  - Spr�va k�dov�ch oblast� (nap�. pro lep�� �itelnost).
  - ��zen� chov�n� kompil�toru (nap�. potla�en� varov�n�).
- **Syntaxe**: Za��naj� znakem `#` a neukon�uj� se st�edn�kem.

---

### **2. Kl��ov� direktivy a jejich pou�it�**

#### **a) Podm�n�n� kompilace**

- **`#define` / `#undef`**: Definuje/ru�� symbol pro podm�n�nou kompilaci.
  ```csharp
  #define DEBUG
  #undef RELEASE
  ```
  
- **`#if`**, **`#elif`**, **`#else`**, **`#endif`**: Podm�n�n� zahrne nebo vynech� k�d.
  ```csharp
  #if DEBUG
      Console.WriteLine("Debug re�im");
  #elif RELEASE
      Console.WriteLine("Produk�n� re�im");
  #else
      Console.WriteLine("Nezn�m� re�im");
  #endif
  ```

#### **b) Generov�n� zpr�v**

- **`#warning`**: Vygeneruje varov�n� p�i kompilaci.
  ```csharp
  #warning Tato metoda je experiment�ln�!
  ```
  
- **`#error`**: Vyvol� chybu kompilace.
  ```csharp
  #error Neplatn� konfigurace!
  ```

#### **c) Organizace k�du**

- **`#region` / `#endregion`**: Skupinuje k�d do sbaliteln�ch oblast� (nap�. v IDE).
  ```csharp
  #region Inicializace
  public void Init() { /* ... */ }
  #endregion
  ```

#### **d) ��zen� kompil�toru**

- **`#pragma warning`**: Potla�� nebo obnov� varov�n�.
  ```csharp
  #pragma warning disable CS0168 // Potla�� varov�n� o nepou�it� prom�nn�
  string unused;
  #pragma warning restore CS0168
  ```
  
- **`#line`**: Uprav� ��slo ��dku v chybov�ch hl�k�ch (u�ite�n� pro generovan� k�dy).
  ```csharp
  #line 200 "SpecialniSoubor.cs"
  ```

#### **e) Nullable kontext (C# 8+)**

- **`#nullable enable` / `#nullable disable`**: Zapne/vypne kontrolu nullable referen�n�ch typ�.
  ```csharp
  #nullable enable
  string? text = null; // Povoleno
  #nullable disable
  ```

---

### **3. B�n� sc�n��e pou�it�**

- **Podm�n�n� k�d pro r�zn� prost�ed�** (DEBUG vs. RELEASE).
- **Do�asn� potla�en� varov�n�** u nehotov�ho k�du.
- **V�ce verz� k�du** (nap�. podpora pro r�zn� platformy).
- **Zv�razn�n� ��st� k�du** pomoc� region�.

---

### **4. Uk�zky k�du**

#### **Podm�n�n� kompilace pro lad�n�**

```csharp
#define LOGGING

using System;

public class Program {
    public static void Main() {
        #if LOGGING
            Log("Aplikace spu�t�na");
        #endif
    }
    
    #if LOGGING
    private static void Log(string message) {
        Console.WriteLine($"[LOG] {message}");
    }
    #endif
}
```

#### **Potla�en� varov�n�**

```csharp
#pragma warning disable CS0219 // Nepou�it� prom�nn�
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

### **5. Omezen� a varov�n�**

- **��dn� makra**: Na rozd�l od C/C++ nelze definovat makra s hodnotami.
- **Lok�ln� platnost symbol�**: Symboly definovan� pomoc� `#define` plat� **pouze v aktu�ln�m souboru**.
- **Kombinace s konstantami**: Pro slo�it�j�� podm�nky pou�ijte `#if` s `const`.
  ```csharp
  const int Version = 2;
  #if Version > 1
      // K�d pro verzi 2+
  #endif
  ```

---

### **6. Tipy pro praxi**

- **Debug vs. Release**: Vyu�ijte p�eddefinovan� symboly `DEBUG` a `TRACE`.
- **IDE podpora**: Vizu�ln� zv�razn�n� region� a podm�n�n�ho k�du v editoru.
- **Alternativy k `#if`**: Pro podm�n�n� chov�n� za b�hu pou�ijte `if` s konstantami.

---

### **7. Rozd�ly oproti jin�m jazyk�m**

| **Vlastnost**       | **C#**                            | **C/C++**                  |
|----------------------|-----------------------------------|----------------------------|
| Makra                | Nejsou podporov�na               | Podporov�na (`#define`)    |
| Podm�n�n� kompilace  | Podpora `#if`, `#define`         | Podpora komplexn�ch maker  |
| `#pragma`            | ��zen� varov�n� a optimalizac�   | �ir�� pou�it� (nap�. `once`) |
