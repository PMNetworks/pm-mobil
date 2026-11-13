# Änderungen

Versionen nach dem Schema JJJJ.MM.N, Releases als Tag `vJJJJ.MM.N`.

## 2026.11.1 – 2026-11-13

Erste Version im Kalenderschema (zuvor 1.0.0 bis 1.1.1).

- Push über APNs: `notify.pm_mobil` und `notify.pm_mobil_<gerät>` mit den Feldern der Companion-App.
- Anrufe per VoIP: `pm_mobil.anruf` (Haustür nur an Personen unterwegs), `pm_mobil.anruf_beenden`.
- Live Activities der Automationen: `pm_mobil.live_activity` und `pm_mobil.live_activity_beenden` mit den Feldern von
  `script.live_activity` (Restzeit in Sekunden, `fortschritt_max`, `icon`, `farbe`, `ton`, `nur_zuhause`). Die
  geöffnete App zeigt genau diese; mit Schlüssel kommen sie zusätzlich per Push.
- Einrichtung auch ohne Schlüssel des Apple Developer Program (nur lokal, ohne Push).
- Eigenes Symbol und Logo in Home Assistant (hell und dunkel), Banner, Beschreibung, Installation über HACS aus
  `PMNetworks/pm-mobil`.
