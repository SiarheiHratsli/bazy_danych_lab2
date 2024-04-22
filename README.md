# bazy_danych_lab2
# Siarhei Hratsli 48738

1. **Wyświetlenie IMIE i NAZWISKO pracowników**
   - Wyświetl IMIE i NAZWISKO wszystkich pracowników z tabeli PRACOWNIK połączone w jedną kolumnę w formacie inicjał imienia i nazwisko (I. NAZWISKO) o nagłówku OSOBA, posortowane alfabetycznie rosnąco po nazwisku.

```sql
SELECT CONCAT(SUBSTRING(Imie, 1, 1), '. ', Nazwisko) AS OSOBA
FROM pracownik
ORDER BY Nazwisko;
```
  
| "OSOBA"          |
|------------------|
| "M. Dąbrowska"   |
| "J. Kowalski"    |
| "A. Lewandowski" |
| "A. Nowak"       |
| "P. Wiśniewski"  |

2. **Wyświetlenie pracowników zamieszkujących w miejscowościach zaczynających się na literę B**
   - Wyświetl IMIE, NAZWISKO i MIEJSCOWOŚĆ zamieszkani pracowników z tabeli PRACOWNIK mieszkających w miejscowościach zaczynających się na literę B.

```sql
SELECT p.Imie, p.Nazwisko, a.Miejscowosc
FROM pracownik p
JOIN adres a ON p.AdresId = a.Id
WHERE a.Miejscowosc LIKE 'B%';
```

| "Imie"    | "Nazwisko"    | "Miejscowosc" |
|-----------|---------------|---------------|
| "Maria"   | "Dąbrowska"   | "Berlin"      |
| "Andrzej" | "Lewandowski" | "Berlin"      |

3. **Wyświetlenie kierowników pracowników z działu RD1**
   - Wyświetl IMIE, NAZWISKO oraz NAZWISKO kierownika pracowników z tabeli PRACOWNIK pracujących w dziale RD1.

```sql
SELECT p.Imie, p.Nazwisko, k.Nazwisko AS NazwiskoKierownika
FROM pracownik p
JOIN pracownik k ON p.KierownikId = k.Id
WHERE p.DzialKod = 'RD1';
```

| "Imie"  | "Nazwisko"   | "NazwiskoKierownika" |
|---------|--------------|----------------------|
| "Anna"  | "Nowak"      | "Kowalski"           |
| "Piotr" | "Wiśniewski" | "Kowalski"           |

4. **Wyświetlenie informacji o samochodach przypisanych do pracowników**
   - Wyświetl IMIE, NAZWISKO oraz NR REJESTR samochodu pracowników z tabeli PRACOWNIK, którzy posiadają przypisany samochód.

```sql
SELECT p.Imie, p.Nazwisko, s.NrRejestr
FROM pracownik p
JOIN samochod s ON p.SamochodId = s.Id
WHERE p.SamochodId IS NOT NULL;
```
| "Imie"  | "Nazwisko"   | "NrRejestr" |
|---------|--------------|-------------|
| "Anna"  | "Nowak"      | "WAW12345"  |
| "Piotr" | "Wiśniewski" | "KRK54321"  |
| "Maria" | "Dąbrowska"  | "BER98765"  |

5. **Liczba pracowników przypisanych do projektów**
   - Wyświetl NAZWA projektu oraz liczbę pracowników do niego przypisanych.

```sql
SELECT pr.Nazwa AS NAZWA_PROJEKTU, COUNT(pp.PracownikId) AS LICZBA_PRACOWNIKOW
FROM projekt pr
LEFT JOIN projekt_pracownik pp ON pr.Kod = pp.ProjektKod
GROUP BY pr.Kod;
```
| "NAZWA_PROJEKTU" | "LICZBA_PRACOWNIKOW" |
|------------------|----------------------|
| "Projekt A"      | "3"                  |
| "Projekt B"      | "1"                  |
| "Projekt C"      | "1"                  |

