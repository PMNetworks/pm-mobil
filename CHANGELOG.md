# Änderungen

## 1.1.0 – 2026-11-13

- Live Activities der Automationen: PM Mobil führt die laufenden Activities (`pm_mobil.live_activity`,
  `pm_mobil.live_activity_beenden`) mit den Feldern von `script.live_activity` (Restzeit in Sekunden,
  `fortschritt_max`, `icon`, `farbe`, `ton`, `nur_zuhause`). Die offene App zeigt genau diese; mit Schlüssel kommen sie
  zusätzlich per Push.
- Einrichtung ohne Schlüssel des Apple Developer Program: dann nur lokal, ohne Push.

## 1.0.0 – 2026-11-13

- Erste Version: Push über APNs (`notify.pm_mobil`, `notify.pm_mobil_<gerät>` mit den Feldern der Companion-App),
  Anrufe per VoIP (`pm_mobil.anruf`, `pm_mobil.anruf_beenden`), Live Activities per Push, WebSocket-Befehle für
  PM HomeControl.
