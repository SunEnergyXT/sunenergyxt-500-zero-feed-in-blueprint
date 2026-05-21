# SunEnergyXT 500 Series Zero Feed-in Blueprint

**Deutsch ist die Standardversion. English is available as an optional blueprint.**

Home Assistant blueprint for SunEnergyXT 500 / 500 Pro systems with an external power meter. The automation keeps the external meter close to the configured grid-power target by adjusting the SunEnergyXT grid-port power setpoint and max inverter power setpoint. It does not change the device operation mode or discharge mode.

## Blueprint URLs

German default:

```text
https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.yaml
```

English optional:

```text
https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.en.yaml
```

If GitHub raw is slow in Home Assistant, use the jsDelivr URLs:

```text
https://cdn.jsdelivr.net/gh/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint@main/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.yaml
https://cdn.jsdelivr.net/gh/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint@main/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.en.yaml
```

## Deutsch

### 1. Voraussetzungen

- Home Assistant 2024.6 oder neuer.
- SunEnergyXT 500 / 500 Pro Integration ist eingerichtet.
- Ein externer Zähler ist in Home Assistant verfügbar.
- Der Zähler liefert aktuelle Leistung, nicht nur Energiezählerstände.

Unterstützte Zählerformen:

| Zählertyp | Konfiguration |
| --- | --- |
| Shelly Pro 3EM | Zählertyp `Shelly Pro 3EM` wählen und das Zählergerät auswählen |
| EcoTracker | Zählertyp `EcoTracker` wählen und das Zählergerät auswählen |
| BitShake / Tasmota | Zählertyp `BitShake / Tasmota` wählen und das Zählergerät auswählen |
| Shelly 3EM | Zählertyp `Shelly 3EM` wählen und die drei Phasengeräte L1 / L2 / L3 auswählen |
| Benutzerdefinierter Zähler | Zählertyp `Benutzerdefinierter Zähler` wählen, Formel auswählen und passende Entitäten eintragen |

### 2. Blueprint importieren

In Home Assistant öffnen:

`Einstellungen` -> `Automatisierungen & Szenen` -> `Blueprints` -> `Blueprint importieren`

Dann die deutsche Standard-URL einfügen:

```text
https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.yaml
```

Für die englische Oberfläche stattdessen diese URL verwenden:

```text
https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.en.yaml
```

Lokale Installation ist ebenfalls möglich. Die YAML-Datei nach Home Assistant kopieren:

```text
/config/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.yaml
```

Danach die Blueprint-Seite neu laden.

### 3. Automatisierung erstellen

1. In Home Assistant `Einstellungen` -> `Automatisierungen & Szenen` -> `Blueprints` öffnen.
2. `SunEnergyXT 500 Serie - Nulleinspeisung` auswählen.
3. `Automatisierung erstellen` wählen.
4. Zuerst `SunEnergyXT-Gerät` auswählen.
5. SOC-Grenzen, maximale Netzeinspeiseleistung und AC-gekoppelte maximale Ladeleistung einstellen.
6. Zählertyp wählen und nur das dazu passende Zählerformular ausfüllen.
7. Speichern und die Automatisierung aktivieren.

### 4. SunEnergyXT-Einstellungen

| Einstellung | Beschreibung |
| --- | --- |
| SunEnergyXT-Gerät | Das zu steuernde SunEnergyXT 500 / 500 Pro Gerät |
| Verhalten bei voller Batterie | `Nach Vollladung Last folgen` oder `Nach Vollladung PV folgen` |
| Maximale Netzeinspeiseleistung | 800 W für SunEnergyXT 500, 2400 W für SunEnergyXT 500 Pro, sofern vor Ort kein niedrigerer Grenzwert gilt |
| System Entladegrenze | Wird in die Geräteeinstellung geschrieben und als untere SOC-Grenze verwendet |
| System Ladegrenze | Wird in die Geräteeinstellung geschrieben und als obere SOC-Grenze verwendet |
| Maximale AC-gekoppelte Ladeleistung | SunEnergyXT 500 und 500 Pro können bis zu 2400 W verwenden, falls vor Ort zulässig |

### 5. Zähler-Einstellungen

Der Blueprint zeigt alle Zählerformulare an, weil Home Assistant Blueprint-Felder nicht dynamisch anhand des Zählertyps ausblenden kann. Nur das passende Formular öffnen und ausfüllen; die anderen leer lassen.

Bei falscher Richtung unter `Erweiterte Zählerzeichen- und Einheitseinstellungen` die Vorzeichenlogik ändern:

| Beobachtung | Einstellung |
| --- | --- |
| Einspeisung wird positiv angezeigt | `Export/feed-in is positive` |
| Netzbezug wird positiv angezeigt | `Import/grid consumption is positive` |

Einheit: Wenn der Zähler in W liefert, Multiplikator `1` verwenden. Wenn der Zähler in kW liefert, Multiplikator `1000` verwenden.

