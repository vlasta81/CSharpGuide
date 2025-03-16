
### **Zahození (`_`) v jazyce C#**

Klíčové slovo `_` (tzv. **discard**) slouží k **ignorování nepotřebných hodnot** v kódu. Používá se v situacích, kdy je nutné syntakticky přiřadit hodnotu, ale tato hodnota není dále využita. Zahození zlepšuje čitelnost kódu a potlačuje varování kompilátoru o nepoužitých proměnných.

---

### **1. Základní koncepty**

- **Ignorování hodnoty**: `_` signalizuje, že proměnná není potřeba a nebude použita.
- **Nealokuje paměť**: Discard není skutečná proměnná – nelze ji číst, pouze přiřadit.
- **Univerzální použití**: Lze použít pro libovolný typ (hodnotové i referenční typy).
- **Podpora od C# 7.0**: V novějších verzích rozšířeno o další scénáře (např. pattern matching).

---

### **2. Hlavní použití**

#### **a) Ignorování návratových hodnot metod (např. `out` parametr)**

```csharp
// Metoda s out parametrem, který nepotřebujeme
if (int.TryParse("123", out _))
{
    Console.WriteLine("Platné číslo");
}
```

#### **b) Ignorování prvků při dekonstrukci n-tic (tuples)**

```csharp
var person = ("Alice", 30, "CZ");
var (name, _, country) = person; // Ignorujeme věk
Console.WriteLine($"{name} z {country}");
```

#### **c) Ignorování parametrů v lambda výrazech**

```csharp
// Lambda s parametrem, který se nepoužívá
button.Click += (_, _) => Console.WriteLine("Tlačítko stisknuto");
```

#### **d) Ignorování hodnot v pattern matchingu**

```csharp
object obj = 42;
if (obj is int _) // Kontrola typu bez přiřazení
{
    Console.WriteLine("Je to číslo");
}

// Ignorování části vzoru
var result = obj switch
{
    string s => "Řetězec",
    int _ => "Číslo", // Ignorujeme konkrétní hodnotu
    _ => "Neznámý typ"
};
```

#### **e) Ignorování návratových hodnot asynchronních metod**

```csharp
_ = SomeAsyncMethod(); // Návratová hodnota (Task) není potřeba
```

---

### **3. Syntaxe a omezení**

- **Nelze číst hodnotu**:
  ```csharp
  _ = 10;
  Console.WriteLine(_); // Chyba: název "_" neexistuje
  ```
- **Lze použít vícekrát ve stejném rozsahu** (od C# 9.0):
  ```csharp
  var (x, _, _) = (1, 2, 3); // Ignorujeme druhý a třetí prvek
  ```
- **Není to proměnná**: Nelze použít s `ref`/`out`.

---

### **4. Výhody oproti pojmenovaným proměnným**

- **Čitelnost**: Zřetelně ukazuje, že hodnota je záměrně ignorována.
- **Potlačení varování**: Kompilátor nevyvolá varování CS0219 (nepoužitá proměnná).
- **Optimalizace**: Zabraňuje alokaci zbytečných proměnných.

---

### **5. Časté chyby a tipy**

- **Záměna s proměnnou `_`**: V některých kontextech může `_` být platná proměnná (např. v starších verzích C#). Vždy zkontrolujte kontext.
- **Ignorování důležitých hodnot**: Ujistěte se, že zahozená hodnota není potřebná pro další logiku.
- **Použití v LINQ**: Ignorování prvků v dotazech:
  ```csharp
  var numbers = new List<int> { 1, 2, 3 };
  numbers.ForEach(_ => Console.WriteLine("Prvek")); // Ignorujeme hodnotu
  ```

---

### **6. Shrnutí**

- **Zahození (`_`)**: Nástroj pro ignorování nepotřebných hodnot.
- **Použití**: `out` parametry, dekonstrukce n-tic, pattern matching, lambda výrazy.
- **Výhody**: Čistší kód, eliminace varování, optimalizace.
- **Omezení**: Nelze číst, nelze použít jako referenci.

📌 **Příklad z praxe – Ignorování prvku při iteraci**:
```csharp
foreach (var (key, _) in dictionary)
{
    Console.WriteLine($"Klíč: {key}"); // Ignorujeme hodnotu
}
```
