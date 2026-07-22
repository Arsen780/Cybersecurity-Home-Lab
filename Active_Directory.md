# Active Directory

## Cel

Active Directory Domain Services (AD DS) zostało wdrożone jako podstawowa usługa odpowiedzialna za centralne zarządzanie środowiskiem laboratoryjnym. Umożliwia uwierzytelnianie użytkowników, zarządzanie komputerami, grupami oraz jednostkami organizacyjnymi (OU), a także stanowi fundament dla kolejnych elementów infrastruktury, takich jak Group Policy, PowerShell czy usługi plików.

---

## Informacje o domenie

| Parametr | Wartość |
|----------|----------|
| Nazwa domeny | lab.local |
| Kontroler domeny | DC01 |
| System operacyjny | Windows Server 2022 |
| Adres IP | 192.168.100.10 |

---

## Wdrożenie Active Directory

Na serwerze **DC01** zainstalowano rolę **Active Directory Domain Services (AD DS)**, a następnie utworzono nowy las oraz domenę o nazwie **lab.local**.

Wraz z instalacją usługi AD DS automatycznie skonfigurowano również usługę DNS, odpowiedzialną za rozwiązywanie nazw w środowisku domenowym.

---

## Struktura organizacyjna

W celu uporządkowania środowiska utworzono podstawową strukturę jednostek organizacyjnych (OU).

```text
lab.local
│
├── Departments
│   ├── Finance
│   ├── HR
│   ├── IT
│   ├── Management
│   └── Marketing
│
├── Groups
│
└── Workstations
```

Taki podział ułatwia zarządzanie użytkownikami oraz umożliwia przypisywanie zasad Group Policy do wybranych działów lub komputerów.

---

## Użytkownicy

Na potrzeby testów utworzono konta użytkowników przypisane do odpowiednich działów.

Każdy użytkownik posiada własne konto domenowe oraz jest członkiem odpowiedniej grupy zabezpieczeń.

Przykładowe konta:

- użytkownik działu IT,
- użytkownik działu HR,
- użytkownik działu Finance,
- użytkownik działu Marketing,
- użytkownik działu Management.

---

## Grupy zabezpieczeń

W celu stosowania zasady **Least Privilege** utworzono grupy zabezpieczeń odpowiadające poszczególnym działom.

Przykładowe grupy:

- GG_IT
- GG_HR
- GG_FINANCE
- GG_MANAGEMENT
- GG_MARKETING

Uprawnienia do zasobów sieciowych są przypisywane grupom, a nie bezpośrednio użytkownikom.

---

## Komputery domenowe

Do domeny **lab.local** dołączono stację roboczą:

| Komputer | Status |
|-----------|--------|
| WIN11-01 | ✔ Dołączony do domeny |

Komputer został umieszczony w jednostce organizacyjnej **Workstations**, co umożliwi zarządzanie nim za pomocą zasad Group Policy.

---

---

# Group Policy (GPO)

W środowisku wdrożono zasady Group Policy (GPO), które umożliwiają centralne zarządzanie konfiguracją użytkowników oraz komputerów znajdujących się w domenie.

W celu zachowania przejrzystości konfiguracji zastosowano kilka oddzielnych obiektów GPO odpowiedzialnych za różne obszary administracji.

## Skonfigurowane polityki

| Nazwa GPO | Przeznaczenie |
|------------|---------------|
| Default Domain Policy | Konfiguracja polityki haseł oraz blokowania kont |
| STD - User Restriction | Ograniczenia dla użytkowników spoza działu IT |
| Departments Drive Mapping | Automatyczne mapowanie dysków sieciowych |
| SEC - Advanced Audit Policy | Centralna konfiguracja audytu zdarzeń bezpieczeństwa |

---

## Polityka ograniczeń użytkowników

Dla użytkowników działów Finance, HR, Marketing oraz Management wdrożono politykę **STD - User Restriction**.

Polityka nie jest stosowana do działu IT, którego użytkownicy zachowują pełne uprawnienia administracyjne wymagane do wykonywania obowiązków.

Skonfigurowane ograniczenia:

- blokada Panelu sterowania,
- blokada aplikacji Ustawienia systemu,
- blokada programu Windows PowerShell,
- blokada Wiersza polecenia (CMD),
- blokada Edytora rejestru,
- blokada Microsoft Store,
- wyłączenie menu „Uruchom”,
- ograniczenie skrótów systemowych z klawiszem Windows.

Celem wdrożenia jest ograniczenie możliwości modyfikowania konfiguracji systemu przez standardowych użytkowników oraz zmniejszenie ryzyka nieautoryzowanych zmian.

---

---

# Mapowanie dysków sieciowych

W celu zapewnienia użytkownikom automatycznego dostępu do zasobów sieciowych wdrożono politykę **Departments Drive Mapping**.

