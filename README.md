# Cybersecurity Home Lab

## Opis projektu

Cybersecurity Home Lab to projekt przedstawiający budowę oraz rozwój własnego środowiska laboratoryjnego do nauki administracji systemami, sieci komputerowych oraz cyberbezpieczeństwa.

Laboratorium zostało zbudowane w środowisku VMware Workstation Pro i odzwierciedla podstawową infrastrukturę niewielkiej organizacji. Celem projektu jest zdobywanie praktycznych umiejętności z zakresu administracji Windows Server, Active Directory, systemów Linux, konfiguracji usług sieciowych oraz testowania zabezpieczeń.

Projekt będzie sukcesywnie rozwijany o kolejne usługi, mechanizmy bezpieczeństwa oraz scenariusze testowe.

---

## Środowisko laboratoryjne

| Nazwa maszyny | System operacyjny | Adres IP | Rola |
|---------------|-------------------|-----------|------|
| DC01 | Windows Server 2022 | 192.168.100.10 | Kontroler domeny oraz główny serwer środowiska |
| WIN11-01 | Windows 11 Pro | 192.168.100.20 | Stacja robocza dołączona do domeny |
| Ubuntu | Ubuntu Desktop | 192.168.100.30 | Maszyna z systemem Linux przeznaczona do nauki administracji i usług sieciowych |
| Kali | Kali Linux | 192.168.100.40 | Maszyna wykorzystywana do testów bezpieczeństwa oraz symulacji ataków |

---

## Aktualny zakres projektu

Na obecnym etapie wykonano:

- konfigurację środowiska VMware Workstation Pro,
- utworzenie sieci laboratoryjnej,
- instalację czterech maszyn wirtualnych,
- konfigurację Windows Server 2022,
- instalację i konfigurację usług Active Directory Domain Services,
- konfigurację serwera DNS,
- utworzenie domeny `lab.local`,
- dołączenie komputera Windows 11 do domeny,
- utworzenie struktury jednostek organizacyjnych (OU),
- utworzenie użytkowników i grup zabezpieczeń,
- konfigurację udziałów sieciowych SMB,
- konfigurację podstawowych uprawnień NTFS,
- rozpoczęcie konfiguracji zasad Group Policy.

---

## Dokumentacja

- [Konfiguracja Windows Server](Windows_Server.md)
- [Konfiguracja Active Directory](Active_Directory.md)

---

## Plan dalszego rozwoju

Projekt będzie rozwijany o kolejne elementy infrastruktury, między innymi:

- zaawansowane zasady Group Policy,
- automatyzację z wykorzystaniem PowerShell,
- administrację systemem Ubuntu,
- testy bezpieczeństwa z wykorzystaniem Kali Linux,
- konfigurację zapory sieciowej (Firewall),
- wdrożenie systemów IDS/IPS,
- centralne monitorowanie logów,
- rozwiązania SIEM,
- scenariusze ataków oraz ich wykrywania,
- dokumentację kolejnych etapów rozwoju laboratorium.