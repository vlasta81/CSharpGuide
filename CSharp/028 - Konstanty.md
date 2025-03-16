
### **1. Definice konstant**  

Konstanty jsou **nemìnné hodnoty**, které se inicializují pøi deklaraci a nelze je pozdìji upravit. Slouí k definování hodnot, které jsou v programu pevnì dané (napø. matematické konstanty, konfigurace).

---

### **2. Typy konstant**  

#### **a) `const` (kompilaèní konstanta)**  

- **Inicializace**: Hodnota musí bıt známá pøi **kompilaci** (literály, vırazy s literály).  
- **Pouití**: Pro primitivní typy (`int`, `string`, `double`), vıèty (`enum`), nebo `null`.  
- **Pøístup**: Implicitnì `static` (patøí tøídì, ne instanci).  

```csharp
public class Matematika 
{
    public const double PI = 3.1415926535;
    public const string Verze = "1.0";
}

// Pouití:
double polomer = 5;
double obsah = Matematika.PI * polomer * polomer;
```

#### **b) `readonly` (runtime konstanta)**  

- **Inicializace**: Hodnota mùe bıt nastavena **pøi deklaraci nebo v konstruktoru**.  
- **Pouití**: Pro komplexní typy nebo hodnoty závislé na runtime logice.  
- **Pøístup**: Mùe bıt instanèní nebo statickı.  

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
| **Inicializace**     | Pøi deklaraci               | Pøi deklaraci nebo v konstruktoru |  
| **Typ hodnoty**      | Pouze primitivní/`null`     | Libovolnı typ               |  
| **Kompilaèní èas**   | Hodnota známá pøi kompilaci | Hodnota mùe bıt urèena za bìhu |  
| **Pamì**            | Statická (patøí tøídì)      | Mùe bıt instanèní/statická |  
| **Verzování**        | Zmìna vyaduje rekompilaci  | Zmìna bez rekompilace       |  

---

### **4. `static readonly`**  

- Kombinuje vıhody `static` a `readonly`:  
  - Hodnota je nastavena jednou a sdílena mezi všemi instancemi.  
  - Lze inicializovat v statickém konstruktoru.  

```csharp
public class Nastaveni 
{
    public static readonly DateTime StartDatum = new DateTime(2023, 1, 1);
}
```

---

### **5. Vıèty (`enum`)**  

- Speciální typ konstant pro **pojmenované celoèíselné hodnoty**.  
```csharp
public enum DenVTydnu 
{
    Pondeli = 1,
    Utery,
    Streda,
    // ...
}

// Pouití:
DenVTydnu dnes = DenVTydnu.Pondeli;
```

---

### **6. Bìné chyby a tipy**  

- **Pokus o zmìnu konstanty**:  
  ```csharp
  Matematika.PI = 3.14; // Chyba kompilace!
  ```  
- **Neinicializované `readonly` pole**:  
  ```csharp
  public readonly int[] Cisla; // Lze mìnit obsah pole (jen reference je konstantní)!
  ```  
- **Verzování**:  
  - `const` hodnoty jsou **vloeny pøímo do kódu** – zmìna vyaduje rekompilaci všech závislıch projektù.  
  - `readonly` hodnoty jsou naèteny za bìhu – zmìna neovlivní ji zkompilované projekty.  

---

### **7. Doporuèené postupy**  

1. **`const` pro jednoduché hodnoty** (matematické konstanty, verze).  
2. **`readonly` pro hodnoty závislé na prostøedí** (cesty, konfigurace).  
3. **Pouívejte `enum` pro omezené mnoiny hodnot** (dny v tıdnu, stavy).  
4. **Vyhnìte se `public const` u komplexních typù**: Mùe zpùsobit problém s verzováním.  

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

#### **Statickı konstruktor pro `static readonly`**  

```csharp
public class AppConfig 
{
    public static readonly string ConnectionString;

    static AppConfig() 
    {
        ConnectionString = LoadFromConfigFile(); // Inicializace za bìhu
    }
}
```

---

### **9. Shrnutí**  

- **`const`**: Nemìnná hodnota známá pøi kompilaci.  
- **`readonly`**: Nemìnná hodnota urèená za bìhu.  
- **`enum`**: Pojmenované celoèíselné konstanty.  