### 6. Verhalten

- Standardziel ist `0 W` am externen Zähler.
- Positive externe Zählerleistung bedeutet Einspeisung.
- Negative externe Zählerleistung bedeutet Netzbezug.
- Bei voller Batterie kann die Automatisierung je nach Einstellung der Last oder der PV folgen.
- Für `Nach Vollladung PV folgen` gibt es eine 1-%-Speicherzone nahe der oberen SOC-Grenze, damit der volle Zustand nicht ständig ein- und ausgeschaltet wird.
- Diagnoseprotokolle sind standardmäßig aus. Im Debug-Modus schreibt der Blueprint Entscheidungslogs höchstens alle 5 Sekunden.

### 7. Erste Inbetriebnahme

- Automatisierung zunächst deaktiviert speichern.
- Prüfen, dass SunEnergyXT-Gerät und Zählerentitäten nicht `unknown` oder `unavailable` sind.
- Automatisierung aktivieren und prüfen, ob der externe Zähler in Richtung `0 W` läuft.
- Bei falscher Richtung die Vorzeicheneinstellung des Zählers korrigieren.
- Bei langsamer Reaktion die Aktualisierungsrate des externen Zählers und die erweiterten Regelintervalle prüfen.

## English

### 1. Requirements

- Home Assistant 2024.6 or newer.
- SunEnergyXT 500 / 500 Pro integration is set up.
- An external meter is available in Home Assistant.
- The meter provides live power, not only accumulated energy.

Supported meter types:

| Meter type | Configuration |
| --- | --- |
| Shelly Pro 3EM | Select `Shelly Pro 3EM` and select the meter device |
| EcoTracker | Select `EcoTracker` and select the meter device |
| BitShake / Tasmota | Select `BitShake / Tasmota` and select the meter device |
| Shelly 3EM | Select `Shelly 3EM` and select the three phase devices L1 / L2 / L3 |
| Custom meter | Select `Custom meter`, choose a formula, and provide the matching entities |

### 2. Import the blueprint

Open Home Assistant:

`Settings` -> `Automations & Scenes` -> `Blueprints` -> `Import blueprint`

Use the German default URL:

```text
https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.yaml
```

Use the English URL if you want English blueprint labels:

```text
https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.en.yaml
```

Local installation is also supported. Copy the YAML file to:

```text
/config/blueprints/automation/sunenergyxt/sunenergyxt-500-zero-feed-in.en.yaml
```

Then reload the blueprint page.

### 3. Create an automation

1. Open `Settings` -> `Automations & Scenes` -> `Blueprints`.
2. Select `SunEnergyXT 500 Series - Zero Feed-in`.
3. Click `Create automation`.
4. Select the `SunEnergyXT device` first.
5. Configure SOC limits, maximum on-grid output power, and AC-coupled maximum charge power.
6. Select the meter type and fill only the matching meter section.
7. Save and enable the automation.

### 4. SunEnergyXT settings

| Setting | Description |
| --- | --- |
| SunEnergyXT device | The SunEnergyXT 500 / 500 Pro device controlled by this automation |
| Full-battery behavior | `Follow load after full` or `Follow PV after full` |
| Maximum on-grid output power | Use 800 W for SunEnergyXT 500 and 2400 W for SunEnergyXT 500 Pro unless a lower local limit is required |
| System Min Discharge SOC | Written to the device setting and used as the low-SOC threshold |
| System Max Charge SOC | Written to the device setting and used as the full-battery threshold |
| AC-coupled maximum charge power | SunEnergyXT 500 and 500 Pro can use up to 2400 W if allowed by the local installation |

### 5. Meter settings

The blueprint shows all meter sections because Home Assistant blueprints cannot dynamically hide fields based on the selected meter type. Expand and fill only the matching section; leave the others blank.

If the sign direction is wrong, open `Advanced meter sign and unit settings` and change the sign convention:

| Observation | Setting |
| --- | --- |
| Export/feed-in is shown as positive | `Export/feed-in is positive` |
| Import/grid consumption is shown as positive | `Import/grid consumption is positive` |

Unit conversion: use multiplier `1` for W and `1000` for kW.

### 6. Behavior

- The default target is `0 W` at the external meter.
- Positive external meter power means feed-in/export.
- Negative external meter power means grid import.
- At full battery, the automation follows the selected full-battery behavior.
- For `Follow PV after full`, a 1% memory band near the upper SOC limit prevents repeated full-state toggling.
- Diagnostic logging is off by default. In debug mode, decision logs are written at most every 5 seconds.

### 7. First run

- Save the automation disabled first.
- Confirm that the SunEnergyXT and meter entities are not `unknown` or `unavailable`.
- Enable the automation and verify that the external meter moves toward `0 W`.
- If the direction is wrong, adjust the meter sign convention.
- If control is slow, check the external meter update rate and the advanced control intervals.
