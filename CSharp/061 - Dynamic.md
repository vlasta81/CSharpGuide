
### **Dynamický typ (`dynamic`) v jazyce C#**

Dynamický typ `dynamic` umožňuje **vyhnout se statické typové kontrole při kompilaci** a odkládá rozpoznání typu až na runtime. Používá se pro práci s objekty, jejichž struktura není známa předem (např. data z externích zdrojů, COM objekty, dynamické jazyky). 

---

#### **1. Základní vlastnosti**

- **Typ je určen za běhu**: Kompilátor nekontroluje existenci metod/vlastností – chyby se projeví až za běhu jako `RuntimeBinderException`.
- **Flexibilita**: Lze přiřadit jakýkoli typ (číslo, řetězec, objekt, `ExpandoObject`).
- **Interakce s DLR (Dynamic Language Runtime)**: Zajišťuje dynamické vyhodnocování operací.
- **Není to `object`**: Na rozdíl od `object` nevyžaduje explicitní přetypování.

---

#### **2. Kdy použít `dynamic`?**

- **Interoperabilita s COM/API**: Např. práce s Excelem přes COM rozhraní.
- **Dynamická data**: JSON, XML s neznámou strukturou.
- **Reflexe bez složitosti**: Volání metod nebo přístup k vlastnostem, jejichž název je znám až za běhu.
- **Práce s dynamickými jazyky** (IronPython, IronRuby).

---

#### **3. Syntaxe a příklady**

```csharp
dynamic data = 10; // Přiřazení čísla
Console.WriteLine(data); // 10

data = "Text"; // Změna typu za běhu
Console.WriteLine(data.ToUpper()); // "TEXT" (volání metody)

data = new List<int> { 1, 2, 3 };
Console.WriteLine(data.Count); // 3 (přístup k vlastnosti)

// COM příklad (Excel)
dynamic excel = Microsoft.VisualBasic.Interaction.CreateObject("Excel.Application");
excel.Visible = true;
```

---

#### **4. Dynamické objekty (`ExpandoObject`)**

Třída `ExpandoObject` umožňuje **dynamicky přidávat vlastnosti a metody**:
```csharp
dynamic person = new ExpandoObject();
person.Name = "Alice";
person.Age = 30;
person.SayHello = (Action)(() => Console.WriteLine($"Ahoj, já jsem {person.Name}!"));

person.SayHello(); // "Ahoj, já jsem Alice!"
```

---

#### **5. Omezení a rizika**

- **Žádná IntelliSense podpora**: Není dostupné automatické doplňování kódu.
- **Runtime chyby**: Pokud vlastnost/metoda neexistuje, vyvolá se `RuntimeBinderException`.
- **Výkonnostní režie**: Dynamické vyhodnocování je pomalejší než statický kód.
- **Ztráta typové bezpečnosti**: Vyžaduje pečlivé ošetření chyb.

---

#### **6. Porovnání s `var` a `object`**

| Vlastnost          | `var`                          | `object`                | `dynamic`               |
|---------------------|--------------------------------|-------------------------|-------------------------|
| **Typová kontrola** | Statická (kompilace)           | Statická (s přetypováním) | Dynamická (runtime)     |
| **Flexibilita**     | Nelze změnit typ               | Vyžaduje přetypování    | Mění typ za běhu        |
| **Použití**         | Zkrácení deklarace             | Generické kontejnery    | Dynamické scénáře       |

---

#### **7. Best Practices**

1. **Používejte pouze tam, kde je to nutné** (např. COM, dynamická data).
2. **Ošetřujte výjimky** pomocí `try-catch` při práci s dynamickými operacemi.
3. **Preferujte statické typování**, pokud je struktura dat známá.
4. **Validujte data**: Kontrolujte existenci vlastností/metod před přístupem.
   ```csharp
   if ((data as IDictionary<string, object>)?.ContainsKey("Name") == true)
       Console.WriteLine(data.Name);
   ```

---

#### **8. Příklady z praxe**

- **Práce s JSON (Newtonsoft.Json)**:
  ```csharp
  dynamic json = JsonConvert.DeserializeObject("{\"Name\": \"Bob\", \"Age\": 25}");
  Console.WriteLine(json.Name); // "Bob"
  ```
- **Reflexe bez `Reflection`**:
  ```csharp
  object obj = new MyClass();
  dynamic dyn = obj;
  dyn.MethodName(); // Volání metody, pokud existuje
  ```

---

### **Shrnutí**

- **Dynamický typ `dynamic`**: Umožňuje práci s objekty neznámé struktury za běhu.
- **Výhody**: Flexibilita, interoperabilita s COM/dynamickými jazyky.
- **Nevýhody**: Ztráta typové bezpečnosti, riziko runtime chyb.
- **Používejte s rozvahou**: Ideální pro specifické scénáře, ne pro obecný kód.

📌 **Příklad – Dynamický přístup k vlastnostem**:
```csharp
try
{
    dynamic data = GetExternalData(); // Např. z API
    Console.WriteLine(data.Value);
}
catch (RuntimeBinderException)
{
    Console.WriteLine("Vlastnost 'Value' neexistuje!");
}
```
