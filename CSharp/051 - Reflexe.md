
### Reflexe v jazyce C#  

Reflexe (Reflection) je mechanismus, kter� umo��uje **zkoumat a manipulovat s metadaty typ�, metod, vlastnost� a dal��ch �len� za b�hu programu**. Pou��v� se pro dynamick� vytv��en� instanc�, vol�n� metod, anal�zu atribut� nebo pr�ci s assembly. Zde je p�ehled kl��ov�ch koncept�:

---

#### **1. Z�kladn� pojmy**  

- **Metadata**: Informace o typech a jejich �lenech (t��dy, metody, vlastnosti atd.).  
- **`System.Reflection`**: Namespace obsahuj�c� t��dy pro reflexi.  
- **Typick� pou�it�**: Serializace, dynamick� pluginy, ORM frameworky, testov�n�.

---

#### **2. Z�sk�n� informac� o typu**  

- **`Type`**: T��da reprezentuj�c� typ.  
  ```csharp
  // Z�sk�n� typu
  Type typ = typeof(string); // Staticky
  Type typ = instance.GetType(); // Dynamicky z instance
  ```  
- **Z�kladn� informace**:  
  ```csharp
  Console.WriteLine($"Jm�no: {typ.Name}"); // "String"
  Console.WriteLine($"Je t��da: {typ.IsClass}"); // True
  ```

---

#### **3. Pr�ce s �leny typu**  

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

#### **4. Dynamick� vytv��en� instanc�**  

- Pomoc� **`Activator.CreateInstance`**:  
  ```csharp
  object instance = Activator.CreateInstance(typeof(List<int>)); // new List<int>()
  ```  
- **S parametry**:  
  ```csharp
  object instance = Activator.CreateInstance(typeof(StringBuilder), "Text");
  ```

---

#### **5. Vol�n� metod dynamicky** 

- **Pomoc� `MethodInfo.Invoke`**:  
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

#### **6. Pr�ce s assembly**  

- **Na�ten� assembly**:  
  ```csharp
  Assembly assembly = Assembly.LoadFrom("MojeKnihovna.dll");
  Type[] typy = assembly.GetTypes();
  ```  
- **Z�sk�n� vstupn� assembly**:  
  ```csharp
  Assembly assembly = Assembly.GetExecutingAssembly();
  ```

---

#### **7. Atributy a reflexe**  

- **Z�sk�n� vlastn�ch atribut�**:  
  ```csharp
  [Author("Jan Nov�k")]
  class MojeTrida { }

  var atributy = typeof(MojeTrida).GetCustomAttributes(typeof(AuthorAttribute), false);
  AuthorAttribute autor = (AuthorAttribute)atributy[0];
  ```  
- **Vytvo�en� vlastn�ho atributu**:  
  ```csharp
  public class AuthorAttribute : Attribute
  {
      public string Name { get; }
      public AuthorAttribute(string name) => Name = name;
  }
  ```

---

#### **8. V�hody a nev�hody**  

- **V�hody**:  
  - Flexibilita (dynamick� pluginy, serializace).  
  - Mo�nost anal�zy k�du za b�hu.  
- **Nev�hody**:  
  - **V�kon**: Reflexe je pomalej�� ne� statick� k�d.  
  - **Bezpe�nostn� rizika**: P��stup k priv�tn�m �len�m.  

---

#### **9. Doporu�en� postupy**  

- **Omezte reflexi**: Pou��vejte ji jen tam, kde nen� mo�n� statick� �e�en�.  
- **Cache v�sledky**: Ukl�dejte `MethodInfo` nebo `Type` do prom�nn�ch pro opakovan� pou�it�.  
- **Pou��vejte `dynamic`**: Pro jednoduch� dynamick� vol�n� metod (kde je to mo�n�).  

---

#### **10. �ast� chyby**  

- **`NullReferenceException`**: Nezkontrolov�n� `null` u `GetMethod` nebo `GetProperty`.  
- **Ignorov�n� `BindingFlags`**: Nap�. nezahrnut� `BindingFlags.NonPublic` pro priv�tn� �leny.  
- **Memory leaks**: P�i pr�ci s dynamicky na�ten�mi assembly (vyu��vejte `AppDomain` opatrn�).  

---

#### **11. Pokro�il� techniky**  

- **Emitov�n� IL k�du**: Tvorba dynamick�ch metod pomoc� `System.Reflection.Emit`.  
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