Mapowanie dysków realizowane jest za pomocą mechanizmu **Group Policy Preferences (Drive Maps)**.

Po zalogowaniu użytkownika odpowiednie dyski sieciowe są automatycznie mapowane z serwera plików.

## Skonfigurowane dyski

| Litera | Udział sieciowy |
|---------|-----------------|
| F: | \\DC01\Finance |
| H: | \\DC01\HR |
| I: | \\DC01\IT |
| M: | \\DC01\Marketing |
| P: | \\DC01\Public |
| X: | \\DC01\Management |

Takie rozwiązanie umożliwia centralne zarządzanie zasobami sieciowymi oraz eliminuje konieczność ręcznego mapowania dysków na stacjach roboczych.

---

---

# Polityka haseł

W ramach **Default Domain Policy** skonfigurowano centralną politykę haseł obowiązującą wszystkich użytkowników domeny.

## Konfiguracja

| Parametr | Wartość |
|-----------|----------|
| Minimalna długość hasła | 10 znaków |
| Historia haseł | 20 poprzednich haseł |
| Maksymalny okres ważności | 90 dni |
| Minimalny okres ważności | 1 dzień |
| Wymuszona złożoność | Tak |
| Reversible Encryption | Wyłączona |

Wdrożona polityka zwiększa poziom bezpieczeństwa poprzez wymuszenie stosowania silniejszych haseł oraz ograniczenie możliwości ponownego wykorzystania poprzednich haseł.

---

---

# Account Lockout Policy

W celu ochrony przed próbami odgadnięcia haseł skonfigurowano mechanizm blokowania kont użytkowników.

## Konfiguracja

| Parametr | Wartość |
|-----------|----------|
| Liczba nieudanych logowań | 5 |
| Czas blokady konta | 30 minut |
| Reset licznika prób | 30 minut |

Mechanizm ten znacząco utrudnia przeprowadzanie ataków typu brute-force na konta domenowe.

---

---

# Advanced Audit Policy

W środowisku wdrożono oddzielną politykę **SEC - Advanced Audit Policy**, odpowiedzialną za rejestrowanie zdarzeń związanych z bezpieczeństwem domeny.

Skonfigurowano między innymi audyt:

- logowania użytkowników,
- nieudanych prób logowania,
- blokowania kont,
- zarządzania użytkownikami,
- zarządzania grupami,
- zmian w usługach katalogowych Active Directory,
- zmian polityk bezpieczeństwa,
- dostępu do udziałów sieciowych.

Rejestrowane zdarzenia są zapisywane w dzienniku **Windows Security Log**, co umożliwia analizę aktywności użytkowników oraz wykrywanie potencjalnych incydentów bezpieczeństwa.
---

## Wykonane działania

Na obecnym etapie skonfigurowano:

- instalację usługi Active Directory Domain Services,
- utworzenie nowego lasu i domeny,
- konfigurację usługi DNS,
- utworzenie struktury OU,
- utworzenie użytkowników domenowych,
- utworzenie grup zabezpieczeń,
- przypisanie użytkowników do odpowiednich grup,
- dołączenie stacji roboczej Windows 11 do domeny.

---

## Testy

Po zakończeniu konfiguracji wykonano podstawowe testy działania środowiska.

### Uwierzytelnianie użytkowników

✔ Możliwość logowania użytkowników do domeny.

### Active Directory Users and Computers

✔ Poprawne wyświetlanie użytkowników, grup oraz jednostek organizacyjnych.

### DNS

✔ Poprawne rozwiązywanie nazwy `lab.local`.

### Dołączenie komputera do domeny

✔ Komputer WIN11-01 został pomyślnie dodany do domeny.

---

## Napotkane problemy

### Problem 1

Komputer Windows 11 nie mógł odnaleźć domeny podczas procesu dołączania.

**Przyczyna**

Niepoprawna konfiguracja serwera DNS.

**Rozwiązanie**

Skonfigurowano klienta tak, aby korzystał z kontrolera domeny jako głównego serwera DNS.

---

### Problem 2

Problemy z przypisywaniem użytkowników do grup.

**Przyczyna**

Błędna konfiguracja grup podczas początkowej budowy środowiska.

**Rozwiązanie**

Zweryfikowano członkostwo grup oraz ponownie przypisano użytkowników do odpowiednich grup zabezpieczeń.

---

## Podsumowanie

Wdrożenie Active Directory umożliwiło centralne zarządzanie środowiskiem laboratoryjnym. Utworzona struktura domenowa stanowi podstawę do dalszego rozwoju projektu. W kolejnych etapach zostaną wdrożone między innymi zasady Group Policy (GPO), automatyzacja z wykorzystaniem PowerShell, zaawansowane zarządzanie uprawnieniami, monitorowanie zdarzeń oraz scenariusze związane z bezpieczeństwem i testami penetracyjnymi.