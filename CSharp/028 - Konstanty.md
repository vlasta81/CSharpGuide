
### **1. Definice konstant**  

Konstanty jsou **nem�nn� hodnoty**, kter� se inicializuj� p�i deklaraci a nelze je pozd�ji upravit. Slou�� k definov�n� hodnot, kter� jsou v programu pevn� dan� (nap�. matematick� konstanty, konfigurace).

---

### **2. Typy konstant**  

#### **a) `const` (kompila�n� konstanta)**  

- **Inicializace**: Hodnota mus� b�t zn�m� p�i **kompilaci** (liter�ly, v�razy s liter�ly).  
- **Pou�it�**: Pro primitivn� typy (`int`, `string`, `double`), v��ty (`enum`), nebo `null`.  
- **P��stup**: Implicitn� `static` (pat�� t��d�, ne instanci).  

```csharp
public class Matematika 
{
    public const double PI = 3.1415926535;
    public const string Verze = "1.0";
}

// Pou�it�:
double polomer = 5;
double obsah = Matematika.PI * polomer * polomer;
```

#### **b) `readonly` (runtime konstanta)**  

- **Inicializace**: Hodnota m��e b�t nastavena **p�i deklaraci nebo v konstruktoru**.  
- **Pou�it�**: Pro komplexn� typy nebo hodnoty z�visl� na runtime logice.  
- **P��stup**: M��e b�t instan�n� nebo statick�.  

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

### **3. Porovn�n� `const` vs `readonly`**  

| **Krit�rium**       | `const`                     | `readonly`                  |  
|----------------------|-----------------------------|-----------------------------|  
| **Inicializace**     | P�i deklaraci               | P�i deklaraci nebo v konstruktoru |  
| **Typ hodnoty**      | Pouze primitivn�/`null`     | Libovoln� typ               |  
| **Kompila�n� �as**   | Hodnota zn�m� p�i kompilaci | Hodnota m��e b�t ur�ena za b�hu |  
| **Pam�**            | Statick� (pat�� t��d�)      | M��e b�t instan�n�/statick� |  
| **Verzov�n�**        | Zm�na vy�aduje rekompilaci  | Zm�na bez rekompilace       |  

---

### **4. `static readonly`**  

- Kombinuje v�hody `static` a `readonly`:  
  - Hodnota je nastavena jednou a sd�lena mezi v�emi instancemi.  
  - Lze inicializovat v statick�m konstruktoru.  

```csharp
public class Nastaveni 
{
    public static readonly DateTime StartDatum = new DateTime(2023, 1, 1);
}
```

---

### **5. V��ty (`enum`)**  

- Speci�ln� typ konstant pro **pojmenovan� celo��seln� hodnoty**.  
```csharp
public enum DenVTydnu 
{
    Pondeli = 1,
    Utery,
    Streda,
    // ...
}

// Pou�it�:
DenVTydnu dnes = DenVTydnu.Pondeli;
```

---

### **6. B�n� chyby a tipy**  

- **Pokus o zm�nu konstanty**:  
  ```csharp
  Matematika.PI = 3.14; // Chyba kompilace!
  ```  
- **Neinicializovan� `readonly` pole**:  
  ```csharp
  public readonly int[] Cisla; // Lze m�nit obsah pole (jen reference je konstantn�)!
  ```  
- **Verzov�n�**:  
  - `const` hodnoty jsou **vlo�eny p��mo do k�du** � zm�na vy�aduje rekompilaci v�ech z�visl�ch projekt�.  
  - `readonly` hodnoty jsou na�teny za b�hu � zm�na neovlivn� ji� zkompilovan� projekty.  

---

### **7. Doporu�en� postupy**  

1. **`const` pro jednoduch� hodnoty** (matematick� konstanty, verze).  
2. **`readonly` pro hodnoty z�visl� na prost�ed�** (cesty, konfigurace).  
3. **Pou��vejte `enum` pro omezen� mno�iny hodnot** (dny v t�dnu, stavy).  
4. **Vyhn�te se `public const` u komplexn�ch typ�**: M��e zp�sobit probl�m s verzov�n�m.  

---

### **8. Uk�zky k�du**  

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

#### **Statick� konstruktor pro `static readonly`**  

```csharp
public class AppConfig 
{
    public static readonly string ConnectionString;

    static AppConfig() 
    {
        ConnectionString = LoadFromConfigFile(); // Inicializace za b�hu
    }
}
```

---

### **9. Shrnut�**  

- **`const`**: Nem�nn� hodnota zn�m� p�i kompilaci.  
- **`readonly`**: Nem�nn� hodnota ur�en� za b�hu.  
- **`enum`**: Pojmenovan� celo��seln� konstanty.  
