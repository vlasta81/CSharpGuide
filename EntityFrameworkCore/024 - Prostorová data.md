
### Prostorová data v Entity Framework Core

#### 1. **Úvod do prostorových dat**

Prostorová data reprezentují geografické nebo geometrické entity (body, čáry, polygony) v aplikacích. EF Core podporuje práci s prostorovými daty pomocí knihovny **NetTopologySuite**, která poskytuje typy jako `Point`, `LineString`, nebo `Polygon`. Tyto typy se mapují na databázové prostorové typy (např. `geometry` v SQL Serveru, `geography` v PostGIS).

---

#### 2. **Požadované knihovny a nastavení**

- **NetTopologySuite**: Hlavní knihovna pro práci s prostorovými daty v .NET.
- **Databázový provider**: Musí podporovat prostorové typy (např. `Microsoft.EntityFrameworkCore.SqlServer`, `Npgsql.EntityFrameworkCore.PostgreSQL`).
- **Konfigurace v DbContextu**:
  ```csharp
  services.AddDbContext<AppDbContext>(options =>
      options.UseSqlServer(connectionString, x => x.UseNetTopologySuite())
  );
  ```

---

#### 3. **Základní typy prostorových dat**

- **Point**: Bod se souřadnicemi (X, Y) nebo (Zeměpisná šířka, délka).
- **LineString**: Série spojených bodů (např. trasa).
- **Polygon**: Uzavřená oblast tvořená body (např. hranice města).
- **GeometryCollection**: Kolekce různých geometrií.

---

#### 4. **Mapování prostorových dat**

- **Data Annotations**:
  ```csharp
  public class Mesto
  {
      public int Id { get; set; }
      public Point Poloha { get; set; } // Vlastnost typu Point
  }
  ```

- **Fluent API**:
  ```csharp
  modelBuilder.Entity<Mesto>()
      .Property(m => m.Poloha)
      .HasColumnType("geometry"); // Explicitní určení datového typu v DB
  ```

---

#### 5. **Podpora databází**

- **SQL Server**: 
  - Typ `geometry`/`geography`.
  - Vyžaduje `Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite`.
- **PostgreSQL/PostGIS**:
  - Typ `geography`.
  - Vyžaduje `Npgsql.EntityFrameworkCore.PostgreSQL.NetTopologySuite`.
- **SQLite**:
  - Nutné přidat rozšíření pro prostorová data (např. `SpatiaLite`).

---

#### 6. **Dotazování na prostorová data**

EF Core umožňuje používat prostorové funkce přímo v LINQ dotazech:
- **Vzdálenost mezi body**:
  ```csharp
  var mesta = context.Mesta
      .Where(m => m.Poloha.Distance(vychoziBod) < 1000) // Vzdálenost v metrech
      .ToList();
  ```

- **Kontrola překryvu**:
  ```csharp
  var oblasti = context.Oblasti
      .Where(o => o.Hranice.Intersects(hledanaHranice))
      .ToList();
  ```

---

#### 7. **Ukládání a načítání dat**

- **Příklad vytvoření bodu**:
  ```csharp
  var praha = new Mesto
  {
      Nazev = "Praha",
      Poloha = new Point(14.41854, 50.073658) { SRID = 4326 } // WGS-84 souřadnice
  };
  ```

- **SRID (Spatial Reference Identifier)**: Určuje systém souřadnic (např. `4326` pro zeměpisné souřadnice).

---

#### 8. **Omezení a doporučení**

- **Výkon**: Prostorové dotazy mohou být náročné – používejte indexy.
- **Kompabilita**: Ne všechny databáze podporují všechny funkce (např. SQLite má omezenou podporu).
- **Validace SRID**: Zajistěte, aby SRID odpovídal napříč dotazy a daty.

---

#### 9. **Časté problémy**

- **Chybějící NetTopologySuite**: Nutné nainstalovat NuGet balíček.
- **Neplatné souřadnice**: Např. zeměpisná šířka mimo rozsah (-90 až 90).
- **Neaktivované rozšíření v databázi**: Např. PostGIS v PostgreSQL musí být povolen.

---

#### 10. **Zdroje**

- **Dokumentace EF Core**: [Spatial Data in EF Core](https://learn.microsoft.com/en-us/ef/core/modeling/spatial)
- **NetTopologySuite**: [Oficiální stránka](https://nettopologysuite.github.io/NetTopologySuite/)
- **PostGIS dokumentace**: [PostGIS Manual](https://postgis.net/docs/)
