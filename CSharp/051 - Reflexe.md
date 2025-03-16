
### Reflexe v jazyce C#  

Reflexe (Reflection) je mechanismus, který umožňuje **zkoumat a manipulovat s metadaty typů, metod, vlastností a dalších členů za běhu programu**. Používá se pro dynamické vytváření instancí, volání metod, analýzu atributů nebo práci s assembly. Zde je přehled klíčových konceptů:

---

#### **1. Základní pojmy**  

- **Metadata**: Informace o typech a jejich členech (třídy, metody, vlastnosti atd.).  
- **`System.Reflection`**: Namespace obsahující třídy pro reflexi.  
- **Typické použití**: Serializace, dynamické pluginy, ORM frameworky, testování.

---

#### **2. Získání informací o typu**  

- **`Type`**: Třída reprezentující typ.  
  ```csharp
  // Získání typu
  Type typ = typeof(string); // Staticky
  Type typ = instance.GetType(); // Dynamicky z instance
  ```  
- **Základní informace**:  
  ```csharp
  Console.WriteLine($"Jméno: {typ.Name}"); // "String"
  Console.WriteLine($"Je třída: {typ.IsClass}"); // True
  ```

---

#### **3. Práce s členy typu**  

- **Metody**:  
  ```csharp
  MethodInfo[] metody = typ.GetMethods();
  MethodInfo metoda = typ.GetMethod("Substring", new[] { typeof(int), typeof(int) });
  ```  
- **Vlastnosti**:  
  ```csharp
  PropertyInfo[] vlastnosti = typ.GetProperties();
  PropertyInfo lengthVlastnost = typ.GetProperty("Length");
  ```  
- **Pole a atributy**:  
  ```csharp
  FieldInfo[] pole = typ.GetFields(BindingFlags.NonPublic | BindingFlags.Instance);
  object[] atributy = typ.GetCustomAttributes(typeof(SerializableAttribute), false);
  ```

---

#### **4. Dynamické vytváření instancí**  

- Pomocí **`Activator.CreateInstance`**:  
  ```csharp
  object instance = Activator.CreateInstance(typeof(List<int>)); // new List<int>()
  ```  
- **S parametry**:  
  ```csharp
  object instance = Activator.CreateInstance(typeof(StringBuilder), "Text");
  ```

---

#### **5. Volání metod dynamicky** 

- **Pomocí `MethodInfo.Invoke`**:  
  ```csharp
  MethodInfo metoda = typ.GetMethod("ToUpper");
  string vysledek = (string)metoda.Invoke("abc", null); // "ABC"
  ```  
- **Metody s parametry**:  
  ```csharp
  MethodInfo metoda = typeof(Math).GetMethod("Max", new[] { typeof(int), typeof(int) });
  int max = (int)metoda.Invoke(null, new object[] { 5, 10 }); // 10
  ```

---

#### **6. Práce s assembly**  

- **Načtení assembly**:  
  ```csharp
  Assembly assembly = Assembly.LoadFrom("MojeKnihovna.dll");
  Type[] typy = assembly.GetTypes();
  ```  
- **Získání vstupní assembly**:  
  ```csharp
  Assembly assembly = Assembly.GetExecutingAssembly();
  ```

---

#### **7. Atributy a reflexe**  

- **Získání vlastních atributů**:  
  ```csharp
  [Author("Jan Novák")]
  class MojeTrida { }

  var atributy = typeof(MojeTrida).GetCustomAttributes(typeof(AuthorAttribute), false);
  AuthorAttribute autor = (AuthorAttribute)atributy[0];
  ```  
- **Vytvoření vlastního atributu**:  
  ```csharp
  public class AuthorAttribute : Attribute
  {
      public string Name { get; }
      public AuthorAttribute(string name) => Name = name;
  }
  ```

---

#### **8. Výhody a nevýhody**  

- **Výhody**:  
  - Flexibilita (dynamické pluginy, serializace).  
  - Možnost analýzy kódu za běhu.  
- **Nevýhody**:  
  - **Výkon**: Reflexe je pomalejší než statický kód.  
  - **Bezpečnostní rizika**: Přístup k privátním členům.  

---

#### **9. Doporučené postupy**  

- **Omezte reflexi**: Používejte ji jen tam, kde není možné statické řešení.  
- **Cache výsledky**: Ukládejte `MethodInfo` nebo `Type` do proměnných pro opakované použití.  
- **Používejte `dynamic`**: Pro jednoduché dynamické volání metod (kde je to možné).  

---

#### **10. Časté chyby**  

- **`NullReferenceException`**: Nezkontrolování `null` u `GetMethod` nebo `GetProperty`.  
- **Ignorování `BindingFlags`**: Např. nezahrnutí `BindingFlags.NonPublic` pro privátní členy.  
- **Memory leaks**: Při práci s dynamicky načtenými assembly (využívejte `AppDomain` opatrně).  

---

#### **11. Pokročilé techniky**  

- **Emitování IL kódu**: Tvorba dynamických metod pomocí `System.Reflection.Emit`.  
  ```csharp
  DynamicMethod metoda = new DynamicMethod("DynamickaMetoda", typeof(void), null);
  ILGenerator generator = metoda.GetILGenerator();
  generator.EmitWriteLine("Ahoj ze IL!");
  generator.Emit(OpCodes.Ret);
  metoda.Invoke(null, null);
  ```  
- **Reflexe s generiky**:  
  ```csharp
  Type otevrenyTyp = typeof(List<>);
  Type konkretniTyp = otevrenyTyp.MakeGenericType(typeof(int));
  ```
