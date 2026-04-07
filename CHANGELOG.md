# Changelog

🇩🇪 [Deutsche Version](#deutsche-version) | 🇬🇧 [English version](#english-version)

---

## English version

All notable changes to this project are documented here.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---

### [1.1.1] – 2026-04-07

#### Added
- **Colour keys** Red, Green, Yellow, Blue in the dashboard and as scripts (`cec_key_red`, `cec_key_green`, `cec_key_yellow`, `cec_key_blue`) with CEC opcodes `0x72`, `0x73`, `0x74`, `0x71`
- **CEC Bus Log** – toggle switch (`input_boolean.cec_bus_log_enabled`) in the dashboard shows a logbook card displaying the complete CEC bus traffic of the last hour
- **NeoPixel TV Backlight** – WS2812X LED strip on GPIO12 (`light.tv_backlight`, 60 LEDs, configurable)
- **English dashboard** (`cec_dashboard_en.yaml`) and English README (`README_EN.md`)

#### Fixed
- **Duplicate entries in device scan** – devices can spontaneously send `0x84` (Report Physical Address), e.g. when powering on. The `cec_scan_collect` automation now checks whether the logical address already exists in the list before adding it
- **Colour key opcodes corrected** – CEC standard maps `0x71=Blue`, `0x72=Red`, `0x73=Green`, `0x74=Yellow` (F1–F4), not the original order

---

### [1.1.0] – 2026-04-03

#### Added
- **Device scan** (`script.cec_scan_start`, button in the dashboard) – sequential ping of all CEC addresses 0–14 with `0x83`, result stored in `input_text.cec_scan_results`
- Scan events: `esphome.cec_scan_started`, `esphome.cec_scan_result`, `esphome.cec_scan_finished`
- Scan results displayed as a persistent HA notification after completion
- Collapsible address reference table in the dashboard

#### Changed
- ESPHome component switched to fork [`DocBig/esphome-native-hdmi-cec`](https://github.com/DocBig/esphome-native-hdmi-cec) (more stable, actively maintained)

---

### [1.0.0] – 2026-04-01

#### Added
- ESPHome firmware for SMLIGHT SLWF-08 (`hdmi-cec-gateway.yaml`)
- HA package (`packages/cec.yaml`) with scripts for power, volume, HDMI input, navigation, playback, and number keys 0–9
- Ready-made dashboard remote control (`dashboards/cec_dashboard.yaml`)
- TV model selection (Philips / Samsung) for volume routing
- CEC event forwarding as `esphome.cec_rx` with `source`, `destination`, `raw`, and `readable`
- CEC opcode reference at `docs/cec-opcodes.md`

---

---

## Deutsche Version

Alle nennenswerten Änderungen an diesem Projekt werden hier dokumentiert.

Format basiert auf [Keep a Changelog](https://keepachangelog.com/de/1.1.0/).

---

### [1.1.1] – 2026-04-07

#### Added
- **Farbtasten** Rot, Grün, Gelb, Blau im Dashboard und als Skripte (`cec_key_red`, `cec_key_green`, `cec_key_yellow`, `cec_key_blue`) mit CEC-Opcodes `0x72`, `0x73`, `0x74`, `0x71`
- **CEC Bus-Log** – Toggle-Schalter (`input_boolean.cec_bus_log_enabled`) im Dashboard blendet eine Logbook-Karte ein, die den vollständigen CEC-Busverlauf der letzten Stunde anzeigt
- **NeoPixel TV-Backlight** – WS2812X LED-Strip an GPIO12 (`light.tv_backlight`, 60 LEDs, konfigurierbar)
- **Englisches Dashboard** (`cec_dashboard_en.yaml`) und englische README (`README_EN.md`)

#### Fixed
- **Doppelte Einträge im Geräte-Scan** – Geräte können spontan `0x84` (Report Physical Address) senden (z. B. beim Einschalten). Die `cec_scan_collect`-Automation prüft jetzt vor dem Hinzufügen, ob die logische Adresse bereits in der Liste steht
- **Farbtasten-Opcodes korrigiert** – CEC-Standard ordnet `0x71=Blau`, `0x72=Rot`, `0x73=Grün`, `0x74=Gelb` (F1–F4), nicht die ursprüngliche Reihenfolge

---

### [1.1.0] – 2026-04-03

#### Added
- **Geräte-Scan** (`script.cec_scan_start`, Button im Dashboard) – sequenzieller Ping aller CEC-Adressen 0–14 mit `0x83`, Ergebnis in `input_text.cec_scan_results`
- Scan-Events: `esphome.cec_scan_started`, `esphome.cec_scan_result`, `esphome.cec_scan_finished`
- Scan-Ergebnisse als persistente HA-Benachrichtigung nach Abschluss
- Adress-Referenz-Tabelle im Dashboard (ausklappbar)

#### Changed
- ESPHome-Komponente auf Fork [`DocBig/esphome-native-hdmi-cec`](https://github.com/DocBig/esphome-native-hdmi-cec) umgestellt (stabiler, aktiv gewartet)

---

### [1.0.0] – 2026-04-01

#### Added
- ESPHome-Firmware für SMLIGHT SLWF-08 (`hdmi-cec-gateway.yaml`)
- HA-Package (`packages/cec.yaml`) mit Skripten für Power, Lautstärke, HDMI-Eingang, Navigation, Wiedergabe und Zahlentasten 0–9
- Fertige Dashboard-Fernbedienung (`dashboards/cec_dashboard.yaml`)
- TV-Modell-Auswahl (Philips / Samsung) für Lautstärke-Routing
- CEC-Ereignis-Weiterleitung als `esphome.cec_rx` mit `source`, `destination`, `raw` und `readable`
- CEC-Opcode-Referenz unter `docs/cec-opcodes.md`

---

[Unreleased]: http://172.16.0.142:3000/sysadmin/HDMI-CEC-Gateway-with-SMLIGHT-SLWF-08-for-Home-Assistant/compare/v1.1.0...HEAD
[1.1.0]: http://172.16.0.142:3000/sysadmin/HDMI-CEC-Gateway-with-SMLIGHT-SLWF-08-for-Home-Assistant/compare/v1.0.0...v1.1.0
[1.0.0]: http://172.16.0.142:3000/sysadmin/HDMI-CEC-Gateway-with-SMLIGHT-SLWF-08-for-Home-Assistant/releases/tag/v1.0.0
