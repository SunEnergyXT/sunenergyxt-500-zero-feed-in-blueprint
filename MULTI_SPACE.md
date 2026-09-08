# Multi-Space Blueprint Preview

Separate preview for one household meter and up to nine independently
grid-connected SunEnergyXT 500 / 500 Pro devices. This does not replace the
single-device blueprint. Do not run another power controller for the same
devices at the same time.

## Import URLs

- [Deutsch](https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-multi-zero-feed-in.yaml)
- [English](https://raw.githubusercontent.com/SunEnergyXT/sunenergyxt-500-zero-feed-in-blueprint/main/blueprints/automation/sunenergyxt/sunenergyxt-500-multi-zero-feed-in.en.yaml)

## English

- Diagnostic logging is off by default. Enabled diagnostic messages use a
  60-second default interval, with a minimum interval of 10 seconds. Leave
  logging off for ordinary operation. Rate limiting does not bound total log
  storage; Home Assistant retention and integration errors are separate.
- Setpoints are written only when the control gates and change thresholds are
  met. This does not disable Home Assistant entity history or integration logs.
- Known limit: the shared blueprint SOC settings do not currently synchronize
  the devices' hardware SOC limits. A lower blueprint charge limit can declare
  a device full before its hardware enters full-battery operation, leading to
  incorrect GS=0 allocation and persistent grid import. Use this preview only
  with matching blueprint and hardware limits on every selected device.
- Hardware discharge inhibition or hysteresis can still block a device even
  when the blueprint considers discharge available. Other devices compensated
  in the tested setup, but this is not guaranteed for all capacity combinations.
- Three-device physical tests covered regrouping, external load/generation,
  lower-SOC gating and release, a nonzero meter target and output saturation.
  Nine-device, positive load-port and full-battery transition coverage is
  simulation coverage, not complete physical validation. Additional meter
  transport delay may require a longer existing feedback-settle setting.

## Deutsch

- Diagnoseprotokolle sind standardmaessig ausgeschaltet. Bei aktivierter
  Diagnose gilt standardmaessig ein Intervall von 60 Sekunden, mindestens
  10 Sekunden. Fuer den normalen Betrieb ausgeschaltet lassen. Die Begrenzung
  der Schreibrate begrenzt nicht die gesamte Dateigroesse; Aufbewahrung in
  Home Assistant und Fehlerprotokolle der Integration sind davon getrennt.
- Sollwerte werden nur bei erfuellten Freigaben und ausreichender Aenderung
  geschrieben. Entitaetsverlauf und Integrationsprotokolle bleiben davon
  unberuehrt.
- Bekannte Einschraenkung: Die gemeinsamen SOC-Einstellungen im Blueprint
  werden derzeit nicht mit den Hardware-SOC-Grenzen synchronisiert. Eine
  niedrigere Ladegrenze im Blueprint kann ein Geraet zu frueh als voll
  einstufen. Eine daraus folgende GS=0-Zuteilung kann dauerhaften Netzbezug
  verursachen. Diese Vorschau nur mit uebereinstimmenden Blueprint- und
  Hardware-Grenzen fuer jedes ausgewaehlte Geraet verwenden.
- Eine hardwareseitige Entladesperre oder Hysterese kann weiterhin die Ausgabe
  verhindern. Im getesteten Aufbau konnten andere Geraete ausgleichen; das
  ist keine Garantie fuer jede Kombination der verfuegbaren Leistungen.
- Physisch getestet wurden drei Geraete mit wechselnder Gruppenzugehoerigkeit,
  externer Last/Einspeisung, SOC-Untergrenze und Freigabe, einem von null
  abweichenden Zaehlerziel sowie erreichter Ausgangsgrenze. Neun Geraete,
  positive Lastanschlussleistung und Vollade-Uebergaenge sind simuliert,
  nicht vollstaendig physisch validiert. Bei zusaetzlicher Zaehlerverzoegerung
  kann eine laengere vorhandene Rueckmelde-Wartezeit erforderlich sein.
