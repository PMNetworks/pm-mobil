# PM Mobil

Integration für Home Assistant: Push an **PM HomeControl** direkt über Apple (APNs). Die Companion-App nutzt den
Relay-Dienst von Home Assistant, der nur Geräte der offiziellen App annimmt; PM Mobil ist der eigene Weg dafür.

Proprietär, © 2026 PM Networks. Siehe `LICENSE` und `AGENTS.md` im Repository.

## Was sie kann

| Dienst | Zweck |
|---|---|
| `notify.pm_mobil`, `notify.pm_mobil_<gerät>` | Mitteilungen; versteht die Felder der Companion-App (`data.push.interruption-level`, `tag`, `group`, `subtitle`, `url`, `actions`, `image`, `entity_id`, `clear_notification`). `critical` kommt als zeitkritische Mitteilung und zusätzlich als Anruf „Alarmanlage“ (abschaltbar). |
| `pm_mobil.anruf` | Haustür, Alarm oder Gefahr als Anruf (VoIP-Push, CallKit). Die Haustür ruft standardmäßig nur Geräte an, deren Person unterwegs ist (`nur_unterwegs`). Antwort: Kennung und erreichte Geräte. |
| `pm_mobil.anruf_beenden` | Alle iPhones hören auf zu klingeln. |
| `pm_mobil.live_activity` | Live Activity anlegen oder aktualisieren; Felder wie `script.live_activity` (`tag`, `titel`, `meldung`, `stufe`, `fortschritt`, `fortschritt_max`, `icon`, `farbe`, `restzeit` in Sekunden, `ton`, `nur_zuhause`). Die offene App zeigt sie sofort; mit Schlüssel kommt sie per Push (Push-to-Start, Aktualisieren). |
| `pm_mobil.live_activity_beenden` | Live Activity mit diesem Tag schließen (Gegenstück zu `script.live_activity_beenden`). |

Ereignisse: `pm_mobil_anruf`, `pm_mobil_anruf_angenommen` (für die Übergabe des Türdienstes in Panel Studio),
`pm_mobil_geraet_registriert` und `mobile_app_notification_action` für Knöpfe in Mitteilungen (wie die Companion-App).

## Einrichtung

1. Apple Developer Program: unter *Certificates, Identifiers & Profiles → Keys* einen Schlüssel mit „Apple Push
   Notifications service (APNs)“ anlegen, `.p8` herunterladen, Schlüssel-ID und Team-ID notieren.
2. HACS → oben rechts ⋮ → Benutzerdefinierte Repositories → `https://github.com/PMNetworks/pm-mobil`, Typ „Integration“;
   dann PM Mobil herunterladen und Home Assistant neu starten. Updates bietet HACS danach selbst an.
3. *Einstellungen → Geräte und Dienste → Integration hinzufügen → PM Mobil*: Team-ID, Schlüssel-ID, den Inhalt der
   `.p8`-Datei und die Bundle-ID der App eintragen. Ohne Apple Developer Program die drei Felder leer lassen: Dann
   arbeitet PM Mobil nur lokal, und die offene App zeigt die Live Activities der Automationen.
4. Optionen: Zonen, die als zu Hause gelten (etwa „Wohnung Umfeld“), und ob kritische Mitteilungen zusätzlich anrufen.
5. In der App (gebaut mit `PM_PUSH = YES`) einmal anmelden; das iPhone meldet sich selbst an. Je iPhone entsteht
   `notify.pm_mobil_<name>`.

Aus Xcode aufgespielte Apps nutzen die Entwicklungsumgebung von APNs, TestFlight und App Store die Produktion; das
iPhone teilt das bei der Anmeldung mit.

## Beispiele

Klingeln als Anruf (die Klingel-Entität ist ein Platzhalter):

```yaml
triggers:
  - trigger: state
    entity_id: binary_sensor.KLINGEL
    to: "on"
actions:
  - action: pm_mobil.anruf
    data:
      anlass: haustuer
      kennung: "{{ now().timestamp() | int }}"
```

Weiche in `script.live_activity`, damit die bestehenden Automationen unverändert bleiben (als weiterer Schritt neben
den Aufrufen von `notify.mobile_app_*`):

```yaml
- action: pm_mobil.live_activity
  data:
    tag: "{{ tag }}"
    titel: "{{ titel }}"
    meldung: "{{ meldung }}"
    stufe: "{{ stufe | default('', true) }}"
    fortschritt: "{{ fortschritt | default(0) }}"
    fortschritt_max: "{{ fortschritt_max | default(100) }}"
    icon: "{{ icon | default('mdi:home-assistant', true) }}"
    farbe: "{{ farbe | default('#2196F3', true) }}"
    restzeit: "{{ restzeit | default(0) }}"
    ton: "{{ ton | default(false) }}"
    nur_zuhause: "{{ nur_zuhause | default(false) }}"
```

Und in `script.live_activity_beenden`:

```yaml
- action: pm_mobil.live_activity_beenden
  data:
    tag: "{{ tag }}"
```

## Sicherheit

- Der Schlüssel liegt im Konfigurationseintrag von Home Assistant; das JWT wird alle 40 Minuten neu signiert.
- Die App meldet sich über den WebSocket des angemeldeten Benutzers an. Ein Gerät gehört dem Konto, das es angemeldet
  hat; ein anderes Konto kann es nicht übernehmen. Token werden nur als Hex-Zeichen angenommen.
- Ungültige Token (Apple meldet `Unregistered` oder `BadDeviceToken`) werden vergessen.

## Entwicklung

Der Quellcode liegt im privaten Repository von PM HomeControl (`HomeAssistant/pm_mobil`). Eine neue Version entsteht mit
`version` in `manifest.json` und einem Abschnitt in `CHANGELOG.md`; nach dem Merge auf `main` führt die Action
„PM Mobil Store“ das öffentliche Repository `PMNetworks/pm-mobil` nach und legt das Release an.

## Tests

```
pip install pytest-homeassistant-custom-component h2
pytest
```
