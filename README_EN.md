# HDMI-CEC Gateway with SMLIGHT SLWF-08 for Home Assistant

> Control your TV and connected devices via HDMI-CEC from Home Assistant using a SMLIGHT SLWF-08 adapter – ESPHome firmware, HA package, and a ready-to-use dashboard included.

![ESPHome](https://img.shields.io/badge/ESPHome-2026-blue)
![License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat-square)
![Platform](https://img.shields.io/badge/platform-ESP8266-orange.svg?style=flat-square)
![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Running-41BDF5?style=flat-square&logo=homeassistant&logoColor=white)

> Full changelog: [CHANGELOG](CHANGELOG.md) | 🇩🇪 [Deutsche Version](README.md)

---

## What is this?

**HDMI-CEC** is a standard that allows devices to control each other over the HDMI cable – for example, the TV automatically switching to the correct input, or a soundbar's volume being adjusted. Almost all modern TVs support it, even though manufacturers give the feature their own names (Philips calls it *EasyLink*, Samsung *Anynet+*).

This project turns the **SMLIGHT SLWF-08** – a small, affordable HDMI adapter based on the ESP8266 – into a CEC gateway for Home Assistant. The adapter simply plugs into a free HDMI port, receives all CEC messages on the bus, and forwards them to Home Assistant. From there you can control your TV, receiver, and other devices via scripts, automations, or a ready-made dashboard.

**Supported TV brands:**
- ✅ Philips (EasyLink) – fully tested
- ⚠️ Samsung (Anynet+) – configuration included, not tested

---

## Required Hardware

- [SMLIGHT SLWF-08](https://smartlight.me/smart-home-devices/wifi-devices/slwf-08-controller) – HDMI-CEC adapter (~€10)
- USB-A to USB-C cable for power (via the TV's USB port)
- A running Home Assistant installation
- *(Optional)* WS2812X LED strip (e.g. 60 LEDs) on GPIO12 for TV backlight

The SLWF-08 plugs into the TV's HDMI port and is powered directly from its USB port – no external power supply needed.

> ⚠️ **Standby note:** On most TVs the USB port cuts power when the TV enters standby. The SLWF-08 will then lose its Wi-Fi connection and become unreachable in HA – meaning CEC commands like "turn on TV" cannot be sent. A workaround is an external USB power supply (e.g. a USB charger at a wall outlet) while keeping the HDMI plug in the TV.

**Enclosure (3D print):**  
A matching 3D-printable case for the SLWF-08 is available on MakerWorld:  
👉 [Case for Smartlight.me SLWF-08 (MakerWorld)](https://makerworld.com/de/models/892432-case-for-smartlight-me-slwf-08#profileId-849227)

---

## Project Structure

```
hdmi-cec-gateway/
├── esphome/
│   ├── hdmi-cec-gateway.yaml      # Firmware for the SLWF-08
│   └── secrets.yaml.example       # Template for Wi-Fi credentials
├── homeassistant/
│   ├── packages/
│   │   └── cec.yaml               # HA package with all scripts and automations
│   ├── dashboards/
│   │   ├── cec_dashboard.yaml     # Remote control dashboard (German)
│   │   └── cec_dashboard_en.yaml  # Remote control dashboard (English)
│   └── CEC-Commander/
│       ├── cec-remote-picture-elements.yaml  # Picture Elements card (visual remote)
│       └── cec-remote.png                    # Remote control background image
└── docs/
    └── cec-opcodes.md             # CEC opcode reference for custom extensions
```

---

## Setup

### Step 1 – Prepare your TV

Make sure HDMI-CEC is enabled in your TV settings:

**Philips:** Settings → EasyLink → On  
**Samsung:** Settings → General → External Device Manager → Anynet+ (HDMI-CEC) → On

---

### Step 2 – Flash the SLWF-08

**Enter your credentials:**  
Copy `esphome/secrets.yaml.example` to `esphome/secrets.yaml` and fill in your details:

```yaml
wifi_ssid_1: "YourWiFi"
wifi_password_1: "YourPassword"
ota_password: "YourOTAPassword"
api_encryption_key: "GENERATE_THIS_KEY_IN_ESPHOME_DASHBOARD"
ap_password: "YourFallbackAPPassword"
web_user: "admin"
web_password: "YourWebPassword"
```

> **Note:** Generate the `api_encryption_key` in the ESPHome dashboard – just click the key button. After the first flash you'll need to enter the key once in the HA integration settings.

**Set the HDMI port:**  
Open `esphome/hdmi-cec-gateway.yaml` and adjust the HDMI port via the substitutions at the top of the file. Only these three lines need changing:

```yaml
substitutions:
  hdmi_phys_addr: "0x3000"   # 0x1000=HDMI1  0x2000=HDMI2  0x3000=HDMI3  0x4000=HDMI4
  hdmi_phys_hi:   "0x30"     # 0x10  =HDMI1  0x20  =HDMI2  0x30  =HDMI3  0x40  =HDMI4
  hdmi_phys_lo:   "0x00"     # always 0x00
```

The values are automatically applied to all relevant locations in the YAML – no manual synchronisation required.

The required HDMI-CEC component is automatically downloaded from GitHub during the build:

```yaml
external_components:
  - source: github://DocBig/esphome-native-hdmi-cec@main
    refresh: 1d
```

Then flash the file via ESPHome.

---

### Step 3 – Add the HA Package

Copy `homeassistant/packages/cec.yaml` into the `packages/` folder of your HA configuration and include it:

```yaml
# configuration.yaml
homeassistant:
  packages:
    cec: !include packages/cec.yaml
```

---

### Step 4 – Set your MAC Suffix

After flashing, the SLWF-08 will be automatically discovered by Home Assistant and appear as a new device under:

**Settings → Devices & Services → ESPHome**

The MAC suffix is the 6-character hex string at the end of the device name, e.g. `hdmi-cec-gateway-4dbde0`. You can also find the exact service name under:

**Developer Tools → Actions → search for "esphome"**

Replace the MAC suffix in `cec.yaml` inside `script.cec_send` (line ~109):

```yaml
script:
  cec_send:
    sequence:
      - action: esphome.hdmi_cec_gateway_4dbde0_cec_send  # ← only change THIS
```

This is **the only place** in the entire package you need to adapt for your device. All other scripts call `script.cec_send` internally.

**Adapt the dashboard:**  
Also replace `4dbde0` with your MAC suffix in the dashboard file – affects the sensors in the CEC Log section:

```
sensor.hdmi_cec_gateway_4dbde0_cec_raw
sensor.hdmi_cec_gateway_4dbde0_cec_readable
                         ^^^^^^
                        → replace with your MAC suffix
```

A comment at the top of the dashboard file marks the affected lines.

---

### Step 5 – Select TV Model

In HA under **Settings → Devices & Services → Helpers** → set `CEC TV Model` to your TV brand – or switch it later directly in the dashboard.

This setting affects how volume commands are sent, which differs between manufacturers:

| TV Brand | Volume Mode |
|----------|-------------|
| Philips (EasyLink) | Direct to TV |
| Samsung (Anynet+) | Broadcast to all devices |

---

### Step 6 – Set Up the Dashboard

**HA → Settings → Dashboards → New Dashboard → RAW Editor** → paste the contents of `homeassistant/dashboards/cec_dashboard_en.yaml`.

The dashboard includes a complete remote control with navigation, volume, HDMI input selection, playback, number keys, and colour buttons.

---

## CEC Commander (Picture Elements)

As an alternative to the standard button dashboard, there is a visual remote control as a **Picture Elements Card** – invisible click areas overlaid on a real remote control image.

[![CEC Remote](https://raw.githubusercontent.com/DocBig/HDMI-CEC-Gateway-with-SMLIGHT-SLWF-08-for-HomeAssistant/main/homeassistant/CEC-Commander/cec-remote.png)](https://raw.githubusercontent.com/DocBig/HDMI-CEC-Gateway-with-SMLIGHT-SLWF-08-for-HomeAssistant/main/homeassistant/CEC-Commander/cec-remote.png)

**Requirements:**
- [card-mod](https://github.com/thomasloven/lovelace-card-mod) installed via HACS (for card size limiting)

**Setup:**

1. Copy `homeassistant/CEC-Commander/cec-remote.png` to `/config/www/`
2. In HA → **Settings → Dashboards → Add Card → Manual Card** → paste the contents of `homeassistant/CEC-Commander/cec-remote-picture-elements.yaml`

The card is displayed with `max-width: 225px` by default. Adjust the value in the `card_mod` block as needed. Without card-mod, remove the `card_mod` block and place the card in a `grid` or `columns` view instead.

**Supported buttons:** Power on/off, volume, mute, HDMI 1–4, navigation (D-pad, OK, Back, Home, Menu, Info), playback (Play/Pause/Stop/FF/RW/Next/Prev), number keys 0–9, colour keys (Red/Green/Yellow/Blue), channel +/−

---

## TV Backlight (NeoPixel)

The firmware includes an optional NeoPixel configuration for a WS2812X LED strip as TV bias lighting. The strip is connected directly to the SLWF-08 on **GPIO12**.

```yaml
light:
  - platform: neopixelbus
    type: GRB
    variant: WS2812X
    pin: GPIO12
    num_leds: 60        # Adjust LED count
    name: "TV backlight"
```

After flashing, `light.tv_backlight` appears in Home Assistant and can be controlled like any other light – colour, brightness, effects. This allows the backlight to automatically turn on/off with the TV, or be driven by an Ambilight-style automation.

> **Note:** The SLWF-08 supplies a maximum of 500 mA via its 3.3 V rail. For longer strips (>10 LEDs at full brightness) an external 5 V power supply for the strip is recommended. The data pin (GPIO12) can still be used directly from the SLWF-08.

---

## Available Scripts

All scripts can be used directly in automations, the dashboard, or the Developer Tools.

| Script | Description |
|--------|-------------|
| `script.cec_tv_on` | Turn TV on |
| `script.cec_standby_all` | Send all devices to standby |
| `script.cec_volume_up` | Increase volume |
| `script.cec_volume_down` | Decrease volume |
| `script.cec_mute` | Mute |
| `script.cec_hdmi1` – `cec_hdmi4` | Switch HDMI input |
| `script.cec_nav_up/down/left/right` | Arrow keys |
| `script.cec_nav_ok` | OK / Enter |
| `script.cec_nav_back` | Back |
| `script.cec_nav_home` | Home |
| `script.cec_nav_menu` | Menu |
| `script.cec_nav_info` | Info |
| `script.cec_play/pause/stop` | Playback control |
| `script.cec_forward/rewind` | Fast-forward / Rewind |
| `script.cec_next/previous` | Next / Previous track |
| `script.cec_key_0` – `cec_key_9` | Number keys |
| `script.cec_key_red` / `green` / `yellow` / `blue` | Colour keys (0x72 / 0x73 / 0x74 / 0x71) |
| `script.cec_channel_up` / `cec_channel_down` | Channel +/− (0x30 / 0x31) |
| `script.cec_scan_start` | Start device scan |

---

## Device Scan

Using `script.cec_scan_start` (or the **Scan** button in the dashboard), all 15 logical CEC addresses are queried sequentially. Every device that responds appears in the results list.

The scan runs entirely on the ESP – no additional code in HA is needed. The result is stored in `input_text.cec_scan_results` and displayed as a persistent notification when complete.

**Flow:**
1. Press button → ESP resets counter, fires `esphome.cec_scan_started`
2. Every 300 ms the next address (0–14) is pinged with `0x83`
3. Responses (opcode `0x84`) fire `esphome.cec_scan_result` to HA
4. After address 14, `esphome.cec_scan_finished` is fired

**Scan events:**

| Event | Fields | Description |
|-------|--------|-------------|
| `esphome.cec_scan_started` | – | Scan started, list is reset |
| `esphome.cec_scan_result` | `logical_address`, `physical_address`, `device_type` | Device found |
| `esphome.cec_scan_finished` | – | All addresses queried |

**Example result:**
```
Addr 0 | 0.0.0.0 | TV
Addr 3 | 1.0.0.0 | Tuner
Addr 5 | 2.0.0.0 | AudioSystem
```

> **Note:** Some devices (especially audio systems) do not always respond to every ping. A second scan pass can reveal missing devices.

---

## Using CEC Events in Automations

Every received CEC message is forwarded as a Home Assistant event `esphome.cec_rx`. This lets you react to any TV event – for example dimming lights when the TV turns on, or putting other devices into standby when the TV goes off.

The event format is `(source<<4|dest):opcode:data`, e.g. `30:04`. For reliable conditions, the `readable` field is recommended as it contains the command as plain text:

```yaml
# Example: automation when TV goes to standby
automation:
  - alias: "TV goes to standby"
    trigger:
      - platform: event
        event_type: esphome.cec_rx
    condition:
      - condition: template
        value_template: "{{ '<Standby>' in trigger.event.data.readable }}"
    action:
      - service: notify.notify
        data:
          message: "TV is going to standby"
```

Each event contains the following fields:

| Field | Description |
|-------|-------------|
| `source` | Sender of the CEC command (0 = TV, 3 = Tuner/Receiver, 5 = Soundbar, …) |
| `destination` | Recipient of the command |
| `raw` | Raw data as hex string in format `(source<<4\|dest):opcode:data`, e.g. `30:04` |
| `readable` | Human-readable description, e.g. `30:04 => Tuner1 to TV: <Image View On>[]` |

Opcodes for custom automations: [docs/cec-opcodes.md](docs/cec-opcodes.md)

---

## CEC Device Addresses

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

## Security

The firmware uses encrypted communication. The following secrets must be set in `secrets.yaml`:

| Secret | Description |
|--------|-------------|
| `api_encryption_key` | Encryption key for the ESPHome native API (generate in the ESPHome dashboard) |
| `ap_password` | Password for the fallback access point (min. 8 characters) |
| `web_user` / `web_password` | Credentials for the built-in web server |

> **Tip:** After the first flash, Home Assistant will show a prompt asking for the API key. Just enter the `api_encryption_key` from your `secrets.yaml`.

---

## Notes & Known Limitations

- **Navigation keys** are forwarded by the TV to the currently active source by default, not to the TV itself. This matches the CEC standard and is not a bug in this project.
- **More log entries than expected:** The component does not always reliably filter messages by destination address – the SLWF-08 occasionally responds to requests directed at other devices. This does not affect functionality.
- **Incomplete device scan:** Audio systems and some receivers do not always reliably respond to individual pings. A second scan pass usually returns the complete result.

---

## External Component

This project uses the fork [esphome-native-hdmi-cec](https://github.com/DocBig/esphome-native-hdmi-cec) by [@DocBig](https://github.com/DocBig), based on the original component by [@Palakis](https://github.com/Palakis/esphome-hdmi-cec). The fork adds `Frame::to_string()` support for human-readable CEC messages.

---

## License

MIT License – see [LICENSE](LICENSE)
