# Einführung:

Die Makros ``MAX_VELOCITY_TEST``, ``MAX_ACCEL_TEST`` und ``BENCHMARK`` sind dafür entwickelt worden, um die maximale Beschleunigung und Geschwindigkeit des 3D-Druckers entlang der X- und Y-Achsen zu testen. Es führt eine Reihe von Testbewegungen bei verschiedenen Beschleunigungs- und Geschwindigkeitswerten aus, um die Leistung des Druckers bei unterschiedlichen Einstellungen zu untersuchen.

## ACHTUNG:
Dabei werden nur die physischen Eigenschaften getestet. Diese können bspw. für Travel-Bewegungen genutzt werden. Ob diese Werte seitens des Hotends realisiert werden können muss separat ermittelt werden. Für dieses Makro muss derzeitig die stepper.py ausgetauscht werden. Dies kann dafür sorgen, dass KIAUH nicht mehr updaten kann. Vor einem Update ist ggf. die originale Stepper.py einzuspielen und nach dem Update wieder auszutauschen. Wir sind derzeitig dabei, diese Funktion in das Standardklipper zu integrieren, damit dieser Schritt entfallen kann.


# Vorbereitung:

- Stelle sicher, dass der 3D-Drucker korrekt eingerichtet und alle Achsen frei beweglich sind
- Kopiere die speed_test.cfg in deine Config und wähle innerhalb der Datei dein Drucktyp aus (cartesian oder corexy).
- Füge ``[include speed_test.cfg]`` und ``[respond]`` in deiner printer.cfg ein
- Sichere dir im Ordner ``home/pi/klipper/klippy/`` die ``Stepper.py`` als Backup.
- Überschreiben die Klipper Datei  ``stepper.py``mit diesem Befehl (putty).
- ``curl 'https://raw.githubusercontent.com/cryd-s/klipper_scripts/main/speed_accel_script/stepper.py' > ~/klipper/klippy/stepper.py`` und starte danach Klipper über diesen Befehl neu```sudo systemctl restart klipper```

# Erläuterung zu den Makros:

## ACHTUNG -- Die Makros lassen sich nur über den notaus stoppen

Das ``MAX_VELOCITY_TEST`` ist dazu gedacht, die maximale Geschwindigkeit eines 3D-Druckers entlang der X- und Y-Achse zu testen. Es führt eine Reihe von Testbewegungen bei verschiedenen Geschwindigkeiten aus und ermöglicht es , die Leistung des Druckers bei unterschiedlichen Geschwindigkeiten zu untersuchen.

Das Makro verwendet mehrere Parameter, um den Test anzupassen:
- MIN_VELOCITY: Die minimale Geschwindigkeit, mit der der Test beginnen soll (Standardwert: 10 mm/s).
- MAX_VELOCITY: Die maximale Geschwindigkeit, mit der der Test durchgeführt werden soll (Standardwert: 300 mm/s).
- VELOCITY_INCREMENT: Die Erhöhung der Geschwindigkeit für jeden Schritt des Tests (Standardwert: 10 mm/s).
- AXIS: Die Achse, entlang der der Test durchgeführt werden soll (Standardwert: "X").
- ACCEL: Der Beschleunigungswert, der während des Tests verwendet werden soll (Standardwert: die maximale Beschleunigungseinstellung des Druckers).
- DISTANCE: Gibt die Entfernung für die Testbewegungen an, entweder "full" (Standardwert) oder "short". Wenn es auf "full" gesetzt ist, wird das Makro die maximal verfügbare Entfernung auf der Achse verwenden; wenn es auf "short" gesetzt ist, wird es eine zufällige Entfernung für jede Testbewegung verwenden, die sicherstellt, dass die angegebene Geschwindigkeit erreicht wird.
- REPEAT: Anzahl der Hin- und Rückfahrten, die bei jedem Geschwindigkeitsschritt durchgeführt werden sollen (Standardwert: 5 für "DISTANCE=full", 50 für "short").

Das ``MAX_ACCEL_TEST`` G-Code-Makro wurde entwickelt, um die maximale Beschleunigung eines 3D-Druckers entlang der X- und Y-Achsen zu testen.
Es führt eine Reihe von Testbewegungen mit verschiedenen Beschleunigungswerten durch, sodass  die Leistung des Druckers bei unterschiedlichen Beschleunigungsraten untersucht werden kann.

Das Makro verwendet mehrere Parameter, um den Test anzupassen:
- MIN_ACCEL: Die minimale Beschleunigung, bei der der Test beginnen soll (Standard: 100 mm/s^2).
- MAX_ACCEL: Die maximale Beschleunigung, bei der der Test durchgeführt werden soll (Standard: 1000 mm/s^2).
- ACCEL_INCREMENT: Der Zuwachs in der Beschleunigung für jeden Schritt des Tests (Standard: 100 mm/s^2).
- AXIS: Die Achse, entlang der der Test durchgeführt werden soll (Standard: "X").
- SPEED: Die Geschwindigkeit, die während des Tests verwendet werden soll (Standard: die maximale Geschwindigkeitseinstellung des Druckers).
- REPEAT: Anzahl der Hin- und Rückfahrten, die bei jedem Geschwindigkeitsschritt durchgeführt werden sollen (50).

