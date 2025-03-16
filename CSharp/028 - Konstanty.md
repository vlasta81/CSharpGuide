
### **1. Definice konstant**  

Konstanty jsou **neměnné hodnoty**, které se inicializují při deklaraci a nelze je později upravit. Slouží k definování hodnot, které jsou v programu pevně dané (např. matematické konstanty, konfigurace).

---

### **2. Typy konstant**  

#### **a) `const` (kompilační konstanta)**  

- **Inicializace**: Hodnota musí být známá při **kompilaci** (literály, výrazy s literály).  
- **Použití**: Pro primitivní typy (`int`, `string`, `double`), výčty (`enum`), nebo `null`.  
- **Přístup**: Implicitně `static` (patří třídě, ne instanci).  

```csharp
public class Matematika 
{
    public const double PI = 3.1415926535;
    public const string Verze = "1.0";
}

// Použití:
double polomer = 5;
double obsah = Matematika.PI * polomer * polomer;
```

#### **b) `readonly` (runtime konstanta)**  

- **Inicializace**: Hodnota může být nastavena **při deklaraci nebo v konstruktoru**.  
- **Použití**: Pro komplexní typy nebo hodnoty závislé na runtime logice.  
- **Přístup**: Může být instanční nebo statický.  

```csharp
public class Konfigurace 
{
    public readonly string CestaKDatum;
    public static readonly int MaxPokusu = 3;

    public Konfigurace(string cesta) 
    {
        CestaKDatum = cesta; // Inicializace v konstruktoru
    }
}
```

---

### **3. Porovnání `const` vs `readonly`**  

| **Kritérium**       | `const`                     | `readonly`                  |  
|----------------------|-----------------------------|-----------------------------|  
| **Inicializace**     | Při deklaraci               | Při deklaraci nebo v konstruktoru |  
| **Typ hodnoty**      | Pouze primitivní/`null`     | Libovolný typ               |  
| **Kompilační čas**   | Hodnota známá při kompilaci | Hodnota může být určena za běhu |  
| **Paměť**            | Statická (patří třídě)      | Může být instanční/statická |  
| **Verzování**        | Změna vyžaduje rekompilaci  | Změna bez rekompilace       |  

---

### **4. `static readonly`**  

- Kombinuje výhody `static` a `readonly`:  
  - Hodnota je nastavena jednou a sdílena mezi všemi instancemi.  
  - Lze inicializovat v statickém konstruktoru.  

```csharp
public class Nastaveni 
{
    public static readonly DateTime StartDatum = new DateTime(2023, 1, 1);
}
```

---

### **5. Výčty (`enum`)**  

- Speciální typ konstant pro **pojmenované celočíselné hodnoty**.  
```csharp
public enum DenVTydnu 
{
    Pondeli = 1,
    Utery,
    Streda,
    // ...
}

// Použití:
DenVTydnu dnes = DenVTydnu.Pondeli;
```

---

### **6. Běžné chyby a tipy**  

- **Pokus o změnu konstanty**:  
  ```csharp
  Matematika.PI = 3.14; // Chyba kompilace!
  ```  
- **Neinicializované `readonly` pole**:  
  ```csharp
  public readonly int[] Cisla; // Lze měnit obsah pole (jen reference je konstantní)!
  ```  
- **Verzování**:  
  - `const` hodnoty jsou **vloženy přímo do kódu** – změna vyžaduje rekompilaci všech závislých projektů.  
  - `readonly` hodnoty jsou načteny za běhu – změna neovlivní již zkompilované projekty.  

---

### **7. Doporučené postupy**  

1. **`const` pro jednoduché hodnoty** (matematické konstanty, verze).  
2. **`readonly` pro hodnoty závislé na prostředí** (cesty, konfigurace).  
3. **Používejte `enum` pro omezené množiny hodnot** (dny v týdnu, stavy).  
4. **Vyhněte se `public const` u komplexních typů**: Může způsobit problém s verzováním.  

---

### **8. Ukázky kódu**  

#### **Kombinace `const` a `readonly`**  

```csharp
public class ApiClient 
{
    public const string BaseUrl = "https://api.example.com";
    public readonly int Timeout;

    public ApiClient(int timeout) 
    {
        Timeout = timeout; // Inicializace v konstruktoru
    }
}
```

#### **Statický konstruktor pro `static readonly`**  

```csharp
public class AppConfig 
{
    public static readonly string ConnectionString;

    static AppConfig() 
    {
        ConnectionString = LoadFromConfigFile(); // Inicializace za běhu
    }
}
```

---

### **9. Shrnutí**  

- **`const`**: Neměnná hodnota známá při kompilaci.  
- **`readonly`**: Neměnná hodnota určená za běhu.  
- **`enum`**: Pojmenované celočíselné konstanty.  
