Idee und Anleitung von Frix_x#0161 (https://github.com/Frix-x) 
Übersetzt von Fragmon#2722

# Kalibrierungsmakro 4.0

Update 07.07.2024 - Aktualisierungen der Anleitungen

## Funktionen

| Befehl | Beispielgrafiken |
|:------|:-------:|
|[`FLOW_MULTIPLIER_CALIBRATION`](./makros/Flow.md)<br /><br /> Bestimmt den optimalen Extrusionsfaktor. Dieser kann im Slicer gespeichert werden. | [<img src="./images/flow_calibration.png">](./functions/axes_map_calibration.md) |
|[`PRESSURE_ADVANCE_CALIBRATION`](./fmakros/PA.md)<br /><br />Bestimmt das optimale PA. Kann im Slicer gespeichert werden. | [<img src="./images/pa_calibration.png">](./functions/axes_map_calibration.md) |
|[`AXES_MAP_CALIBRATION`](./functions/axes_map_calibration.md)<br /><br />Überprüft, ob Ihr Beschleunigungssensor korrekt funktioniert und findet automatisch den `axes_map`-Parameter von Klipper | [<img src="./images/axesmap_example.png">](./functions/axes_map_calibration.md) |
|[`COMPARE_BELTS_RESPONSES`](./functions/compare_belts_responses.md)<br /><br />Erstellt ein Differenzdiagramm für Riemenresonanzen, um die relativen Riemenspannungen und das Verhalten der Riemenpfade bei einem CoreXY- oder CoreXZ-Drucker zu überprüfen | [<img src="./images/belts_example.png">](./macros/compare_belts_responses.md) |
|[`AXES_SHAPER_CALIBRATION`](./mfunctions/axes_shaper_calibrations.md)<br /><br />Erstellt die üblichen Eingabeshaper-Diagramme, um die Eingabeshaper-Filter von Klipper zu optimieren und Ringing/Ghosting zu reduzieren | [<img src="./images/axis_example.png">](./macros/axes_shaper_calibrations.md) |
|[`CREATE_VIBRATIONS_PROFILE`](./functions/create_vibrations_profile.md)<br /><br />Misst die globalen Maschinenvibrationen in Abhängigkeit von der Richtung und Geschwindigkeit des Werkzeugkopfes, um problematische Bereiche zu finden, in denen der Drucker stärkeren VFAs ausgesetzt sein könnte, um Ihre Slicer-Geschwindigkeitsprofile und TMC-Treiberparameter zu optimieren | [<img src="./images/vibrations_example.png">](./functions/create_vibrations_profile.md) |
|[`EXCITATE_AXIS_AT_FREQ`](./functions/excitate_axis_at_freq.md)<br /><br />Hält eine spezifische Erregungsfrequenz aufrecht, nützlich, um Parasitenpeaks zu untersuchen und herauszufinden, was resoniert | [<img src="./images/excitate_at_freq_example.png">](./macros/excitate_axis_at_freq.md) |

## Installation

  > **Deinstallation alter Versionen**:
  >
  > Lösche die `Calibrate.cfg` und wenn vorhanden, den `K-Shake-Tune Ordner` aus deinem Verzeichnis.
>    **Raspberry System**:
  > Folgende Bibliotheken und Anwendungen werden benötigt und normalerweise mithilfe des Installationsskriptes aktualisiert. Es kann jedoch passieren, dass während der Installation Fehler auftreten. Des Weiteren sollte das System mindestens Bullseye oder Bookworm sein. Buster ist veraltet und kann inkompatibilitäten auslösen.
  >
> GitPython==3.1.41
  >
> matplotlib==3.8.2
  >
> numpy==1.26.2
  >
> scipy==1.11.4
  >
