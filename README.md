# 🛡️ Isolation Forest IDS – AI-based Network Intrusion Detection System

**Inżynierska praca projektowa**  
**Vladyslav Fedun** | Uniwersytet WSB Merito Wrocław, 2026  
Promotor: Dr Tomasz Długosz

---

## 📋 Opis projektu

System wykrywania intruzów (IDS) oparty na algorytmie **Isolation Forest** (uczenie maszynowe bez nadzoru). System monitoruje ruch sieciowy w czasie rzeczywistym i automatycznie wykrywa anomalie — ataki hakerskie, które nie posiadają znanych sygnatur w tradycyjnych bazach zagrożeń.

### Kluczowe funkcje:
- 🔍 Detekcja anomalii w czasie rzeczywistym (opóźnienie < 2 sekundy)
- 🤖 Samouczenie się na normalnym ruchu sieciowym ("ciszy")
- 🔁 Monitorowanie wielu segmentów sieci jednocześnie (LAN Corp + DMZ)
- 📊 Automatyczne generowanie raportów CSV i wykresów
- 🚨 Rozróżnianie ataków wewnętrznych od szumu statystycznego

---

## 🧠 Technologie

| Warstwa | Technologia |
|---------|-------------|
| Język | Python 3 |
| AI / ML | scikit-learn (IsolationForest), StandardScaler |
| Przechwytywanie pakietów | Scapy |
| Wizualizacja | Matplotlib |
| Środowisko testowe | VMware – Windows Server 2019, Windows 10, Kali Linux |
| Usługi sieciowe | Active Directory, DNS, IIS, RRAS/NAT |
| Ataki testowe | Xmas Scan, Null Scan, SYN Flood (Scapy/nmap) |

---

## 🏗️ Architektura środowiska

```
Internet (WAN)
      |
[Windows Server 2019] ← sensor IDS (Python)
      |              \
[LAN Corp]         [DMZ / LAN Guest]
10.10.10.0/24      10.10.11.0/24
      |                   |
[Windows 10]         [Kali Linux]
  (ofiara)             (agresor)
10.10.10.40          10.10.11.50
```

Serwer pełni rolę centralnego punktu monitorowania — każdy pakiet między podsieciami przechodzi przez sensor IDS.

---

## 📈 Wyniki badań – kalibracja modelu

| Czas uczenia | Pakiety | False Positives | Status |
|---|---|---|---|
| 5 min | ~2 500 | > 15% | ❌ Underfitting |
| 15 min | ~7 500 | ~8–10% | ⚠️ Niestabilny |
| 30 min | ~15 000 | ~3–5% | ✅ Akceptowalny |
| **60 min** | **~30 000** | **< 0.5%** | **🏆 Optymalny** |
| 120 min | ~60 000 | ~2–3% | ❌ Overfitting |

---

## 🎯 Wyniki testu końcowego (20-minutowy stress test)

| Kategoria | Pakiety | Udział |
|---|---|---|
| Ruch normalny (NORMAL) | 31 200 | 87.82% |
| **Wykryte ataki (LATERAL_ALERT)** | **1 807** | **5.09%** |
| Szum statystyczny | 1 098 | 3.09% |
| Ruch zewnętrzny (ignorowany) | 1 424 | 4.00% |
| **SUMA** | **35 529** | **100%** |

✅ **Wykryto 100% cykli ataku** z Kali Linux (Xmas Scan, Null Scan, SYN Flood)

---

## 📁 Struktura repozytorium

```
isolation-forest-ids/
├── README.md
├── ids.py                        ← główny skrypt IDS
├── screenshots/
│   ├── screenshot-00-ids-flow-diagram.jpg
│   ├── screenshot-01-network-topology.jpg
│   ├── screenshot-02-windows-server-adapter-corp.jpg
│   ├── screenshot-03-windows-server-adapter-dmz.jpg
│   ├── screenshot-04-windows-server-adapter-wan.jpg
│   ├── screenshot-05-active-directory-user.jpg
│   ├── screenshot-06-dns-test.jpg
│   ├── screenshot-07-ids-graph-5min-underfitting.jpg
│   ├── screenshot-08-ids-graph-15min-unstable.jpg
│   ├── screenshot-09-ids-graph-30min-acceptable.jpg
│   ├── screenshot-10-ids-graph-60min-optimal.jpg
│   ├── screenshot-11-ids-graph-120min-overfitting.jpg
│   └── screenshot-12-attack-detection-final-result.jpg
```

---

## 🚀 Uruchomienie

### Wymagania
```bash
pip install scapy scikit-learn pandas matplotlib joblib
```

### Tryb uczenia (zbieranie profilu normalnego ruchu)
```bash
python ids.py
# Wybierz: 1 = Train
```

### Tryb monitorowania
```bash
python ids.py
# Wybierz: 2 = Monitor
```

> ⚠️ Wymagane uruchomienie jako administrator/root. Zmień `IFACE_NAME` w kodzie na nazwę swojego interfejsu sieciowego.

---

## 👤 Autor

**Vladyslav Fedun**  
Inżynier Informatyki – WSB Merito Wrocław, 2026  
📧 risev44@gmail.com | 🐙 github.com/vfedun
