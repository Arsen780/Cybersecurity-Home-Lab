# Windows Server 2022

## Cel

Windows Server 2022 stanowi centralny element środowiska laboratoryjnego. Pełni rolę głównego serwera odpowiedzialnego za zarządzanie domeną, użytkownikami oraz usługami sieciowymi. W kolejnych etapach projektu będzie również wykorzystywany do wdrażania nowych usług oraz mechanizmów bezpieczeństwa.

---

## Informacje o maszynie

| Parametr | Wartość |
|----------|----------|
| Nazwa maszyny | DC01 |
| System operacyjny | Windows Server 2022 |
| Rola | Domain Controller |
| Adres IPv4 | 192.168.100.10 |
| Domena | lab.local |

---

## Konfiguracja maszyny wirtualnej

Serwer został uruchomiony jako maszyna wirtualna w środowisku VMware Workstation Pro.

### Konfiguracja sieci

- NAT – dostęp do Internetu
- Host-Only (LAB) – komunikacja z pozostałymi maszynami laboratoryjnymi

Takie rozwiązanie umożliwia jednoczesne korzystanie z Internetu oraz odizolowanie środowiska testowego od sieci domowej.

---

## Konfiguracja systemu

Po instalacji systemu wykonano podstawową konfigurację serwera.

Wykonane czynności:

- zmiana nazwy komputera na **DC01**,
- konfiguracja statycznego adresu IPv4,
- konfiguracja serwera DNS,
- weryfikacja połączenia sieciowego,
- instalacja najważniejszych aktualizacji systemowych.

---

## Zainstalowane role

Na obecnym etapie projektu na serwerze zainstalowano następujące role:

| Rola | Status |
|------|--------|
| Active Directory Domain Services | ✔ |
| DNS Server | ✔ |

W kolejnych etapach planowane jest rozszerzenie funkcjonalności serwera o dodatkowe role i usługi.

---

## Aktualna funkcjonalność serwera

Obecnie serwer odpowiada za:

- kontrolę domeny `lab.local`,
- uwierzytelnianie użytkowników,
- zarządzanie strukturą Active Directory,
- obsługę usługi DNS,
- zarządzanie użytkownikami i grupami,
- udostępnianie zasobów sieciowych.

---

## Testy

Po zakończeniu konfiguracji wykonano podstawowe testy poprawności działania.

### Test komunikacji sieciowej

✔ Komunikacja pomiędzy wszystkimi maszynami laboratoryjnymi.

### Test logowania do domeny

✔ Komputer Windows 11 został pomyślnie dołączony do domeny.

### Test rozwiązywania nazw DNS

✔ Poprawne rozwiązywanie nazwy domeny `lab.local`.

### Test Active Directory

✔ Możliwość tworzenia użytkowników oraz grup.

---

## Podsumowanie

Na obecnym etapie Windows Server 2022 pełni rolę kontrolera domeny oraz centralnego punktu zarządzania środowiskiem laboratoryjnym. Skonfigurowano podstawowe usługi niezbędne do funkcjonowania infrastruktury, co umożliwi dalszą rozbudowę laboratorium o kolejne elementy, takie jak Group Policy, PowerShell, Firewall, IDS/IPS, monitoring czy rozwiązania SIEM.