# Kalibrierung des "axes_map" des Beschleunigungssensors

Alle von Shake&Tune generierten Grafiken zeigen Diagramme basierend auf Messungen des Beschleunigungssensors, die typischerweise mit den Achsen X, Y und Z gekennzeichnet sind. Wenn der Beschleunigungssensor gedreht ist, können seine Achsen nicht korrekt mit den Maschinenachsen ausgerichtet sein, was die Diagramme schwerer zu interpretieren, zu analysieren und zu verstehen macht. Das Makro `AXES_MAP_CALIBRATION` ist darauf ausgelegt, die Ausrichtung des Beschleunigungssensors automatisch zu messen, um sie korrekt einzustellen, was es einfacher denn je macht, das Beste aus Ihren Daten herauszuholen!

  > **Hinweis**:
  >
  > Diese Fehlausrichtung beeinträchtigt nicht die Genauigkeit der Messungen, da die Gesamtsumme über alle Achsen in den meisten Shake&Tune-Tools verwendet wird. Es ist nur eine optionale, aber praktische Möglichkeit, den "axes_map"-Parameter von Klippers `[adxl345]` (oder welchen Beschleunigungssensor Sie auch haben) zu konfigurieren.

## Verwendung

Rufen Sie das Makro `AXES_MAP_CALIBRATION` auf und suchen Sie im Ergebnisordner nach den Diagrammen. Hier sind die verfügbaren Parameter:

| Parameter | Standardwert | Beschreibung |
|-----------:|---------------|-------------|
|Z_HEIGHT|20|Z-Höhe, um den Werkzeugkopf vor Beginn der Bewegungen zu positionieren. Seien Sie vorsichtig, wenn Ihr Beschleunigungssensor unter der Düse montiert ist, erhöhen Sie ihn, um ein Aufprallen auf das Bett der Maschine zu vermeiden|
|SPEED|80|Geschwindigkeit des Werkzeugkopfs in mm/s für die Bewegungen|
|ACCEL|1500 (oder max. Druckerbeschleunigung)|Beschleunigung in mm/s², die für alle Bewegungen verwendet wird|
|TRAVEL_SPEED|120|Geschwindigkeit in mm/s, die für alle Reisebewegungen verwendet wird|

  > **Hinweis**:
  >
  > Dieser Befehl funktioniert nur, wenn Sie denselben Beschleunigungssensor in alle 3 Richtungen bewegen können, wie bei einem Voron V2.4 Drucker. Wenn Sie 2 Beschleunigungssensoren an Ihrer Maschine haben, wie bei einem Prusa, Switchwire oder Ender3, funktioniert es nicht, weil es unmöglich ist, die Orientierung des Beschleunigungssensors mit nur einer Bewegung zu erkennen (wie für das Bett).

![](../images/axesmap_example.png)

Während der Messung bewegt sich die Maschine leicht in +X, +Y und +Z. Dies ermöglicht es, die Orientierung des Beschleunigungssensors automatisch zu erkennen.

Verwenden Sie diesen Wert in Ihrer `printer.cfg` Konfigurationsdatei:
```
[adxl345] # ersetzen Sie "adxl345" durch den Namen Ihres korrekten Beschleunigungssensors
axes_map: -z,y,x
```

### Beschleunigungsdiagramm

Dieses Diagramm zeigt die Beschleunigungsdaten über die Zeit für die Achsen X, Y und Z an, nachdem der Gravitationsversatz entfernt wurde. Suchen Sie nach Mustern in den Beschleunigungsdaten für jede Achse: Sie sollten genau 2 Spitzen für jedes Unterdiagramm haben (für den Start und Stopp der Bewegung), die sich vom globalen Rauschen abheben. Dies kann helfen, Anomalien oder Inkonsistenzen im Verhalten Ihres Beschleunigungssensors zu identifizieren.

Das dynamische Rauschen und die Hintergrundvibrationen, die vom Beschleunigungssensor gemessen werden, werden aus dem Signal extrahiert (unter Verwendung der Wavelet-Transformation-Zerlegung) und in der Legende gedruckt. **Normalerweise sind Werte unter etwa 500mm/s² in Ordnung**, aber Shake&Tune wird automatisch eine Anmerkung hinzufügen, wenn zu viel Rauschen aufgezeichnet wird. **Seien Sie vorsichtig, da dieser Wert sehr unterschiedlich ist zum Klippers `MEASURE_AXES_NOISE`-Befehl, da Shake&Tune alles während der Bewegung misst**, wie Beschleunigungssensorrauschen, aber auch Vibrationen und Motorengeräusche, Achsen- und Werkzeugkopfoszillationen usw. Wenn Sie Ihren axes_map korrekt aufzeichnen möchten, müssen Sie möglicherweise etwa das 10-fache dieses Werts im `ACCEL`-Parameter verwenden, um ein gutes Signal-Rausch-Verhältnis zu erhalten und Shake&Tune die Beschleunigungs- und Verzögerungsphasen des Werkzeugkopfs korrekt erkennen zu lassen.

Der erkannte Gravitationsversatz wird in der Legende gedruckt, um etwas Kontext zu den Messwerten und deren Skala zu geben: Wenn er zu weit vom Standard 9.8-10 m/s² entfernt ist, bedeutet dies, dass Ihr Beschleunigungssensor nicht richtig funktioniert und repariert oder kalibriert werden sollte.

### Geschätzter 3D-Bewegungspfad

Dieses Diagramm visualisiert den geschätzten Pfad des Werkzeugkopfs, wie er vom Beschleunigungssensor im 3D-Raum aufgezeichnet wurde. Bedenken Sie, dass, obwohl Shake&Tune einige mathematische Tricks verwendet, um etwas so genau wie möglich zu erhalten, wir kein Gyroskop haben, um den Beschleunigungsdrift zu kompensieren, und dieses Diagramm immer noch weitgehend eine "Schätzung" ist.

Beim Betrachten sollten Sie auf die Konsistenz des Pfads achten, indem Sie die Gleichmäßigkeit der Pfade (orangefarbene gepunktete Linien) überprüfen: Sie sollten größtenteils linear sein. Ideal sollten die berechneten Richtungsvektoren (in Lila) entlang einer der primären Achsen (X, Y oder Z) ausgerichtet erscheinen, mit minimalem Winkelabweichungsfehler, was auf eine genaue Ausrichtung des Beschleunigungssensorchips mit der Maschinenachse hinweist.

Bedenken Sie, dass diese Grafik eine Schätzung ist und es zwischen aufeinanderfolgenden Durchläufen Variationen geben kann, insbesondere bei den berechneten Winkeln. Zum Beispiel hatte ich auf meiner Maschine diese Ergebnisse über 20 aufeinanderfolgende Durchläufe (mittlerer quadratischer Fehler etwa 3 bis 5 Grad):

![](../images/axes_map_inaccuracy.png)
