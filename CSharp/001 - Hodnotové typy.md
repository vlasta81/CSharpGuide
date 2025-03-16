
### **1. Definice hodnotov�ch typ�**

- Ukl�daj� **data p��mo v pam�ti** (na z�sobn�ku *stack* nebo uvnit� objekt� na hald� *heap*).
- Kop�rov�n�m vznik� **nez�visl� kopie dat**.
- Maj� **pevnou velikost** a jsou **rychlej��** pro p��stup ne� referen�n� typy.

---

### **2. Kategorie hodnotov�ch typ�**

#### **a) Vestav�n� primitivn� typy**

- **Cel� ��sla**:
  - `byte` (0�255), `sbyte` (-128�127)
  - `short` (-32 768�32 767), `ushort` (0�65 535)
  - `int` (-2 miliardy�2 miliardy), `uint`
  - `long` (velk� rozsah), `ulong`
- **Desetinn� ��sla**:
  - `float` (~6�9 platn�ch m�st), `double` (~15�17 platn�ch m�st)
  - `decimal` (28�29 platn�ch m�st, p�esn� pro finance).
- **Logick� hodnoty**:
  - `bool` (`true`/`false`).
- **Znaky**:
  - `char` (Unicode znak, nap�. `'A'`).

#### **b) Struktury (`struct`)**

- U�ivatelsky definovan� slo�en� typy.
- **P��klad**: `DateTime`, `Point`, vlastn� struktury.
- **Vlastnosti**:
  - Mohou obsahovat pole, metody, vlastnosti.
  - Nemohou d�dit (krom� implementace rozhran�).
  - Implicitn� konstruktor bez parametr� nelze definovat.
  ```csharp
  public struct Bod {
      public int X;
      public int Y;
      public Bod(int x, int y) => (X, Y) = (x, y);
  }
  ```

#### **c) V��tov� typy (`enum`)**

- Definuj� mno�inu pojmenovan�ch konstant.
- **V�choz� typ**: `int` (lze zm�nit na `byte`, `short` apod.).
- **P��klad**:
  ```csharp
  public enum DenVTydnu {
      Pondeli, Utery, Streda, Ctvrtek, Patek, Sobota, Nedele
  }
  ```

---

### **3. Kl��ov� vlastnosti**

- **Implicitn� hodnota**: Inicializov�ny na v�choz� hodnotu (nap�. `0` pro ��sla, `false` pro `bool`).
- **�ivotnost**: Zni�eny p�i opu�t�n� kontextu (nap�. po dokon�en� metody).
- **Nullabilita**: Nelze p�i�adit `null` (pouze s `Nullable<T>` nebo `?`).

---

### **4. Nullable hodnotov� typy**

- Umo��uj� p�i�adit `null` pomoc� `Nullable<T>` nebo zkratky `?`.
- **P��klad**:
  ```csharp
  int? cislo = null;
  if (cislo.HasValue) {
      Console.WriteLine(cislo.Value);
  }
  ```

---

### **5. Rozd�ly oproti referen�n�m typ�m**

| **Vlastnost**       | **Hodnotov� typy**       | **Referen�n� typy**       |
|----------------------|--------------------------|---------------------------|
| Ukl�d�n�             | P��mo na stack/heap      | Reference na heap         |
| Kop�rov�n�           | Hlubok� kopie            | Kopie reference           |
| V�choz� hodnota      | `0`, `false` apod.       | `null`                    |
| V�kon                | Rychlej�� p��stup        | Pomalej�� (GC overhead)   |

---

### **6. Boxing a Unboxing**

- **Boxing**: Konverze hodnotov�ho typu na `object` (ukl�d� se na heap).
- **Unboxing**: Zp�tn� konverze na hodnotov� typ.
- **P��klad**:
  ```csharp
  int cislo = 42;
  object boxed = cislo; // Boxing
  int unboxed = (int)boxed; // Unboxing
  ```

---

### **7. Doporu�en� pou�it�**

- **Struktury**: Pro mal�, nem�nn� datov� struktury (nap�. sou�adnice).
- **Enumy**: Pro omezen� mno�iny hodnot (dny v t�dnu, stavy).
- **Nullable**: Pokud hodnota m��e chyb�t (nap�. v datab�zov�ch pol�ch).

---

### **8. Pozor na**

- **Zbyte�n� kop�rov�n�**: Velk� struktury mohou zpomalit k�d.
- **Boxing overhead**: �ast� boxov�n� sni�uje v�kon.
- **Imutabilita**: U struktur doporu�eno pro p�edch�zen� chyb�m.

---

### **9. Uk�zka k�du**

```csharp
// Struktura
public struct Velikost {
    public int Sirka { get; }
    public int Vyska { get; }
    public Velikost(int sirka, int vyska) => (Sirka, Vyska) = (sirka, vyska);
}

// Enum
public enum Stav { Aktivni, Neaktivni, Pozastaveno }

// Pou�it�
Velikost v = new Velikost(1920, 1080);
Stav currentStav = Stav.Aktivni;
int? skore = null;
```
