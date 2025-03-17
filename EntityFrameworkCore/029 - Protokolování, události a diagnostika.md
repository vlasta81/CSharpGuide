
### Protokolování, události a diagnostika v Entity Framework Core

---

#### **1. Protokolování (Logging)**  

**Co to je**:  
Mechanismus pro zaznamenávání informací o činnosti EF Core (např. generované SQL dotazy, chyby, transakce).  

**Jak nastavit**:  
- **Integrace s .NET logging frameworkem**:  
  ```csharp  
  var optionsBuilder = new DbContextOptionsBuilder<AppDbContext>()
      .UseSqlServer(connectionString)
      .LogTo(Console.WriteLine, LogLevel.Information); // Logování do konzole
  ```  
- **Vlastní logger (ILoggerFactory)**:  
  ```csharp  
  ILoggerFactory loggerFactory = LoggerFactory.Create(builder => 
      builder.AddConsole().AddFilter(level => level >= LogLevel.Information));
  
  services.AddDbContext<AppDbContext>(options => 
      options.UseSqlServer(connectionString)
             .UseLoggerFactory(loggerFactory));
  ```  

**Úrovně logování**:  
- `Trace`: Podrobné informace (výkonnostní metrika, ladění).  
- `Debug`: Technické detaily (např. parametry dotazů).  
- `Information`: Základní operace (otevření spojení, uložení změn).  
- `Warning`: Neočekávané, ale nefatální události.  
- `Error`: Chyby (např. neplatný SQL dotaz).  

**Příklad výstupu**:  
```text  
info: Microsoft.EntityFrameworkCore.Database.Command[20101]  
      Executed DbCommand (25ms) [Parameters=[@p0='10'], CommandType='Text', CommandTimeout='30']  
      SELECT * FROM Studenti WHERE Vek > @p0  
```  

---

#### **2. Události (Events)**  

**Co to je**:  
Události umožňují reagovat na specifické akce v EF Core (např. před uložením změn, po provedení dotazu).  

**Hlavní události**:  
- **SavingChanges**: Vyvolána před uložením změn.  
- **SavedChanges**: Vyvolána po úspěšném uložení.  
- **SaveChangesFailed**: Vyvolána při chybě během ukládání.  
- **CommandExecuting/CommandExecuted**: Vyvolána před a po provedení SQL příkazu.  

**Příklad použití**:  
```csharp  
context.SavingChanges += (sender, args) => 
{
    Console.WriteLine("Ukládám změny...");  
};  
```  

**Interceptory**:  
- Třídy implementující `IDbCommandInterceptor` nebo `ISaveChangesInterceptor`.  
- **Příklad (logování SQL příkazů)**:  
  ```csharp  
  public class SqlCommandInterceptor : DbCommandInterceptor  
  {  
      public override InterceptionResult<DbDataReader> ReaderExecuting(  
          DbCommand command, CommandEventData eventData, InterceptionResult<DbDataReader> result)  
      {  
          Console.WriteLine($"SQL: {command.CommandText}");  
          return result;  
      }  
  }  
  
  // Registrace  
  services.AddDbContext<AppDbContext>(options =>  
      options.UseSqlServer(connectionString)
             .AddInterceptors(new SqlCommandInterceptor()));
  ```  

---

#### **3. Diagnostika (Diagnostics)**  

**Co to zahrnuje**:  
Nástroje pro sledování výkonu, ladění problémů a analýzu chování aplikace.  

**Diagnostické nástroje**:  
- **Activity API**: Pro sledování distribuovaných transakcí (např. v mikroservisní architektuře).  
- **DiagnosticListener**: Posluchač pro interní události EF Core.  
  ```csharp  
  DiagnosticListener.AllListeners.Subscribe(new EfCoreDiagnosticObserver());  
  ```  
- **Metriky (EF Core 7+)**:  
  ```csharp  
  var metrics = context.Database.GetMetrics();  
  Console.WriteLine($"Počet dotazů: {metrics.Queries}");  
  ```  

**Výkonnostní tipy**:  
- Používejte `EnableSensitiveDataLogging()` pouze pro vývoj (loguje hodnoty parametrů).  
- Omezte logování na potřebné úrovně (`LogLevel`).  
- Sledujte metriky jako dobu trvání dotazů nebo počet otevřených spojení.  

---

#### **4. Běžné problémy a řešení**  

| **Problém**                          | **Řešení**                                  |  
|--------------------------------------|---------------------------------------------|  
| Příliš mnoho logů (nízký výkon)      | Nastavte vyšší `LogLevel` (např. `Warning`). |  
| Citlivá data v logu                  | Nepoužívejte `EnableSensitiveDataLogging()` v produkci. |  
| Nezachycené výjimky                  | Přidejte interceptory pro `SaveChangesFailed`. |  

---

#### **5. Doporučené postupy**  

1. **Logujte strukturovaně**: Usnadňuje analýzu nástroji jako Elasticsearch nebo Application Insights.  
2. **Používejte interceptory pro audit**: Zaznamenávejte změny dat nebo přístup k citlivým informacím.  
3. **Sledujte metriky**: Identifikujte pomalé dotazy nebo zahlcení spojeními.  

---

**Shrnutí**:  
- **Protokolování** umožňuje sledovat operace EF Core a ladit problémy.  
- **Události a interceptory** poskytují kontrolu nad životním cyklem operací.  
- **Diagnostika** pomáhá optimalizovat výkon a řešit chyby.  

**Zdroje**:  
- [EF Core Dokumentace – Logování](https://learn.microsoft.com/en-us/ef/core/logging-events-diagnostics/)  
- [EF Core – Interceptory](https://learn.microsoft.com/en-us/ef/core/logging-events-diagnostics/interceptors)  
- [Diagnostika s Activity API](https://learn.microsoft.com/en-us/dotnet/core/diagnostics/distributed-tracing)
