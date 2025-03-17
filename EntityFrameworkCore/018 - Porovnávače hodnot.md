
### Porovnávače hodnot (Value Comparers) v Entity Framework Core

---

#### **1. Co jsou porovnávače hodnot?**  

- **Definice**: Mechanismus pro **porovnávání hodnot vlastností** entit při detekci změn (change tracking).  
- **Účel**: Řeší, jak EF Core určuje, zda se hodnota vlastnosti změnila, aby se předešlo zbytečným aktualizacím v databázi.  
- **Klíčové použití**:  
  - Pro **komplexní typy** (např. objekty, kolekce, pole).  
  - Pro vlastnosti, kde výchozí porovnání (např. `Equals`) není dostatečné.  

---

#### **2. Proč jsou důležité?**  

- **Výchozí chování**: EF Core porovnává hodnoty pomocí `Equals()`, což u složitých typů (např. kolekcí) může selhat (porovnává reference, ne obsah).  
- **Příklad problému**:  
  ```csharp  
  var entity = context.Products.First();  
  entity.Tags.Add("novy"); // Změna kolekce, ale EF Core nemusí detekovat změnu.  
  context.SaveChanges(); // Žádná aktualizace v DB!  
  ```  

---

#### **3. Konfigurace porovnávačů**  

- **Fluent API**: Porovnávače se nastavují pro konkrétní vlastnosti v metodě `OnModelCreating`.  
- **Vestavěné porovnávače**:  
  - `ValueComparer<T>` – základní třída pro vytváření vlastních porovnávačů.  
  - `ListComparer<T>`, `DictionaryComparer<T>`, atd. (pro kolekce).  

**Příklad pro pole bytů (byte[])**  
```csharp  
modelBuilder.Entity<Entity>()  
    .Property(e => e.ByteArray)  
    .Metadata.SetValueComparer(  
        new ValueComparer<byte[]>(  
            (a, b) => a.SequenceEqual(b), // Porovnání obsahu pole  
            c => c.Aggregate(0, (hash, b) => HashCode.Combine(hash, b)), // Výpočet hashe  
            c => c.ToArray() // Klonování pro snapshot  
        )  
    );  
```  

---

#### **4. Vlastní porovnávače**  

**Krok 1**: Vytvořte třídu dědící od `ValueComparer<T>`.  
**Krok 2**: Definujte logiku porovnání, výpočtu hashe a klonování.  

**Příklad pro vlastní objekt `Adresa`**  
```csharp  
public class AdresaComparer : ValueComparer<Adresa>  
{  
    public AdresaComparer()  
        : base(  
            (a1, a2) => a1.Ulice == a2.Ulice && a1.Mesto == a2.Mesto, // Porovnání  
            a => a.Ulice.GetHashCode() ^ a.Mesto.GetHashCode(), // Hash  
            a => new Adresa { Ulice = a.Ulice, Mesto = a.Mesto } // Klonování  
        )  
    { }  
}  

// Registrace  
modelBuilder.Entity<Firma>()  
    .Property(f => f.Sidlo)  
    .Metadata.SetValueComparer(new AdresaComparer());  
```  

---

#### **5. Kdy použít vlastní porovnávače?**  

| **Scénář**                     | **Řešení**                                  |  
|--------------------------------|---------------------------------------------|  
| **Kolekce** (List, Dictionary) | Porovnání obsahu, ne reference.             |  
| **Vlastní hodnotové objekty**  | Např. `Adresa`, `Penize`.                   |  
| **Pole bytů**                  | Porovnání po bajtech, ne podle reference.   |  

---

#### **6. Běžné problémy**  

| **Problém**                          | **Příčina**                     | **Řešení**                         |  
|--------------------------------------|----------------------------------|-------------------------------------|  
| Změny v kolekci nejsou detekovány    | Výchozí porovnávač kontroluje reference. | Použijte `ListComparer` nebo vlastní porovnávač. |  
| Zbytečné aktualizace v DB            | Porovnávač vrací falešné pozitivy. | Optimalizujte logiku porovnání. |  
| Chybějící klonování pro snapshot     | EF Core nemůže sledovat původní stav. | Implementujte metodu pro klonování. |  

---

#### **7. Doporučené postupy**  

1. **Pro kolekce**: Vždy používejte vestavěné nebo vlastní porovnávače.  
2. **Testování**: Ověřte detekci změn v testovacím prostředí.  
3. **Hashování**: Zajistěte, aby hash odrážel celý obsah (pro snížení kolizí).  

---

**Shrnutí**:  
- Porovnávače hodnot **definují, jak EF Core detekuje změny** ve vlastnostech entit.  
- Jsou nezbytné pro **komplexní typy a kolekce**, kde výchozí chování nestačí.  
- Konfigurují se přes **Fluent API** a umožňují plnou kontrolu nad logikou porovnání.  

**Zdroje**:  
- [EF Core Dokumentace – Porovnávače hodnot](https://learn.microsoft.com/en-us/ef/core/modeling/value-comparers)  
- [EF Core – Práce s kolekcemi](https://learn.microsoft.com/en-us/ef/core/modeling/owned-entities#collections-of-owned-types)
