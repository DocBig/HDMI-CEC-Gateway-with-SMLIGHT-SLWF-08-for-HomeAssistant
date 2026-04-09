# Changelog

🇩🇪 [Deutsche Version](#deutsche-version) | 🇬🇧 [English version](#english-version)

---

## English version

All notable changes to this project are documented here.

Format based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

---
### [Unreleased]

#### Added
- **CEC Commander** – Picture Elements card (`CEC-Commander/cec-remote-picture-elements.yaml`) with remote control image as background; invisible click areas for all buttons (requires [card-mod](https://github.com/thomasloven/lovelace-card-mod))
- **Scan warning banner** – conditional markdown card inside the scan section, visible only while a scan is running

#### Fixed
- **Dashboard sensor entity IDs corrected** – `sensor.cec_raw` / `sensor.cec_readable` replaced with `sensor.hdmi_cec_gateway_4dbde0_cec_raw` / `sensor.hdmi_cec_gateway_4dbde0_cec_readable`; MAC suffix comment added to dashboard file header

#### Changed
- **Dashboard layout** – scan and log sections separated from the remote control area with a visual divider (`🔧 Diagnostics & Bus Monitor`)

#### Docs
- **Standby warning** added – TV USB ports cut power in standby on most TVs; workaround documented
- **3D print case** link added (MakerWorld)
- **CHANGELOG links** corrected from Gitea to GitHub

### [1.2.0] – 2026-04-10

#### Security
- **AP password moved to secrets** – fallback AP password is now loaded from `!secret ap_password` instead of being hardcoded
- **API encryption enabled** – ESPHome native API now uses an encryption key (`!secret api_encryption_key`)
- **Web server authentication** – web server on port 80 is now protected by username/password (`!secret web_user` / `!secret web_password`)

#### Changed
- **`script.cec_send` is now the single send entry point** – all scripts now route through `script.cec_send`; only the MAC suffix needs to be updated in one place
- **Physical address via substitutions** – `hdmi_phys_addr`, `hdmi_phys_hi`, `hdmi_phys_lo` control the HDMI port without manual sync
- **External component pinned** to `@main` branch with `refresh: 1d`
- **Vendor ID handler** no longer filtered by `source: 0x0` – all devices receive a response
- **CPU set to 160 MHz** via `platformio_options`
- **WiFi stability** – `fast_connect: true` and `power_save_mode: none` added
- **NeoPixel block** marked as optional in comment

#### Added
- **Channel Up / Down** (`cec_channel_up`, `cec_channel_down`) – CEC opcodes `0x30` / `0x31`
- **`secrets.yaml.example`** updated with all required keys
- **Inactive Source address comment** in `cec.yaml` for different HDMI port configurations

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
### [Unreleased]

#### Hinzugefügt
- **CEC Commander** – Picture-Elements-Karte (`CEC-Commander/cec-remote-picture-elements.yaml`) mit Fernbedienungsbild als Hintergrund; unsichtbare Klickflächen für alle Tasten (benötigt [card-mod](https://github.com/thomasloven/lovelace-card-mod))
- **Scan-Hinweisbanner** – Conditional-Markdown-Karte im Scan-Bereich, nur während eines aktiven Scans sichtbar

#### Behoben
- **Dashboard-Sensor-Entity-IDs korrigiert** – `sensor.cec_raw` / `sensor.cec_readable` durch `sensor.hdmi_cec_gateway_4dbde0_cec_raw` / `sensor.hdmi_cec_gateway_4dbde0_cec_readable` ersetzt; MAC-Suffix-Hinweis am Dateikopf ergänzt

#### Geändert
- **Dashboard-Layout** – Scan- und Log-Bereich durch visuellen Trenner (`🔧 Diagnose & Bus-Monitor`) vom Fernbedienungsbereich getrennt

#### Dokumentation
- **Standby-Warnung** ergänzt – USB-Ports der meisten TVs trennen im Standby die Stromversorgung; Workaround dokumentiert
- **3D-Druck-Gehäuse**-Link hinzugefügt (MakerWorld)
- **CHANGELOG-Links** von Gitea auf GitHub korrigiert

### [1.2.0] – 2026-04-10

#### Sicherheit
- **AP-Passwort in secrets ausgelagert** – Fallback-AP-Passwort wird jetzt aus `!secret ap_password` geladen statt hartkodiert zu sein
- **API-Verschlüsselung aktiviert** – ESPHome Native API nutzt jetzt einen Verschlüsselungskey (`!secret api_encryption_key`)
- **Web-Server-Authentifizierung** – Web-Server auf Port 80 ist jetzt durch Benutzername/Passwort geschützt (`!secret web_user` / `!secret web_password`)

#### Geändert
- **`script.cec_send` ist jetzt der einzige Sende-Einstiegspunkt** – alle Scripts rufen jetzt `script.cec_send` auf; der MAC-Suffix muss nur noch an einer Stelle angepasst werden
- **Physische Adresse über Substitutions** – `hdmi_phys_addr`, `hdmi_phys_hi`, `hdmi_phys_lo` steuern den HDMI-Port ohne manuelle Synchronisierung
- **Externe Komponente gepinnt** auf Branch `@main` mit `refresh: 1d`
- **Vendor-ID-Handler** nicht mehr auf `source: 0x0` gefiltert – alle Geräte erhalten eine Antwort
- **CPU auf 160 MHz** über `platformio_options` gesetzt
- **WLAN-Stabilität** – `fast_connect: true` und `power_save_mode: none` ergänzt
- **Neopixel-Block** als optional im Kommentar gekennzeichnet

#### Hinzugefügt
- **Programm +/−** (`cec_channel_up`, `cec_channel_down`) – CEC-Opcodes `0x30` / `0x31`
- **`secrets.yaml.example`** mit allen benötigten Keys aktualisiert
- **Kommentar zu Inactive-Source-Adressen** in `cec.yaml` für verschiedene HDMI-Port-Konfigurationen

### [1.1.1] – 2026-04-07

#### Added
- **Farbtasten** Rot, Grün, Gelb, Blau im Dashboard und als Skripte (`cec_key_red`, `cec_key_green`, `cec_key_yellow`, `cec_key_blue`) mit CEC-Opcodes `0x72`, `0x73`, `0x74`, `0x71`
- **Programm +/−** (`cec_channel_up`, `cec_channel_down`) – CEC-Opcodes `0x30` / `0x31`
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

[Unreleased]: https://github.com/DocBig/HDMI-CEC-Gateway-with-SMLIGHT-SLWF-08-for-HomeAssistant/compare/v1.2.0...HEAD
[1.2.0]: https://github.com/DocBig/HDMI-CEC-Gateway-with-SMLIGHT-SLWF-08-for-HomeAssistant/compare/v1.1.1...v1.2.0
[1.1.1]: https://github.com/DocBig/HDMI-CEC-Gateway-with-SMLIGHT-SLWF-08-for-HomeAssistant/compare/v1.1.0...v1.1.1
[1.1.0]: https://github.com/DocBig/HDMI-CEC-Gateway-with-SMLIGHT-SLWF-08-for-HomeAssistant/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/DocBig/HDMI-CEC-Gateway-with-SMLIGHT-SLWF-08-for-HomeAssistant/releases/tag/v1.0.0
