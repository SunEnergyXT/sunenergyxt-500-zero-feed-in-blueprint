# SunEnergyXT 500: Nulleinspeisung mit Home Assistant

**Deutsch** | [English](README.en.md)

Ein SunEnergyXT 500 / 500 Pro oder mehrere Geräte gemeinsam: Diese Blueprints
regeln die Leistung anhand eines externen Home-Assistant-Zählers.

## Blueprint auswählen

| Blueprint | Einsatz | Anleitung |
| --- | --- | --- |
| Einzelgerät | Ein SunEnergyXT 500 / 500 Pro | [Einzelgerät einrichten](#einzelgerät-einrichten) |
| Multi-Space | Bis zu neun Geräte an einem Haushaltszähler | [Multi-Space einrichten](#multi-space-einrichten) |

**Deutsche Import-URLs:**

| Blueprint | Home-Assistant-Import-URL |
| --- | --- |
| Einzelgerät | `https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.yaml` |
| Multi-Space | `https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-multi-zero-feed-in.yaml` |

## Voraussetzungen

- Home Assistant 2024.6 oder neuer.
- Die SunEnergyXT 500 / 500 Pro Integration ist eingerichtet.
- Ein externer Zähler liefert die aktuelle Leistung in Home Assistant, nicht nur Energiezählerstände.

## Blueprint importieren

In Home Assistant `Einstellungen` -> `Automatisierungen & Szenen` -> `Blueprints`
-> `Blueprint importieren` öffnen. Die passende URL aus der Tabelle oben einfügen
und anschließend aus dem importierten Blueprint eine Automatisierung erstellen.

Alternativ die gewählte YAML-Datei unter
`/config/blueprints/automation/sunenergyxt/` ablegen und die Blueprint-Seite neu laden.

## Einzelgerät einrichten

`SunEnergyXT 500 Serie - Nulleinspeisung` auswählen. Dieser Blueprint passt den
Sollwert am Netzanschluss und die maximale Wechselrichterleistung eines Geräts an.
Betriebsmodus und Entlademodus werden nicht verändert.

1. Das `SunEnergyXT-Gerät` auswählen.
2. SOC-Grenzen, maximale Netzeinspeiseleistung und maximale AC-gekoppelte Ladeleistung einstellen.
3. Das Verhalten bei voller Batterie auswählen.
4. Den Zähler wie unter [Zähler konfigurieren](#zähler-konfigurieren) einrichten.
5. Die Automatisierung zunächst deaktiviert speichern und die Hinweise zur Inbetriebnahme unten beachten.

| Einstellung | Beschreibung |
| --- | --- |
| Verhalten bei voller Batterie | `Nach Vollladung Last folgen` oder `Nach Vollladung PV folgen` |
| Maximale Netzeinspeiseleistung | 800 W für SunEnergyXT 500, 2400 W für SunEnergyXT 500 Pro, sofern vor Ort kein niedrigerer Grenzwert gilt |
| System Entladegrenze | Wird in die Geräteeinstellung geschrieben und als untere SOC-Grenze verwendet |
| System Ladegrenze | Wird in die Geräteeinstellung geschrieben und als obere SOC-Grenze verwendet |
| Maximale AC-gekoppelte Ladeleistung | Bis zu 2400 W je Gerät, falls vor Ort zulässig |

Beim Einzelgeräte-Blueprint verhindert eine 1-%-Speicherzone nahe der oberen
SOC-Grenze wiederholtes Umschalten bei `Nach Vollladung PV folgen`. Sind bei
bestätigtem vollem Zustand Zähler-, GP-, LP- oder PV-Daten vorübergehend ungültig,
hält er den PV-Bypass mit `GS = 0 W` und maximalem `IS`. Bei ungültigem SOC, GS
oder IS wird der Bypass nicht erzwungen.

## Multi-Space einrichten

`SunEnergyXT 500 Serie - Multi-Space-Nulleinspeisung` auswählen. Eine Automatisierung
koordiniert bis zu **neun SunEnergyXT 500 / 500 Pro** an einem Haushaltszähler.
Gerätemodelle werden über die Integration erkannt; eine manuelle Modellwahl ist
nicht erforderlich.

Die Netzanschlüsse der Geräte müssen unabhängig voneinander hinter diesem Zähler
angeschlossen sein, nicht am Lastausgang eines anderen gesteuerten Geräts.

1. Gerät 1 und bis zu acht weitere Geräte auswählen; ungenutzte Gerätefelder leer lassen.
2. SOC-Grenzen, Leistungsgrenzen und das Verhalten bei voller Batterie einstellen.
3. Den Haushaltszähler wie unter [Zähler konfigurieren](#zähler-konfigurieren) einrichten.
4. Die Automatisierung zunächst deaktiviert speichern.
5. Vor dem Aktivieren andere Automatisierungen deaktivieren, die dieselben Geräte regeln, einschließlich bisheriger Einzelgeräte-Automatisierungen.

`Schneller Lastsprungausgleich (optional)` ist standardmäßig eingeschaltet und
richtet die Korrektur nach der gesamten Leistungsabweichung am Haushaltszähler.
Die Wartezeit für Rückmeldungen gilt weiterhin; die tatsächliche Reaktionszeit
hängt auch von Zähler und Geräten ab. Eine bereits ausdrücklich gespeicherte
Einstellung `aus` bleibt erhalten.

## Zähler konfigurieren

Die Zählerkonfiguration gilt für beide Blueprints. Nur das zum Zählertyp passende
Formular öffnen und ausfüllen; die übrigen Formulare leer lassen.

| Zählertyp | Konfiguration |
| --- | --- |
| Shelly Pro 3EM | Zählertyp wählen und das Zählergerät auswählen |
| EcoTracker | Zählertyp wählen und das Zählergerät auswählen |
| BitShake / Tasmota | Zählertyp wählen und das Zählergerät auswählen |
| Shelly 3EM | Die drei Phasengeräte L1 / L2 / L3 auswählen; ihre Leistungen werden summiert |
| Benutzerdefinierter Zähler | Leistungsformel auswählen und passende Entitäten eintragen |

Die Vorzeicheneinstellung muss zum tatsächlichen Zählerwert passen:

- Zeigt der Zähler Einspeisung positiv an, die Option für positive Einspeisung wählen.
- Zeigt der Zähler Netzbezug positiv an, die Option für positiven Netzbezug wählen.
- Bei Leistung in W den Multiplikator `1` verwenden, bei kW `1000`.

## Inbetriebnahme und Hinweise

- Prüfen, dass die benötigten Geräte- und Zählerentitäten nicht `unknown` oder `unavailable` sind.
- Pro Gerät nur eine regelnde Automatisierung aktivieren, damit keine widersprüchlichen Leistungsbefehle entstehen.
- Standardziel ist `0 W` am externen Zähler. Nach dem Aktivieren prüfen, ob sich der Zähler dem Ziel nähert; der gewählte PV-Folgemodus kann bei voller Batterie Einspeisung zulassen.
- Bei falscher Regelrichtung die Vorzeicheneinstellung prüfen.
- Bei langsamer Reaktion die Aktualisierungsrate des Zählers, die Rückmeldewartezeit und die Regelintervalle prüfen. Ein kurzes Schreibintervall allein garantiert keine entsprechend schnelle Reaktion.
- Diagnoseprotokolle sind standardmäßig aus. Nur bei Bedarf aktivieren.
