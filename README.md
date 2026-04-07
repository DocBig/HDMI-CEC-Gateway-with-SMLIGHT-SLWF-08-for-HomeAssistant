# HDMI-CEC Gateway with SMLIGHT SLWF-08 for Home Assistant

> Control your TV and connected devices via HDMI-CEC from Home Assistant using a SMLIGHT SLWF-08 adapter – ESPHome firmware, HA package, and a ready-to-use dashboard included.

Steuere deinen TV und angeschlossene Geräte über HDMI-CEC direkt aus Home Assistant – mit einem SMLIGHT SLWF-08 als Gateway.

![ESPHome](https://img.shields.io/badge/ESPHome-2026-blue)
![License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)
![Platform](https://img.shields.io/badge/platform-ESP8266-orange.svg?style=flat-square)
![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Running-41BDF5?style=flat-square&logo=homeassistant&logoColor=white)

> Alle Änderungen: [CHANGELOG](CHANGELOG.md) | 🇬🇧 [English version](README_EN.md)

---

## Was ist das?

**HDMI-CEC** ist ein Standard, der es Geräten ermöglicht, sich gegenseitig über das HDMI-Kabel zu steuern – zum Beispiel wenn der TV automatisch auf den richtigen Eingang wechselt oder die Lautstärke einer Soundbar geregelt wird. Fast alle modernen TVs unterstützen das, auch wenn der Hersteller dem Feature eigene Namen gibt (Philips nennt es *EasyLink*, Samsung *Anynet+*).

Dieses Projekt verwandelt den **SMLIGHT SLWF-08** – einen kleinen, günstigen HDMI-Adapter auf Basis des ESP8266 – in ein CEC-Gateway für Home Assistant. Der Adapter steckt einfach in einen freien HDMI-Port, empfängt alle CEC-Nachrichten im Netzwerk und leitet sie an Home Assistant weiter. Von dort aus kannst du TV, Receiver und andere Geräte über Skripte, Automationen oder ein fertiges Dashboard steuern.

**Unterstützte TV-Marken:**
- ✅ Philips (EasyLink) – vollständig getestet
- ⚠️ Samsung (Anynet+) – Konfiguration vorhanden, nicht getestet

---

## Benötigte Hardware

- [SMLIGHT SLWF-08](https://smartlight.me/smart-home-devices/wifi-devices/slwf-08-controller) – HDMI-CEC Adapter (~10 €)
- USB-A-auf-USB-C-Kabel zur Stromversorgung (über die USB-Buchse des TVs)
- Eine laufende Home Assistant Installation
- *(Optional)* WS2812X LED-Strip (z. B. 60 LEDs) an GPIO12 für TV-Backlight

Der SLWF-08 steckt im HDMI-Port des TVs und wird direkt über dessen USB-Buchse mit Strom versorgt – keine externe Stromquelle nötig.

---

## Projektstruktur

```
hdmi-cec-gateway/
├── esphome/
│   ├── hdmi-cec-gateway.yaml   # Firmware für den SLWF-08
│   └── secrets.yaml.example    # Vorlage für WLAN-Zugangsdaten
├── homeassistant/
│   ├── packages/
│   │   └── cec.yaml            # HA-Package mit allen Skripten und Automationen
│   └── dashboards/
│       ├── cec_dashboard.yaml     # Fertige Fernbedienung als Dashboard (Deutsch)
│       └── cec_dashboard_en.yaml  # Fertige Fernbedienung als Dashboard (Englisch)
└── docs/
    └── cec-opcodes.md          # CEC-Opcode-Referenz für eigene Erweiterungen
```

---

## Einrichtung

### Schritt 1 – TV vorbereiten

Stelle sicher, dass HDMI-CEC in den TV-Einstellungen aktiviert ist:

**Philips:** Einstellungen → EasyLink → Ein  
**Samsung:** Einstellungen → Allgemein → Externe Geräte-Manager → Anynet+ (HDMI-CEC) → Ein

---

### Schritt 2 – SLWF-08 flashen

**WLAN-Zugangsdaten und OTA-Passwort eintragen:**  
Kopiere `esphome/secrets.yaml.example` nach `esphome/secrets.yaml` und trage deine Daten ein:

```yaml
wifi_ssid_1: "DeinWLAN"
wifi_password_1: "DeinPasswort"
ota_password: "DeinOTAPasswort"
```

**HDMI-Port einstellen:**  
Öffne `esphome/hdmi-cec-gateway.yaml` und passe den HDMI-Port an, in den du den SLWF-08 steckst:

```yaml
hdmi_cec:
  physical_address: 0x3000  # Hier den Port anpassen:
                             # 0x1000 = HDMI 1
                             # 0x2000 = HDMI 2
                             # 0x3000 = HDMI 3
                             # 0x4000 = HDMI 4
```

Den gleichen Wert musst du auch im `0x83`-Handler im ersten Byte aktualisieren:

```yaml
data: [0x84, 0x30, 0x00, 0x04]
#            ^^^^
#            0x10 = HDMI 1, 0x20 = HDMI 2, 0x30 = HDMI 3, 0x40 = HDMI 4
```

Die benötigte HDMI-CEC-Komponente wird beim Build automatisch von GitHub geladen – du musst nichts manuell installieren:

```yaml
external_components:
  - source: github://Palakis/esphome-hdmi-cec
```

Danach die Datei in ESPHome flashen.

---

### Schritt 3 – HA-Package einbinden

Kopiere `homeassistant/packages/cec.yaml` in den `packages/`-Ordner deiner HA-Konfiguration und binde es ein:

```yaml
# configuration.yaml
homeassistant:
  packages:
    cec: !include packages/cec.yaml
```

---

### Schritt 4 – MAC-Suffix anpassen

Nach dem Flashen wird der SLWF-08 automatisch von Home Assistant erkannt und erscheint als neues Gerät unter:

**Einstellungen → Geräte & Dienste → ESPHome**

Dort siehst du das Gerät mit seinem Namen – der MAC-Suffix ist die 6-stellige Hex-Zeichenkette am Ende, z. B. `hdmi-cec-gateway-4dbde0`. Den genauen Service-Namen findest du außerdem unter:

**Entwicklerwerkzeuge → Aktionen → nach „esphome" suchen**

Ersetze in `cec.yaml` alle Vorkommen von `4dbde0` durch deinen eigenen MAC-Suffix:

```
esphome.hdmi_cec_gateway_4dbde0_cec_send
                         ^^^^^^
                        → durch deinen MAC-Suffix ersetzen
```

Das ist die einzige Stelle, die du für dein Gerät anpassen musst.

---

### Schritt 5 – TV-Modell wählen

In HA unter **Einstellungen → Geräte & Dienste → Eingaben** → `CEC TV Modell` auf dein TV-Modell setzen – oder später direkt im Dashboard umschalten.

Diese Einstellung beeinflusst, wie die Lautstärke-Befehle gesendet werden – das ist je nach Hersteller unterschiedlich:

| TV-Marke | Lautstärke-Modus |
|----------|-----------------|
| Philips (EasyLink) | Direkt an TV |
| Samsung (Anynet+) | Broadcast an alle Geräte |

---

### Schritt 6 – Dashboard einrichten

**HA → Einstellungen → Dashboards → Neues Dashboard → RAW-Editor** → Inhalt von `homeassistant/dashboards/cec_dashboard.yaml` einfügen.

Das Dashboard enthält eine vollständige "Demo" Fernbedienung mit Navigation, Lautstärke, HDMI-Eingangswahl, Wiedergabe und Zahlentasten.

---

## TV-Backlight (NeoPixel)

Die Firmware enthält eine optionale NeoPixel-Konfiguration für einen WS2812X LED-Strip als TV-Hintergrundbeleuchtung. Der Strip wird direkt am SLWF-08 an **GPIO12** angeschlossen.

```yaml
light:
  - platform: neopixelbus
    type: GRB
    variant: WS2812X
    pin: GPIO12
    num_leds: 60        # Anzahl LEDs anpassen
    name: "TV backlight"
```

Nach dem Flashen erscheint `light.tv_backlight` in Home Assistant und kann wie jedes andere Licht gesteuert werden – Farbe, Helligkeit, Effekte. So lässt sich das Backlight z. B. automatisch mit dem TV ein- und ausschalten oder per Ambilight-Automation steuern.

> **Hinweis:** Der SLWF-08 liefert maximal 500 mA über seine 3,3 V-Leitung. Bei längeren Strips (>10 LEDs mit voller Helligkeit) empfiehlt sich eine externe 5 V-Stromversorgung für den Strip. Der Daten-Pin (GPIO12) kann trotzdem direkt vom SLWF-08 verwendet werden.

---

## Verfügbare Skripte

Alle Skripte können direkt in Automationen, dem Dashboard oder den Entwicklerwerkzeugen verwendet werden.

| Skript | Beschreibung |
|--------|-------------|
| `script.cec_tv_on` | TV einschalten |
| `script.cec_standby_all` | Alle Geräte in Standby |
| `script.cec_volume_up` | Lautstärke erhöhen |
| `script.cec_volume_down` | Lautstärke verringern |
| `script.cec_mute` | Stummschalten |
| `script.cec_hdmi1` – `cec_hdmi4` | HDMI-Eingang wechseln |
| `script.cec_nav_up/down/left/right` | Pfeiltasten |
| `script.cec_nav_ok` | OK / Enter |
| `script.cec_nav_back` | Zurück |
| `script.cec_nav_home` | Home |
| `script.cec_nav_menu` | Menü |
| `script.cec_nav_info` | Info |
| `script.cec_play/pause/stop` | Wiedergabe steuern |
| `script.cec_forward/rewind` | Vor-/Zurückspulen |
| `script.cec_next/previous` | Nächster/Vorheriger Titel |
| `script.cec_key_0` – `cec_key_9` | Zahlentasten |
| `script.cec_key_red` / `green` / `yellow` / `blue` | Farbtasten (0x71–0x74) |
| `script.cec_scan_start` | Geräte-Scan starten |

---

## Geräte-Scan

Mit `script.cec_scan_start` (oder dem **Scan-Button** im Dashboard) werden alle 15 logischen CEC-Adressen sequenziell abgefragt. Jedes Gerät das antwortet, erscheint in der Ergebnisliste.

Der Scan läuft vollständig auf dem ESP – kein zusätzlicher Code in HA nötig. Das Ergebnis wird in `input_text.cec_scan_results` gespeichert und als persistente Benachrichtigung angezeigt.

**Ablauf:**
1. Button drücken → ESP setzt Zähler zurück, feuert `esphome.cec_scan_started`
2. Alle 300 ms wird die nächste Adresse (0–14) mit `0x83` angepingt
3. Antworten (Opcode `0x84`) feuern `esphome.cec_scan_result` nach HA
4. Nach Adresse 14 wird `esphome.cec_scan_finished` gefeuert

**Scan-Events:**

| Event | Felder | Beschreibung |
|-------|--------|-------------|
| `esphome.cec_scan_started` | – | Scan gestartet, Liste wird zurückgesetzt |
| `esphome.cec_scan_result` | `logical_address`, `physical_address`, `device_type` | Gerät gefunden |
| `esphome.cec_scan_finished` | – | Alle Adressen abgefragt |

**Beispielergebnis:**
```
Addr 0 | 0.0.0.0 | TV
Addr 3 | 1.0.0.0 | Tuner
Addr 5 | 2.0.0.0 | AudioSystem
```

> **Hinweis:** Manche Geräte (insbesondere Audio-Systeme) antworten nicht immer auf jeden Ping. Ein zweiter Scan-Durchlauf kann fehlende Geräte aufdecken.

---

## CEC-Events in Automationen nutzen

Jede empfangene CEC-Nachricht wird als Home Assistant Event `esphome.cec_rx` weitergeleitet. So kannst du auf beliebige TV-Ereignisse reagieren – zum Beispiel Lichter dimmen wenn der TV eingeschaltet wird, oder andere Geräte in Standby schalten wenn der TV ausgeht.

Das Event-Format ist `(source<<4|dest):opcode:data`, z. B. `30:04`. Für zuverlässige Conditions empfiehlt sich das `readable`-Feld, das den Befehl als Text enthält:

```yaml
# Beispiel: Automation wenn TV in Standby geht
automation:
  - alias: "TV geht in Standby"
    trigger:
      - platform: event
        event_type: esphome.cec_rx
    condition:
      - condition: template
        value_template: "{{ '<Standby>' in trigger.event.data.readable }}"
    action:
      - service: notify.notify
        data:
          message: "TV geht in Standby"
```

Jedes Event enthält folgende Felder:

| Feld | Beschreibung |
|------|-------------|
| `source` | Absender des CEC-Befehls (0 = TV, 3 = Tuner/Receiver, 5 = Soundbar, …) |
| `destination` | Empfänger des Befehls |
| `raw` | Rohdaten als Hex-String im Format `(source<<4\|dest):opcode:data`, z. B. `30:04` |
| `readable` | Menschenlesbare Beschreibung, z. B. `30:04 => Tuner1 to TV: <Image View On>[]` |

Opcodes für eigene Automationen: [docs/cec-opcodes.md](docs/cec-opcodes.md)

---

## CEC-Geräteadressen

| Address | Device |
|:-------:|--------|
| `0`  | TV |
| `1`  | Recording Device 1 |
| `2`  | Recording Device 2 |
| `3`  | Tuner 1 |
| `4`  | Playback Device 1 |
| `5`  | Audio System |
| `6`  | Tuner 2 |
| `7`  | Tuner 3 |
| `8`  | Playback Device 2 |
| `9`  | Playback Device 3 |
| `10` | Tuner 4 |
| `11` | Playback Device 2 |
| `12` | Reserved |
| `13` | Reserved |
| `14` | Free Use |
| `15` | Unregistered *(as initiator)* / Broadcast *(as destination)* |


---

## Hinweise & bekannte Einschränkungen

- **Navigationstasten** werden vom TV standardmäßig an die aktuell aktive Quelle weitergeleitet, nicht an den TV selbst. Das entspricht dem CEC-Standard und ist kein Fehler dieses Projekts.
- **Mehr Log-Einträge als erwartet:** Die verwendete Palakis-Komponente filtert Nachrichten nach Zieladresse nicht immer zuverlässig – der SLWF-08 reagiert gelegentlich auf Anfragen, die eigentlich an andere Geräte gerichtet sind. Das hat keinen Einfluss auf die Funktion.
- **Geräte-Scan unvollständig:** Audio-Systeme und manche Receiver antworten nicht immer zuverlässig auf einzelne Pings. Ein zweiter Scan-Durchlauf bringt in der Regel das vollständige Ergebnis.

---

## Externe Komponente

Dieses Projekt nutzt die [esphome-hdmi-cec](https://github.com/Palakis/esphome-hdmi-cec) Komponente von [@Palakis](https://github.com/Palakis).

---

## Lizenz

MIT License – siehe [LICENSE](LICENSE)
