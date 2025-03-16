
### **LINQ (Language Integrated Query)**  

LINQ je technologie integrovan� do jazyka C#, kter� umo��uje **dotazov�n� nad daty** p��mo v syntaxi jazyka. Kombinuje s�lu SQL-like dotaz� s v�hodami siln�ho typov�ho syst�mu C#.

---

#### **Z�kladn� charakteristika**  

- **Integrace do jazyka**: LINQ dotazy jsou sou��st� k�du v C#, nikoli �et�zc� (nap�. jako v SQL).  
- **Univerz�ln� pou�it�**: Lze dotazovat r�zn� zdroje dat (kolekce, XML, SQL datab�ze, Entity Framework, JSON atd.).  
- **Dva p��stupy**:  
  1. **Query Syntax** (deklarativn� styl podobn� SQL).  
  2. **Method Syntax** (metody roz���en� a lambda v�razy).  

---

#### **Kl��ov� komponenty**  

1. **Extension Methods** (`System.Linq`):  
   - Metody jako `Where()`, `Select()`, `OrderBy()` atd.  
   - Pracuj� s rozhran�m `IEnumerable<T>` nebo `IQueryable<T>`.  
2. **Lambda Expressions**:  
   - Zkr�cen� z�pis pro anonymn� funkce (nap�. `x => x.Age > 18`).  
3. **Anonymous Types**:  
   - Vytv��en� anonymn�ch objekt� pro projekce (`new { x.Name, x.Age }`).  
4. **Deleg�ti**:  
   - Nap�. `Func<T, bool>` pro definici podm�nek.  

---

#### **Syntaxe LINQ**  

**1. Query Syntax** (SQL-like):  
```csharp
var result = from person in people
             where person.Age > 18
             orderby person.Name
             select new { person.Name, person.City };
```

**2. Method Syntax** (fluent style):  
```csharp
var result = people
    .Where(person => person.Age > 18)
    .OrderBy(person => person.Name)
    .Select(person => new { person.Name, person.City });
```

Oba p��stupy jsou ekvivalentn� � kompil�tor p�ekl�d� Query Syntax na Method Syntax.

---

#### **Hlavn� oper�tory LINQ**  

| Kategorie         | P��klady                                      | Popis                              |
|--------------------|----------------------------------------------|------------------------------------|
| **Filtrov�n�**     | `Where()`, `OfType<T>()`                     | V�b�r prvk� podle podm�nky.        |
| **�azen�**         | `OrderBy()`, `ThenBy()`, `Reverse()`         | Se�azen� v�sledk�.                 |
| **Seskupen�**      | `GroupBy()`, `ToLookup()`                    | Seskupen� podle kl��e.             |
| **Projekce**       | `Select()`, `SelectMany()`                   | Transformace v�sledk�.             |
| **Spojen�**        | `Join()`, `GroupJoin()`                      | Spojen� dvou kolekc�.              |
| **Agregace**       | `Sum()`, `Average()`, `Count()`, `Max()`     | V�po�ty nad daty.                  |
| **Str�nkov�n�**    | `Skip()`, `Take()`                           | V�b�r podmno�iny prvk�.            |
| **Elementy**       | `First()`, `Single()`, `ElementAt()`         | V�b�r konkr�tn�ho prvku.           |
| **Konverze**       | `ToList()`, `ToArray()`, `ToDictionary()`    | P�evod na konkr�tn� kolekci.       |

---

#### **LINQ Providers**  

- **LINQ to Objects**: Pro pr�ci s kolekcemi v pam�ti (pole, seznamy).  
- **LINQ to SQL/Entity Framework**: Pro dotazov�n� rela�n�ch datab�z�.  
- **LINQ to XML**: Pro pr�ci s XML dokumenty (`XDocument`, `XElement`).  
- **LINQ to JSON** (pomoc� knihoven jako Newtonsoft.Json).  

---

#### **Deferred Execution (Odlo�en� vykon�n�)**  

- Dotaz se **nevykon� okam�it�** p�i deklaraci, ale a� p�i iteraci v�sledku (nap�. ve `foreach` nebo p�i vol�n� `ToList()`).  
- Umo��uje optimalizaci a kombinov�n� dotaz�.  
- **P��klad**:  
  ```csharp
  var query = people.Where(p => p.Age > 18); // Dotaz se je�t� nevykonal
  var results = query.ToList(); // Vyvol�n� dotazu
  ```

