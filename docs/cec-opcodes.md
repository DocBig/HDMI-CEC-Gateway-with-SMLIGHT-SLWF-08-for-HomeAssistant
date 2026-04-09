# CEC Opcode Referenz

Häufig verwendete CEC-Opcodes für die manuelle Steuerung über `esphome.hdmi_cec_gateway_XXXXX_cec_send`.

## Power

| Opcode | Name | Beschreibung |
|--------|------|-------------|
| `0x04` | Image View On | TV einschalten |
| `0x36` | Standby | Gerät in Standby |

## Eingang / Routing

| Opcode | Name | Beschreibung |
|--------|------|-------------|
| `0x82` | Active Source | Aktive Quelle ankündigen |
| `0x9D` | Inactive Source | Quelle als inaktiv markieren |
| `0x86` | Set Stream Path | Stream-Pfad setzen |

## Tastencodes (User Control)

Werden zusammen mit `0x44` (Key Pressed) und `0x45` (Key Released) verwendet.

| Code | Taste |
|------|-------|
| `0x00` | OK / Select |
| `0x01` | Pfeil Hoch |
| `0x02` | Pfeil Runter |
| `0x03` | Pfeil Links |
| `0x04` | Pfeil Rechts |
| `0x09` | Root Menu |
| `0x0D` | Exit / Zurück |
| `0x10` | Home / Top Menu |
| `0x20` | Taste 0 |
| `0x21` | Taste 1 |
| `0x22` | Taste 2 |
| `0x23` | Taste 3 |
| `0x24` | Taste 4 |
| `0x25` | Taste 5 |
| `0x26` | Taste 6 |
| `0x27` | Taste 7 |
| `0x28` | Taste 8 |
| `0x29` | Taste 9 |
| `0x35` | Display Information / Info |
| `0x41` | Volume Up |
| `0x42` | Volume Down |
| `0x43` | Mute |
| `0x44` | Play |
| `0x45` | Stop |
| `0x46` | Pause |
| `0x48` | Rewind |
| `0x49` | Fast Forward |
| `0x4B` | Forward / Next |
| `0x4C` | Backward / Previous |
| `0x6C` | Power Off |
| `0x6D` | Power On |

## Beispiel: Taste senden

```yaml
# Key Pressed + Key Released immer zusammen senden!
- action: esphome.hdmi_cec_gateway_4dbde0_cec_send
  data:
    destination: 0    # an TV
    data: [0x44, 0x01]  # Key Pressed: Pfeil Hoch
- action: esphome.hdmi_cec_gateway_4dbde0_cec_send
  data:
    destination: 0
    data: [0x45]        # Key Released
```

## Geräte-Abfragen

| Opcode | Name | Beschreibung |
|--------|------|-------------|
| `0x83` | Give Physical Address | Physische Adresse anfragen |
| `0x84` | Report Physical Address | Physische Adresse melden |
| `0x8C` | Give Device Vendor ID | Hersteller-ID anfragen |
| `0x87` | Device Vendor ID | Hersteller-ID melden |
| `0x8F` | Give Device Power Status | Power-Status anfragen |
| `0x90` | Report Power Status | Power-Status melden |
| `0x46` | Give OSD Name | OSD-Name anfragen |
| `0x47` | Set OSD Name | OSD-Name setzen |

## Audio

| Opcode | Name | Beschreibung |
|--------|------|-------------|
| `0x71` | Give Audio Status | Audio-Status anfragen |
| `0x7A` | Report Audio Status | Audio-Status melden |
| `0x72` | Set System Audio Mode | System-Audio-Modus setzen |
| `0xC3` | Request ARC Initiation | ARC starten |
| `0xC1` | Report ARC Initiated | ARC gestartet |
| `0xC4` | Request ARC Termination | ARC beenden |
| `0xC5` | Terminate ARC | ARC beendet |

## Physische Adressen

| Adresse | Hex | Beschreibung |
|---------|-----|-------------|
| HDMI 1 | `0x1000` | `[0x10, 0x00]` |
| HDMI 2 | `0x2000` | `[0x20, 0x00]` |
| HDMI 3 | `0x3000` | `[0x30, 0x00]` |
| HDMI 4 | `0x4000` | `[0x40, 0x00]` |
