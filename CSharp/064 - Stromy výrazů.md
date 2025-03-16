
### **Stromy výrazů (Expression Trees) v jazyce C#**

Stromy výrazů reprezentují kód jako **hierarchickou datovou strukturu**, kterou lze analyzovat, upravovat nebo převádět do jiných formátů (např. SQL dotazů). Jsou klíčové pro dynamické generování kódu, optimalizaci dotazů a práci s LINQ poskytovateli (Entity Framework, LINQ to SQL).

---

### **1. Základní koncepty**

- **Definice**: Stromy výrazů jsou **abstraktní syntaktické stromy**, kde každý uzel reprezentuje operaci (např. sčítání, volání metody, přístup k vlastnosti).
- **Imutabilita**: Jednotlivé výrazy jsou neměnné; úpravy vyžadují vytvoření nového stromu.
- **Namespace**: `System.Linq.Expressions`.
- **Rozdíl oproti delegátům**: 
  - **Delegát**: Spustitelný kód (např. `Func<int, int>`).
  - **Strom výrazů**: Data popisující kód (lze analyzovat a převést na delegáta pomocí `Compile()`).

---

### **2. Vytváření stromů výrazů**

#### **a) Ruční konstrukce pomocí factory metod**

```csharp
using System.Linq.Expressions;

// Vytvoření parametrů
ParameterExpression paramA = Expression.Parameter(typeof(int), "a");
ParameterExpression paramB = Expression.Parameter(typeof(int), "b");

// Vytvoření operace sčítání
BinaryExpression add = Expression.Add(paramA, paramB);

// Vytvoření lambda výrazu
Expression<Func<int, int, int>> lambda = 
    Expression.Lambda<Func<int, int, int>>(add, paramA, paramB);

// Kompilace do delegáta a spuštění
Func<int, int, int> compiled = lambda.Compile();
Console.WriteLine(compiled(2, 3)); // 5
```

#### **b) Automatické generování z lambda výrazu**

```csharp
Expression<Func<int, int, int>> expr = (a, b) => a * b + 10;
```

---

### **3. Analýza a úpravy stromů**

- **Třída `ExpressionVisitor`**: Umožňuje procházet a upravovat stromy výrazů.
```csharp
public class MyVisitor : ExpressionVisitor
{
    protected override Expression VisitBinary(BinaryExpression node)
    {
        if (node.NodeType == ExpressionType.Add)
            return Expression.Subtract(node.Left, node.Right); // Nahradí "+" za "-"
        return base.VisitBinary(node);
    }
}

// Použití
var modifiedExpr = new MyVisitor().Visit(expr);
```

---

### **4. Hlavní použití**

- **LINQ poskytovatelé**: Převod LINQ dotazů na SQL (Entity Framework), MongoDB dotazy atd.
- **Dynamické generování kódu**: Vytváření optimalizovaných delegátů pro reflexi.
- **Validační pravidla**: Dynamické sestavování podmínek pro validaci dat.
- **Dependency Injection**: Konfigurace pomocí výrazů (např. `services.AddScoped(c => new MyService())`).

---

### **5. Typy uzlů ve stromu výrazů**

| Typ uzlu               | Popis                          | Příklad                     |
|-------------------------|--------------------------------|----------------------------|
| `BinaryExpression`      | Binární operace (+, -, &&)     | `a + b`                    |
| `MethodCallExpression`  | Volání metody                  | `Math.Max(a, b)`           |
| `ConstantExpression`    | Konstanta                     | `5`, `"text"`              |
| `ParameterExpression`   | Parametr lambda výrazu        | `a` ve výrazu `a => a * 2` |
| `MemberExpression`      | Přístup k vlastnosti/poli      | `person.Age`               |
| `LambdaExpression`      | Lambda výraz                  | `(a, b) => a + b`          |

---

### **6. Příklady z praxe**

#### **a) Dynamický filtr pro Entity Framework**

```csharp
public static IQueryable<T> WhereContains<T>(IQueryable<T> source, string propertyName, object value)
{
    ParameterExpression param = Expression.Parameter(typeof(T), "x");
    MemberExpression property = Expression.Property(param, propertyName);
    ConstantExpression constant = Expression.Constant(value);
    BinaryExpression equals = Expression.Equal(property, constant);
    Expression<Func<T, bool>> lambda = Expression.Lambda<Func<T, bool>>(equals, param);
    
    return source.Where(lambda);
}

// Použití: users.WhereContains("Age", 25);
```

#### **b) Optimalizace reflexe pomocí kompilace výrazů**

```csharp
// Vytvoření getteru pro vlastnost (rychlejší než reflexe)
public static Func<object, object> CreateGetter(string propertyName)
{
    ParameterExpression objParam = Expression.Parameter(typeof(object), "obj");
    MemberExpression property = Expression.Property(
        Expression.Convert(objParam, typeof(MyClass)), 
        propertyName
    );
    Expression<Func<object, object>> lambda = 
        Expression.Lambda<Func<object, object>>(
            Expression.Convert(property, typeof(object)), 
            objParam
        );
    return lambda.Compile();
}

// Použití
var getter = CreateGetter("Age");
object age = getter(myClassInstance);
```

---

### **7. Omezení a tipy**

- **Podpora pouze pro výrazy**: Nelze vyjádřit víceřádkové lambdy (bloky kódu).
- **Kompilace je nákladná**: Cache-ujte kompilované delegáty pro opakované použití.
- **Debugování**: Prohlížeč debuggeru zobrazuje stromy výrazů jako text (např. `a => (a + b)`).

---

### **Shrnutí**

- **Stromy výrazů**: Reprezentace kódu jako dat pro analýzu a transformace.
- **Použití**: LINQ poskytovatelé, dynamické generování kódu, optimalizace.
- **Klíčové třídy**: `Expression`, `LambdaExpression`, `ExpressionVisitor`.
- **Tip**: Pro složité scénáře použijte knihovny jako `System.Reflection.Emit` nebo `Roslyn`.

📌 **Příklad – Převod výrazu na SQL**:
```csharp
Expression<Func<User, bool>> filter = u => u.Age > 18 && u.IsActive;
// Entity Framework převede tento výraz na: WHERE Age > 18 AND IsActive = 1
```