> PyWavelets==1.6.0


  1. Kopiere die Makrodatei "calibrate.cfg" auf deinen Drucker.
  2. Stelle sicher, dass die Klipper-Erweiterung 'gcode_shell_command.py' installiert ist.
  Die einfachste Installationsmethode ist die Nutzung des erweiterten Bereichs von KIAUH.\
  [KIAUH installieren](https://www.obico.io/blog/install-klipper-with-kiauh/#install-kiauh-on-your-raspberry-pi)\
  Starte KIAUH -> 4) [Erweitert] -> 8) [G-Code Shell Befehl]
  3. Installiere die Shake-Tune-Python
     ```bash
     wget -O - https://raw.githubusercontent.com/Frix-x/klippain-shaketune/main/install.sh | bash
     ```
  5. Füge folgende Konfiguration in deine  `printer.cfg` ein:
     ```
     [shaketune]
      result_folder: ~/printer_data/config/ShakeTune_results
      #    Der Ordner, in dem die Ergebnisse gespeichert werden. Er wird erstellt, falls er nicht existiert.
      number_of_results_to_keep: 3
      #    Die Anzahl der Ergebnisse, die im result_folder behalten werden sollen. Die ältesten Ergebnisse werden
      #    nach jedem Lauf automatisch gelöscht.
      keep_raw_csv: False
      #    Wenn True, werden die rohen CSV-Dateien im result_folder zusammen mit den
      #    PNG-Grafiken behalten. Wenn False, werden sie gelöscht und nur die Grafiken werden behalten.
      show_macros_in_webui: True
      #    Mainsail und Fluidd erstellen keine Schaltflächen für "System"-Makros, die nicht in der
      #    printer.cfg-Datei sind. Wenn du die Makros in der WebUI sehen möchtest, setze dies auf True.
      timeout: 300
      #    Die maximale Zeit in Sekunden, die Shake&Tune zum Verarbeiten der CSV-Dateien und zum Erstellen der Grafiken lassen soll.
     ```
  6. Füge folgende Konfiguration in deine `moonraker.conf` ein:
     
    [update_manager Klippain-ShakeTune]
    type: git_repo
    origin: https://github.com/Frix-x/klippain-shaketune.git
    path: ~/klippain_shaketune
    virtualenv: ~/klippy-env
    requirements: requirements.txt
    system_dependencies: system-dependencies.json
    primary_branch: main
    managed_services: klipper

## Arbeitsablauf

Ein standardmäßiger Abstimmungsablauf könnte folgendermaßen aussehen:

```mermaid
%%{
  init: {
    'theme': 'base',
    'themeVariables': {
      'lineColor': '#232323',
      'primaryTextColor': '#F2055C',
      'secondaryColor': '#D3D3D3',
      'tertiaryColor': '#FFFFFF'
    }
  }
}%%

flowchart TB
    subgraph Abstimmungsablauf
    direction LR
    start([Start]) --> tensionBelts[Spanne deine\nRiemen so gut\nwie möglich]
    checkmotion --> tensionBelts
    tensionBelts --> SnT_Belts[Führe das Shake&Tune\nRiemenvergleichswerkzeug aus]
    SnT_Belts --> goodbelts{Überprüfe die Dokumentation\nSehen die Riemenvergleichsprofile\ngut aus?}
    goodbelts --> |JA| SnT_IS[Führe das Shake&Tune\nAchsen-Input-Shaper-Werkzeug aus]
    goodbelts --> |NEIN| checkmotion[Repariere deine mechanische Montage\nund dein Bewegungssystem]
    SnT_IS --> goodIS{Überprüfe die Dokumentation\nSehen die Achsenprofile und\nInput-Shaper gut aus?}
    goodIS --> |JA| SnT_Vibrations[Führe das Shake&Tune\nVibrationsprofil-Werkzeug aus]
    goodIS--> |NEIN| checkmotion
    SnT_Vibrations --> goodvibs{Überprüfe die Dokumentation\nSind die Diagramme in Ordnung?\nSetze die Geschwindigkeiten in\ndeinem Slicer-Profil}
    goodvibs --> |JA| pressureAdvance[Stimme dein\nDruckvorschub an]
    goodvibs --> |NEIN| checkTMC[Wenn du willst, beschäftige dich mit der\nAbstimmung der TMC-Treiber]
    goodvibs --> |NEIN| checkmotion
    checkTMC --> SnT_Vibrations
    pressureAdvance --> extrusionMultiplier[Stimme deinen\nExtrusionsmultiplikator an]
    extrusionMultiplier --> testPrint[Mache einen Testdruck]
    testPrint --> printGood[Ist der Druck gut?]
    printGood --> |JA| unicorn[Möchtest du Einhörner jagen]
    printGood --> |NEIN -> Unterextrusion / Überextrusion| extrusionMultiplier
    printGood --> |NEIN -> Eckschwankungen und kein Ghosting| pressureAdvance
    printGood --> |NEIN -> Sichtbare VFAs| SnT_Vibrations
    printGood --> |NEIN -> Ghosting, Ringing, Resonanz| SnT_IS
    unicorn --> |NEIN| done
    unicorn --> |JA| SnT_Belts
    end

    classDef standard fill:#70088C,stroke:#150140,stroke-width:4px,color:#ffffff;
    classDef questions fill:#FF8D32,stroke:#F24130,stroke-width:4px,color:#ffffff;
    classDef startstop fill:#F2055C,stroke:#150140,stroke-width:3px,color:#ffffff;
    class start,done startstop;
    class goodbelts,goodIS,goodvibs,printGood,unicorn questions;
    class tensionBelts,checkmotion,SnT_Belts,SnT_IS,SnT_Vibrations,pressureAdvance,extrusionMultiplier,testPrint,checkTMC standard;
```

## Ergänzende Ressourcen

  - [Beitrag von Sineos](https://klipper.discourse.group/t/interpreting-the-input-shaper-graphs/9879) in der Klipper-Wissensdatenbank
