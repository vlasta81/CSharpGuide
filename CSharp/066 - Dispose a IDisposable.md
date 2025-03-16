
### **Dispose a IDisposable v jazyce C#**

Rozhraní `IDisposable` a metoda `Dispose()` slouží k **správnému uvolňování nespravovaných prostředků** (např. soubory, síťová spojení, databázové connectiony). Bez jejich použití hrozí **úniky prostředků** (memory leaks) nebo nedostupnost souborů.

---

#### **1. Základní koncepty**

- **`IDisposable`**: Rozhraní s jednou metodou – `Dispose()`. Implementují ho třídy pracující s nespravovanými prostředky.
- **`Dispose()`**: Uvolňuje prostředky explicitně (není třeba čekat na garbage collector).
- **`using` blok**: Zajišťuje automatické volání `Dispose()` i při výjimkách.
- **Finalizér (destruktor)**: Záloha pro uvolnění prostředků, pokud `Dispose()` nebyl zavolán. Není deterministický (volá GC).

---

#### **2. Implementace IDisposable**

Standardní vzor pro implementaci `IDisposable` (tzv. **Dispose pattern**):

```csharp
public class Resource : IDisposable
{
    private bool _disposed = false;

    // Veřejná metoda Dispose (implementace IDisposable)
    public void Dispose()
    {
        Dispose(true);
        GC.SuppressFinalize(this); // Zabrání volání finalizéru
    }

    // Chráněná virtuální metoda pro možnost přepsání v potomcích
    protected virtual void Dispose(bool disposing)
    {
        if (!_disposed)
        {
            if (disposing)
            {
                // Uvolnění spravovaných prostředků (např. další IDisposable objekty)
            }

            // Uvolnění nespravovaných prostředků (např. file handles)
            _disposed = true;
        }
    }

    // Finalizér (destruktor) – volán GC, pokud Dispose() nebyl zavolán
    ~Resource()
    {
        Dispose(false);
    }
}
```

---

#### **3. Použití `using` bloku**

Automaticky volá `Dispose()` na konci bloku:

```csharp
using (var file = new FileStream("data.txt", FileMode.Open))
{
    // Práce se souborem
} // Dispose() se zavolá automaticky, i když dojde k výjimce
```

Pro C# 8+ lze použít zjednodušenou syntaxi bez složených závorek:
```csharp
using var file = new FileStream("data.txt", FileMode.Open);
// Práce se souborem
// Dispose() se zavolá na konci metody
```

---

#### **4. Kdy implementovat IDisposable?**

- Třída drží **nespravované prostředky** (např. `FileStream`, `SqlConnection`).
- Třída obsahuje **spravované prostředky**, které implementují `IDisposable` (např. další `IDisposable` objekty).
- Je potřeba explicitně uvolnit prostředky před garbage collection.

---

#### **5. Příklady z praxe**

##### **Uvolnění souborového proudu**

```csharp
public class FileReader : IDisposable
{
    private FileStream _stream;

    public FileReader(string path)
    {
        _stream = new FileStream(path, FileMode.Open);
    }

    public void Dispose()
    {
        _stream?.Dispose();
    }
}

// Použití
using (var reader = new FileReader("data.txt"))
{
    // Čtení dat
}
```

##### **Kaskádové uvolňování prostředků**

Pokud třída obsahuje jiné `IDisposable` objekty, uvolněte je v `Dispose()`:

```csharp
public class DatabaseWrapper : IDisposable
{
    private SqlConnection _connection;
    private SqlCommand _command;

    public DatabaseWrapper()
    {
        _connection = new SqlConnection("...");
        _command = _connection.CreateCommand();
    }

    public void Dispose()
    {
        _command?.Dispose();
        _connection?.Dispose();
    }
}
```

---

#### **6. Časté chyby**

- **Zapomenutí volání `Dispose()`**: Vždy používejte `using` nebo ručně zavolejte `Dispose()`.
- **Nesprávná implementace vzoru Dispose**: Zapomenutí na `GC.SuppressFinalize(this)` nebo špatná logika v `Dispose(bool)`.
- **Volání metod na disposed objektu**: Po zavolání `Dispose()` by měly metody vyvolávat `ObjectDisposedException`.

---

#### **7. Best Practices**

1. **Vždy používejte `using`** pro objekty implementující `IDisposable`.
2. **Nepoužívejte finalizéry**, pokud není nutné pracovat s nespravovanými prostředky.
3. **Kaskádové uvolňování**: Pokud třída obsahuje `IDisposable` objekty, implementujte `IDisposable` i v ní.
4. **Ošetřujte `ObjectDisposedException`**: Pokud může být objekt již uvolněn.

---

### **Shrnutí**

- **`IDisposable`**: Mechanismus pro deterministické uvolňování prostředků.
- **`Dispose()`**: Explicitní uvolnění. Vždy ji zavolejte pomocí `using`.
- **Finalizér**: Záloha pro uvolnění, ale spoléhejte hlavně na `Dispose()`.
- **Vzor Dispose**: Implementujte pomocí `Dispose(bool)` a `GC.SuppressFinalize`.

📌 **Příklad – Bezpečné použití `IDisposable`**:
```csharp
using (var connection = new SqlConnection("connectionString"))
{
    connection.Open();
    using (var command = new SqlCommand("SELECT * FROM Users", connection))
    {
        var reader = command.ExecuteReader();
        // ...
    }
}
```