Das ``BENCHMARK`` Makro ist dafür gedacht, das Leistungsverhalten eines 3D-Druckers anhand einer Reihe von Kurz- und Langbewegungen zu testen. Das Makro erlaubt es, eine umfangreiche Analyse des Druckers hinsichtlich verschiedener Beschleunigungs- und Geschwindigkeitsparameter durchzuführen.

Das Makro verwendet folgende Parameter, um den Test anzupassen:
MAX_ACCEL: Die maximale Beschleunigung, mit der der Test durchgeführt werden soll (Standardwert: die maximale Beschleunigungseinstellung des Druckers).
MAX_VELOCITY: Die maximale Geschwindigkeit, mit der der Test durchgeführt werden soll (Standardwert: die maximale Geschwindigkeitseinstellung des Druckers).
MOVEMENTS_SHORT: Anzahl der kurzen Bewegungen, die durchgeführt werden sollen (Standardwert: 200).
MOVEMENTS_LONG: Anzahl der langen Bewegungen, die durchgeführt werden sollen (Standardwert: 200).
RANDOM_SEED: Ein Seed-Wert für die Zufallszahlengenerierung, um die Position der Bewegungen festzulegen (Standardwert: 42).
Das Makro beginnt mit dem Homing der Achsen und setzt die Z-Achse auf 20. Danach stellt es die Beschleunigungs- und Geschwindigkeitsgrenzen des Druckers entsprechend den gegebenen oder Standardparametern ein.

Danach führt das Makro die festgelegte Anzahl an kurzen Bewegungen aus. Für jede Bewegung berechnet das Makro eine Zufallsposition entlang der X- und Y-Achse, wobei es sicherstellt, dass die Bewegung innerhalb des gültigen Bereichs der jeweiligen Achse bleibt. Es verwendet den gegebenen oder Standardseed für die Zufallsgenerierung, um die Positionen festzulegen. Ähnlich führt das Makro danach die festgelegte Anzahl an langen Bewegungen aus, wieder mit zufällig berechneten Positionen basierend auf dem Seed-Wert. Am Ende des Tests führt das Makro erneut ein Homing der X- und Y-Achsen durch und setzt die Geschwindigkeits- und Beschleunigungsgrenzen des Druckers auf ihre ursprünglichen Werte zurück. Dieses Makro ist sehr nützlich, um das Verhalten des Druckers unter verschiedenen Bedingungen zu analysieren und zu optimieren, insbesondere im Hinblick auf Geschwindigkeits- und Beschleunigungseinstellungen. Es kann auch dazu verwendet werden, um mögliche Probleme mit der Drucker-Hardware zu identifizieren, indem man das Verhalten des Druckers bei unterschiedlichen Geschwindigkeits- und Beschleunigungsparametern überwacht.


# Empfohlener Testablauf
1. Maximale Geschwindigkeit
Das Makro MAX_VELOCITY_TEST sollte auf der X-Achse ausgeführt werden. Dabei sollte sich schrittweise an die Maximalgeschwindigkeit herangetastet werden. Die full Distanz sollte genutzt werden. Bei "ungünstigen" Geräuschen des Druckers sollte das Makro durch Betätigen des Not-Aus gestoppt werden.
!!Achtung!! Trotz hoher Geräusche kann es passieren, dass es zu keinem Schrittverlust kommt. Hier sollte neben der technischen Erkennung im Zweifel nach Gehör agiert werden
Die Geschwindigkeit, welche keine Geräusche produziert, sollte über die short Distanz validiert werden. Dieser Test sollte dann analog auf der Y-Achse ausgeführt werden. Dabei ist zu beachten, dass Bedslinger Drucker auf der Y-Achse eine geringere Geschwindigkeit leisten können. Die ermittelte Maximalgeschwindigkeit sollte aus Sicherheitsgründen und zur Materialschonung um 20% reduziert werden.

3. Maximale Beschleunigung
Das Makro MAX_ACCEL_TEST sollte auf der X-Achse ausgeführt werden. Dabei sollte sich schrittweise an die Maximalbeschleunigung herangetastet werden. Dieses Makro hat eine automatische Selbstabschaltung bei Schrittverlust. Trotzdem sollte der Drucker bei "ungünstigen" Geräuschen durch Betätigen des Not-Aus gestoppt werden. Die Beschleunigung, welche keine Geräusche produziert, sollte über einen REPEAT von 200 validiert werden. Dieser Test sollte dann analog auf der Y-Achse ausgeführt werden. Dabei ist zu beachten, dass Bedslinger Drucker auf der Y-Achse eine geringere Beschleunigung leisten können. Die ermittelte Maximalbeschleunigung sollte aus Sicherheitsgründen und zur Materialschonung um 30% reduziert werden.

4. Abschließende Schritte:
Die Werte sollten mit dem Makro BENCHMARK validiert werden. Die maximalen Beschleunigungs- und Geschwindigkeitswerte, die der Drucker ohne Schrittverlust oder Vibrationen erreichen konnte, sollten notiert werden. Die Einstellungen des Druckers sollten entsprechend aktualisiert und die Änderungen gespeichert werden. Abschließende Testdrucke sollten durchgeführt werden, um die Druckqualität und Leistung bei den ermittelten maximalen Werten zu überprüfen.