---

#### **Rozd�l mezi `IEnumerable` a `IQueryable`**  

- **`IEnumerable`**:  
  - Pro pr�ci s lok�ln�mi daty (v pam�ti).  
  - Vykon�v� se na stran� klienta.  
- **`IQueryable`**:  
  - Pro dotazy nad extern�mi zdroji (nap�. SQL).  
  - Generuje optimalizovan� SQL dotaz (nap�. pro Entity Framework).  

---

#### **P��klad: LINQ s Entity Framework**  

```csharp
using (var context = new AppDbContext())
{
    var adults = context.People
                        .Where(p => p.Age >= 18)
                        .OrderBy(p => p.Name)
                        .ToList();
}
```

---

#### **V�hody LINQ**  

- Jednotn� zp�sob dotazov�n� r�zn�ch zdroj�.  
- �itelnost a udr�ovatelnost k�du.  
- Kompil�tor kontroluje chyby (na rozd�l od SQL v �et�zc�ch).  

#### **Nev�hody**  

- N�kdy m�n� efektivn� ne� nativn� SQL (nutnost optimalizace).  
- Slo�it�j�� debugov�n�.  

---

## **Metody**

### **Z�kladn� operace**
- **Select** � `Projekce prvk�`  
- **Where** � `Filtrace podle podm�nky`  
- **OfType** � `Filtrace podle typu`  

### **�azen� a seskupov�n�**
- **OrderBy** � `�azen� vzestupne`  
- **OrderByDescending** � `�azen� sestupn�`  
- **ThenBy** � `Sekund�rn� �azen� vzestupn�`  
- **ThenByDescending** � `Sekund�rn� �azen� sestupn�`  
- **GroupBy** � `Seskupen� prvk�`  
- **ToLookup** � `Vytvo�en� lookup tabulky`  

### **Spojov�n� kolekc�**
- **Join** � `Rela�n� spojen�`  
- **GroupJoin** � `Seskupen� spojen�`  
- **Concat** � `Spojen� kolekc�`  

### **Agrega�n� funkce**
- **Sum** � `Sou�et hodnot`  
- **Average** � `Pr�m�rn� hodnota`  
- **Count** � `Po�et prvk�`  
- **Max** � `Maxim�ln� hodnota`  
- **Aggregate** � `Agregace hodnot`  

### **Pr�ce s prvky**
- **First** � `Prvn� prvek`  
- **FirstOrDefault** � `Prvn� prvek s v�choz� hodnotou`  
- **Last** � `Posledn� prvek`  
- **LastOrDefault** � `Posledn� prvek s v�choz� hodnotou`  
- **Single** � `Jedin� prvek`  
- **SingleOrDefault** � `Jedin� prvek s v�choz� hodnotou`  
- **ElementAt** � `Prvek na pozici`  
- **ElementAtOrDefault** � `Prvek na pozici s v�choz� hodnotou`  

### **Podm�n�n� operace**
- **Any** � `Alespo� jeden spl�uje podm�nku`  
- **All** � `V�echny prvky spl�uj� podm�nku`  
- **Contains** � `Obsahuje prvek`  

### **Porovn�n� a manipulace**
- **SequenceEqual** � `Porovn�n� sekvenc�`  
- **Distinct** � `Unik�tn� prvky`  
- **Except** � `Rozd�l kolekc�`  
- **Intersect** � `Pr�nik kolekc�`  
- **Union** � `Sjednocen� kolekc�`  

### **Pr�ce s sekvencemi**
- **Skip** � `P�esko�en� prvk�`  
- **SkipWhile** � `P�esko�en� dokud podm�nka plat�`  
- **Take** � `V�b�r prvk�`  
- **TakeWhile** � `V�b�r dokud podm�nka plat�`  

### **Speci�ln� kolekce**
- **Empty** � `Pr�zdn� kolekce`  
- **Range** � `Sekvence ��sel`  
- **Repeat** � `Opakuj�c� se prvek`  
- **DefaultIfEmpty** � `V�choz� hodnota pro pr�zdnou kolekci`  

---

### **Doporu�en� zdroje**  

- Ofici�ln� dokumentace: [Microsoft LINQ Guide](https://learn.microsoft.com/cs-cz/dotnet/csharp/programming-guide/concepts/linq/)  
