# Wazuh

## Cel

Wazuh został wdrożony jako centralny system monitorowania bezpieczeństwa środowiska laboratoryjnego.

Rozwiązanie umożliwia centralne zbieranie i analizowanie zdarzeń z hostów, monitorowanie konfiguracji systemów, kontrolę integralności plików oraz prezentowanie wyników w interfejsie webowym.

W projekcie Wazuh pełni rolę centralnego komponentu monitorowania i analizy zdarzeń bezpieczeństwa.

---

## Architektura

Wazuh został wdrożony w modelu **all-in-one** na maszynie Ubuntu.

```text
Ubuntu 26.04 LTS
192.168.100.30
        │
        ├── Wazuh Manager
        ├── Wazuh Indexer
        └── Wazuh Dashboard
                ▲
                │
           Wazuh Agent
                │
                │
        Windows Server 2022
        DC01 - 192.168.100.10
```

---

## Informacje o serwerze

| Parametr | Wartość |
|----------|---------|
| System | Ubuntu 26.04 LTS |
| Architektura | AMD64 |
| Adres LAB | `192.168.100.30` |
| CPU | 2 vCPU |
| RAM | 4 GB przydzielone VM |
| Dysk | 40 GB |
| Wersja Wazuh | 4.14.7 |

Środowisko zostało skonfigurowane jako małe laboratorium testowe. Zasoby VM zostały pozostawione na poziomie 2 vCPU, 4 GB RAM oraz 40 GB dysku.

---

## Instalacja

Na Ubuntu wdrożono komponenty:

- **Wazuh Manager** – odpowiedzialny za odbieranie i analizowanie danych z agentów,
- **Wazuh Indexer** – odpowiedzialny za przechowywanie i indeksowanie danych,
- **Wazuh Dashboard** – interfejs webowy do analizy zdarzeń i alertów.

Instalację wykonano przy użyciu instalatora Wazuh w trybie all-in-one.

Ze względu na ograniczoną ilość pamięci systemowej instalator został uruchomiony z opcją:

```bash
sudo ./wazuh-install.sh -a -i
```

Po zakończeniu instalacji wszystkie główne komponenty działały poprawnie.

---

## Weryfikacja usług

Sprawdzono działanie:

- `wazuh-manager`,
- `wazuh-indexer`,
- Wazuh Dashboard.

Dashboard jest dostępny pod adresem:

```text
https://192.168.100.30
```

---

## Porty

W środowisku zweryfikowano między innymi następujące porty:

| Port | Protokół | Funkcja |
|------|----------|---------|
| 1514 | TCP | komunikacja agenta z Managerem |
| 1515 | TCP | rejestracja/enrollment agentów |
| 443 | TCP | Wazuh Dashboard |
| 9200 | TCP | Wazuh Indexer |

Z poziomu DC01 wykonano testy:

```powershell
Test-NetConnection 192.168.100.30 -Port 1514
Test-NetConnection 192.168.100.30 -Port 1515
```

Oba testy zakończyły się sukcesem.

---

# Wazuh Agent – DC01

## Instalacja

Na kontrolerze domeny **DC01** zainstalowano Wazuh Agent w wersji **4.14.7**.

Agent został skonfigurowany do komunikacji z Managerem:

```text
192.168.100.30
```

Nazwa agenta:

```text
DC01
```

Po instalacji usługę uruchomiono:

```powershell
NET START Wazuh
```

---

## Status agenta

Po rejestracji w Dashboardzie agent otrzymał:

| Parametr | Wartość |
|----------|---------|
| Agent ID | `001` |
| Nazwa | `DC01` |
| IP | `192.168.100.10` |
| Grupa | `default` |
| Wersja | `4.14.7` |
| Status | `active` |

Oznacza to, że DC01 poprawnie komunikuje się z centralnym Wazuh Managerem.

---

# Źródła danych

Agent na DC01 został skonfigurowany do zbierania Windows Event Logs:

```text
Application
Security
System
```

W konfiguracji zastosowano format:

```xml
<log_format>eventchannel</log_format>
```

Dodatkowo skonfigurowano pobieranie zdarzeń Sysmon:

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

---

# Integracja Sysmon + Wazuh

Jednym z ważniejszych elementów projektu jest połączenie Sysmona z Wazuh.

