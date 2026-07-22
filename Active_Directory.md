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