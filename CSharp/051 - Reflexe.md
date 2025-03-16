
### Reflexe v jazyce C#  

Reflexe (Reflection) je mechanismus, který umožòuje **zkoumat a manipulovat s metadaty typù, metod, vlastností a dalších èlenù za bìhu programu**. Používá se pro dynamické vytváøení instancí, volání metod, analýzu atributù nebo práci s assembly. Zde je pøehled klíèových konceptù:

---

#### **1. Základní pojmy**  

- **Metadata**: Informace o typech a jejich èlenech (tøídy, metody, vlastnosti atd.).  
- **`System.Reflection`**: Namespace obsahující tøídy pro reflexi.  
- **Typické použití**: Serializace, dynamické pluginy, ORM frameworky, testování.

---

#### **2. Získání informací o typu**  

- **`Type`**: Tøída reprezentující typ.  
  ```csharp
  // Získání typu
  Type typ = typeof(string); // Staticky
  Type typ = instance.GetType(); // Dynamicky z instance
  ```  
- **Základní informace**:  
  ```csharp
  Console.WriteLine($"Jméno: {typ.Name}"); // "String"
  Console.WriteLine($"Je tøída: {typ.IsClass}"); // True
  ```

---

#### **3. Práce s èleny typu**  

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

#### **4. Dynamické vytváøení instancí**  

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

- **Naètení assembly**:  
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

- **Získání vlastních atributù**:  
  ```csharp
  [Author("Jan Novák")]
  class MojeTrida { }

  var atributy = typeof(MojeTrida).GetCustomAttributes(typeof(AuthorAttribute), false);
  AuthorAttribute autor = (AuthorAttribute)atributy[0];
  ```  
- **Vytvoøení vlastního atributu**:  
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
  - Možnost analýzy kódu za bìhu.  
- **Nevýhody**:  
  - **Výkon**: Reflexe je pomalejší než statický kód.  
  - **Bezpeènostní rizika**: Pøístup k privátním èlenùm.  

---

#### **9. Doporuèené postupy**  

- **Omezte reflexi**: Používejte ji jen tam, kde není možné statické øešení.  
- **Cache výsledky**: Ukládejte `MethodInfo` nebo `Type` do promìnných pro opakované použití.  
- **Používejte `dynamic`**: Pro jednoduché dynamické volání metod (kde je to možné).  

---

#### **10. Èasté chyby**  

- **`NullReferenceException`**: Nezkontrolování `null` u `GetMethod` nebo `GetProperty`.  
- **Ignorování `BindingFlags`**: Napø. nezahrnutí `BindingFlags.NonPublic` pro privátní èleny.  
- **Memory leaks**: Pøi práci s dynamicky naètenými assembly (využívejte `AppDomain` opatrnì).  

---

#### **11. Pokroèilé techniky**  

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
