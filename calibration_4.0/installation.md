# Kalibrierung

Ersteller: frix-x <https://github.com/Frix-x>\
Bearbeitet und modifiziert von: Fragmon <https://github.com/Fragmon>

## Beschreibung

Dieses Set von Makros und Kalibrierungstools, welche dir bei der Einrichtung deines Druckers helfen.

## Installation

  1. Kopiere die Makrodatei "calibrate_4.0.cfg" direkt in deine eigene Konfiguration.
  2. Stelle sicher, dass die Klipper-Erweiterung 'gcode_shell_command.py' installiert ist.
  Die einfachste Installationsmethode ist die Nutzung des erweiterten Bereichs von KIAUH.\
  [KIAUH installieren](https://www.obico.io/blog/install-klipper-with-kiauh/#install-kiauh-on-your-raspberry-pi)\
  Starte KIAUH -> 4) [Erweitert] -> 8) [G-Code Shell Befehl]
  3. Installiere die Shake-Tune-Python (diese werden in klippy/extras integriert und lassen sich über Befehle später aufrufen
     ```bash
     wget -O - https://raw.githubusercontent.com/Frix-x/klippain-shaketune/main/install.sh | bash
     ```
  5. Füge folgende Konfiguration in deine  `printer.cfg` ein:
     ```
     [shaketune]
     # result_folder: ~/printer_data/config/ShakeTune_results
     #    The folder where the results will be stored. It will be created if it doesn't exist.
     # number_of_results_to_keep: 3
     #    The number of results to keep in the result_folder. The oldest results will
     #    be automatically deleted after each runs.
     # keep_raw_csv: False
     #    If True, the raw CSV files will be kept in the result_folder alongside the
     #    PNG graphs. If False, they will be deleted and only the graphs will be kept.
     # show_macros_in_webui: True
     #    Mainsail and Fluidd doesn't create buttons for "system" macros that are not in the
     #    printer.cfg file. If you want to see the macros in the webui, set this to True.
     # timeout: 300
     #    The maximum time in seconds to let Shake&Tune process the CSV files and generate the graphs.
     ```
  6. Füge folgende Konfiguration in deine `moonraker.conf` ein:
     ```
    [update_manager Klippain-ShakeTune]
    type: git_repo
    origin: https://github.com/Frix-x/klippain-shaketune.git
    path: ~/klippain_shaketune
    virtualenv: ~/klippy-env
    requirements: requirements.txt
    system_dependencies: system-dependencies.json
    primary_branch: main
    managed_services: klipper

## Funktionalität

  `FLOW_MULTIPLIER_CALIBRATION`: Bestimmt den optimalen Extrusionsfaktor. Dieser kann im Slicer gespeichert werden.
    COMPUTE_FLOW_MULTIPLIER: Wird verwendet, um den optimalen Extrusionsfaktor zu berechnen.

  `PRESSURE_ADVANCE_CALIBRATION`: Bestimmt das optimale PA. Kann im Slicer gespeichert werden.
  
  `MAX_FLOW_CALIBRATION`: Bestimmt den maximalen Durchfluss unter den eingestellten Bedingungen.
  
  `AXES_SHAPER_CALIBRATION`: Bestimmt die optimalen Werte für den Input Shaper. Diese können in der printer.cfg gespeichert werden.
  
  `VIBRATIONS_CALIBRATION`: Bestimmt die Vibrationen abhängig von der Geschwindigkeit. Sollte nur mit aktiviertem Input Shaper ausgeführt werden.
  
  `BELTS_SHAPER_CALIBRATION`: Bestimmt die Spannung der Riemen. Nur nutzbar für CoreXY-Systeme.