6. **Liczba pracowników zamieszkujących w danym kraju**
   - Wyświetl KOD kraju oraz liczbę pracowników w nim zamieszkujących.
  
```sql
SELECT a.KrajKod AS KOD_KRAJU, COUNT(p.Id) AS LICZBA_PRACOWNIKOW
FROM pracownik p
JOIN adres a ON p.AdresId = a.Id
GROUP BY a.KrajKod;
```
| "KOD_KRAJU" | "LICZBA_PRACOWNIKOW" |
|-------------|----------------------|
| "DE"        | "2"                  |
| "PL"        | "3"                  |

7. **Pracownicy bez przypisanego samochodu**
   - Wyświetl IMIE, NAZWISKO i NAZWA oddziału pracowników, którzy nie posiadają przypisanego samochodu.

```sql
SELECT p.Imie, p.Nazwisko, o.Nazwa AS NAZWA_ODDZIALU
FROM pracownik p
JOIN oddzial o ON p.OddzialId = o.Id
WHERE p.SamochodId IS NULL;
```

| "Imie"    | "Nazwisko"    | "NAZWA_ODDZIALU"    |
|-----------|---------------|---------------------|
| "Jan"     | "Kowalski"    | "Oddział centralny" |
| "Andrzej" | "Lewandowski" | "Oddział centralny" |

8. **Kierownicy pracowników przypisanych do projektów**
   - Wyświetl (bez powtórzeń) KOD projektu oraz NAZWISKO kierownika pracowników przypisanego do tego projektu.
  
```sql
SELECT DISTINCT pr.Kod AS KOD_PROJEKTU, k.Nazwisko AS NAZWISKO_KIEROWNIKA
FROM projekt_pracownik pp
JOIN projekt pr ON pp.ProjektKod = pr.Kod
JOIN pracownik p ON pp.PracownikId = p.Id
JOIN pracownik k ON p.KierownikId = k.Id;
```

| "KOD_PROJEKTU" | "NAZWISKO_KIEROWNIKA" |
|----------------|-----------------------|
| "PR1"          | "Nowak"               |
| "PR2"          | "Kowalski"            |
| "PR3"          | "Kowalski"            |

9. **Miejscowość oddziału dla pracowników z danego działu**
   - Wyświetl (bez powtórzeń) KOD działu oraz MIEJSCOWOŚĆ oddziału, w którym pracują pracownicy z tego działu.

```sql
SELECT DISTINCT d.Kod AS KOD_DZIALU, a.Miejscowosc AS MIEJSCOWOSC_ODDZIALU
FROM pracownik p
JOIN dzial d ON p.DzialKod = d.Kod
JOIN oddzial o ON p.OddzialId = o.Id
JOIN adres a ON o.AdresId = a.Id;
```

| "KOD_DZIALU" | "MIEJSCOWOSC_ODDZIALU" |
|--------------|------------------------|
| "IT1"        | "Warszawa"             |
| "RD1"        | "Kraków"               |
| "ADM"        | "Warszawa"             |

10. **Informacje o samochodach i oddziałach**
    - Wyświetl NAZWA oddziału oraz MARKA, MODEL i NR REJ samochodu.
   
```sql
SELECT o.Nazwa AS NAZWA_ODDZIALU, s.Marka, s.Model, s.NrRejestr
FROM pracownik p
JOIN oddzial o ON p.OddzialId = o.Id
JOIN samochod s ON p.SamochodId = s.Id;
```
| "NAZWA_ODDZIALU"    | "Marka"      | "Model"   | "NrRejestr" |
|---------------------|--------------|-----------|-------------|
| "Oddział Kraków"    | "Toyota"     | "Corolla" | "WAW12345"  |
| "Oddział Kraków"    | "Volkswagen" | "Golf"    | "KRK54321"  |
| "Oddział centralny" | "Ford"       | "Focus"   | "BER98765"  |
