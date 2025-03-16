
### **1. Základní struktura programu**

#### **Hierarchie komponent**:

1. **Namespace** → **Třídy/Struktury/Rozhraní** → **Metody/Vlastnosti/Pole** → **Výkazy**.
2. **Příklad**:
   ```csharp
   using System; // Namespace pro základní funkce

   namespace MojeAplikace // Organizace kódu
   {
       public class Program // Hlavní třída
       {
           static void Main(string[] args) // Vstupní bod programu
           {
               Console.WriteLine("Ahoj světe!"); // Výkaz
           }
       }
   }
   ```

---

### **2. Klíčové prvky**

#### **a) Vstupní bod (`Main` metoda)**:

- **Povinná metoda** pro spuštění programu.
- **Varianty**:
  ```csharp
  static void Main() { ... }
  static void Main(string[] args) { ... } // S argumenty příkazové řádky
  static int Main() { ... } // Návratový kód
  static async Task Main() { ... } // Asynchronní verze
  ```

#### **b) Namespaces**:

- Organizují kód do logických skupin.
- **Použití**:
  ```csharp
  using System.Collections.Generic; // Import namespace
  namespace MojeAplikace.Data { ... } // Vlastní namespace
  ```

#### **c) Třídy (`class`)**:

- Základní stavební jednotka OOP.
- **Příklad**:
  ```csharp
  public class Uzivatel {
      public string Jmeno { get; set; } // Vlastnost
      public void PredstavSe() => Console.WriteLine(Jmeno); // Metoda
  }
  ```

#### **d) Metody**:

- Definují chování objektů.
- **Syntax**:
  ```csharp
  public int Secti(int a, int b) {
      return a + b;
  }
  ```

---

### **3. Souborová struktura**

#### **Typické soubory**:

- **`.csproj`**: Konfigurace projektu (závislosti, verze .NET).
- **`Program.cs`**: Hlavní soubor s metodou `Main`.
- **`*.cs`**: Ostatní třídy/komponenty.

#### **Příklad `.csproj`**:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>net8.0</TargetFramework>
  </PropertyGroup>
</Project>
```

---

### **4. Organizace kódu**

#### **a) Regiony (`#region`)**: 

- Skupinují kód pro lepší čitelnost v IDE.
  ```csharp
  #region Inicializace
  private void Inicializuj() { ... }
  #endregion
  ```

#### **b) Komentáře**:

- **XML dokumentace** (pro API):
  ```csharp
  /// <summary>
  /// Metoda pro výpočet součtu.
  /// </summary>
  ```
- **Jednořádkové/Blokové**:
  ```csharp
  // Toto je komentář
  /* Toto je 
     víceřádkový komentář */
  ```

#### **c) Assembly (sestavení)**:

- Kompilovaný výstup (EXE/DLL).
- **Metadata**: Informace o verzích, závislostech.

---

### **5. Příklady struktur**

#### **Konzolová aplikace**:

```csharp
using System;

namespace Calculator
{
    class Program
    {
        static void Main()
        {
            Console.WriteLine("Výsledek: " + Secti(5, 3));
        }

        static int Secti(int a, int b) => a + b;
    }
}
```

#### **ASP.NET Core Web API**:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/", () => "Hello World!");
app.Run();
```

---

### **6. Životní cyklus programu**

1. **Kompilace**: C# → IL kód (`.exe`/`.dll`).
2. **Spuštění**: CLR (Common Language Runtime) převede IL kód do nativního kódu (JIT kompilace).
3. **Provádění**: Řízeno CLR (správa paměti, výjimky).

---

### **7. Důležité koncepty**

#### **a) Access Modifiers**:

- `public`, `private`, `protected`, `internal` – řídí viditelnost členů.
- **Příklad**:
  ```csharp
  private string _tajnyKlic; // Viditelný pouze uvnitř třídy
  ```

#### **b) Závislosti**:

- **NuGet balíčky**: Externí knihovny (např. `Newtonsoft.Json`).
- **Projektové reference**:
  ```xml
  <ItemGroup>
    <ProjectReference Include="..\Shared\Shared.csproj" />
  </ItemGroup>
  ```

---

### **8. Doporučené postupy**

- **Rozdělení do vrstev**: Např. `MojeAplikace.Web`, `MojeAplikace.Data`.
- **SOLID principy**: Jedna zodpovědnost třídy, otevřenost/zavřenost.
- **Pojmenování**: Konvence jako `PascalCase` pro třídy, `camelCase` pro proměnné.

---

### **9. Časté chyby**

- **Chybějící `Main` metoda**: Program nelze spustit.
- **Neimportovaný namespace**: Např. `Console` bez `using System;`.
- **Nesprávný access modifier**: Např. `private` metoda, která má být volána z jiné třídy.

---

### **10. Ukázka komplexní struktury**

```csharp
// Program.cs
using MojeAplikace.Services;

var service = new UserService();
service.RegisterUser("Karel");

// Services/UserService.cs
namespace MojeAplikace.Services;
public class UserService {
    public void RegisterUser(string jmeno) {
        // Logika registrace
    }
}
```