Przepływ danych:

```text
                 DC01
                  │
        ┌─────────▼─────────┐
        │       Sysmon      │
        └─────────┬─────────┘
                  │
                  ▼
      Windows Event Log
                  │
                  ▼
          Wazuh Agent
                  │
             TCP 1514
                  │
                  ▼
         Wazuh Manager
                  │
                  ▼
         Wazuh Indexer
                  │
                  ▼
         Wazuh Dashboard
```

Dzięki temu zdarzenia generowane przez Sysmon mogą być centralnie analizowane w Wazuh.

---

# Monitorowanie systemu

Konfiguracja agenta obejmuje również dodatkowe moduły Wazuh.

## Security Configuration Assessment

Aktywny jest moduł SCA:

```xml
<sca>
  <enabled>yes</enabled>
  <scan_on_start>yes</scan_on_start>
  <interval>12h</interval>
</sca>
```

Pozwala on na ocenę konfiguracji bezpieczeństwa systemu.

## File Integrity Monitoring

Aktywny jest moduł `syscheck`, wykorzystywany do monitorowania zmian w wybranych plikach oraz elementach rejestru Windows.

## System inventory

Aktywny jest również `syscollector`, który zbiera informacje dotyczące między innymi:

- sprzętu,
- systemu operacyjnego,
- sieci,
- pakietów,
- portów,
- procesów,
- użytkowników,
- grup,
- usług,
- rozszerzeń przeglądarek.

---

# Dashboard i analiza zdarzeń

Wazuh Dashboard umożliwia centralną analizę danych otrzymywanych z agentów.

Podczas testów na DC01 obserwowano między innymi zdarzenia powiązane z technikami MITRE ATT&CK.

Przykładowe techniki widoczne w środowisku:

| ID MITRE | Technika |
|----------|----------|
| T1021.002 | SMB/Windows Admin Shares |
| T1078 | Valid Accounts |
| T1105 | Ingress Tool Transfer |
| T1053.005 | Scheduled Task/Job |

Dashboard pozwala również analizować zdarzenia według taktyk MITRE ATT&CK, poziomu alertu, agenta oraz czasu wystąpienia.

---

# Test integracji

W celu sprawdzenia przepływu danych wygenerowano kontrolowane zdarzenie Sysmon poprzez uruchomienie procesu:

```powershell
Start-Process notepad.exe
```

Uruchomienie procesu zostało zarejestrowane przez Sysmon jako zdarzenie **Process Create (Event ID 1)**.

Następnie zdarzenie zostało pobrane przez Wazuh Agent i przesłane do centralnego Wazuh Managera.

Test potwierdził poprawne działanie integracji:

```text
Process
   ↓
Sysmon
   ↓
Windows Event Log
   ↓
Wazuh Agent
   ↓
Wazuh Manager
   ↓
Wazuh Dashboard
```

---

# Znaczenie dla projektu

Wdrożenie Wazuh zmieniło laboratorium z infrastruktury przeznaczonej wyłącznie do nauki administracji w środowisko umożliwiające również **centralne monitorowanie bezpieczeństwa**.

Połączenie:

- Active Directory,
- Windows Server,
- Sysmon,
- Wazuh Agent,
- Wazuh Manager,
- Wazuh Indexer,
- Wazuh Dashboard

tworzy podstawę do dalszych ćwiczeń z zakresu:

- SOC,
- SIEM,
- detekcji zagrożeń,
- analizy logów,
- MITRE ATT&CK,
- incident response,
- testów penetracyjnych.

---

# Podsumowanie

Na Ubuntu 26.04 wdrożono Wazuh 4.14.7 w architekturze all-in-one. Uruchomiono Manager, Indexer oraz Dashboard.

Następnie do infrastruktury dodano Wazuh Agent na kontrolerze domeny DC01. Agent został poprawnie zarejestrowany i posiada status `active`.

Skonfigurowano zbieranie Windows Event Logs oraz integrację z kanałem Sysmon. Wykonano również test end-to-end z wykorzystaniem uruchomienia `notepad.exe`.

Wazuh stanowi obecnie centralny element monitorowania bezpieczeństwa w laboratorium i będzie wykorzystywany podczas kolejnych scenariuszy testowych z Windows 11 oraz Kali Linux.