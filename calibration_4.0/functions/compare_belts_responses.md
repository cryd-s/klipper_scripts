Übersetzt von: https://github.com/Frix-x/klippain-shaketune/blob/main/docs/macros/compare_belts_responses.md

# Messung der relativen Unterschiede der Riemen

Das Makro `COMPARE_BELTS_RESPONSES` ist speziell für CoreXY- oder CoreXZ-Maschinen konzipiert, wo es dir helfen kann, Probleme mit dem Riemenweg zu diagnostizieren, indem es die Unterschiede in ihrem Verhalten misst und darstellt. Es hilft dir auch dabei, deine Riemen gleichmäßig zu spannen.

  > **Hinweis**:
  >
  > Obwohl es verlockend sein könnte, es auch bei anderen Druckertypen, wie z.B. Kartesischen Druckern, zu verwenden, ist das wahrscheinlich keine gute Idee. Es ist normal, dass in diesem Fall unterschiedliche Reaktionen auftreten, da die Riemenwege nicht symmetrisch sind.

## Verwendung

**Bevor du beginnst, stelle sicher, dass die Riemen richtig gespannt sind**. Du kannst beispielsweise die [Voron-Riemen-Spannungsanleitung](https://docs.vorondesign.com/tuning/secondary_printer_tuning.html#belt-tension) befolgen. Du musst eine solide Grundlage haben, um darauf aufzubauen!

Rufe dann das Makro `COMPARE_BELTS_RESPONSES` auf und suche im Ergebnisordner nach den Diagrammen. Hier sind die verfügbaren Parameter:

| Parameter | Standardwert | Beschreibung |
|-----------:|---------------|-------------|
|FREQ_START|Keiner (Standardwert aus `[resonance_tester]`)|Startfrequenz der Anregung|
|FREQ_END|Keiner (Standardwert aus `[resonance_tester]`)|Maximale Anregungsfrequenz|
|HZ_PER_SEC|1|Anzahl der Hz pro Sekunde für den Test|
|ACCEL_PER_HZ|Keiner (Standardwert aus `[resonance_tester]`)|Beschleunigung pro Hz, die für den Test verwendet wird|
|TRAVEL_SPEED|120|Geschwindigkeit in mm/s, die für alle Reisebewegungen verwendet wird (um zur Startposition vor dem Test zu gelangen)|
|Z_HEIGHT|Keiner|Z-Höhe, die für den Test gewünscht wird. Dieser Wert kann verwendet werden, um bei Bedarf den Z-Wert des probe_point in deinem `[resonance_tester]` Konfigurationsabschnitt zu überschreiben|

![](../images/belts_example.png)

### Frequenzprofile der Riemen

Bei diesen Diagrammen **möchtest du, dass beide Kurven ähnlich aussehen und sich zu einer einzigen Kurve überlappen**: Versuche, sie in Frequenz **und** Amplitude so genau wie möglich aneinander anzupassen. Normalerweise besteht ein Riemen-Diagramm aus einem oder zwei Hauptpaaren von Peaks (mehr als 2 Peaks können auf mechanische Probleme hinweisen). Es ist akzeptabel, "Rauschen" um die Hauptpeaks herum zu haben, aber es sollte auf beiden Kurven mit vergleichbarer Amplitude vorhanden sein. Beachte, dass beim Spannen eines Riemens seine Peaks diagonal nach oben rechts wandern sollten, sich signifikant in der Amplitude und leicht in der Frequenz ändern. Außerdem sollte die Größenordnung der Hauptpeaks *typischerweise* bei den meisten Maschinen zwischen ~500k und ~2M liegen.

Neben der eigentlichen Riemen-Spannung hängt die Resonanzfrequenz/-amplitude der Kurven hauptsächlich von drei Parametern ab:
  - die *Masse des Werkzeugkopfs*, die bei CoreXY, CrossXY und H-Bot Maschinen für beide Riemen identisch ist. Dies wird hier also wahrscheinlich keinen Effekt haben
  - die *Elastizität des Riemens*, die sich mit der Zeit ändert, wenn der Riemen abgenutzt wird. Stelle sicher, dass du **dieselbe Riemenmarke und denselben Typ** für die Riemen A und B verwendest und dass sie **zur gleichen Zeit installiert wurden**: Du möchtest ähnliche Riemen mit einem ähnlichen Abnutzungsgrad!
  - die *Länge des Riemenwegs*, weshalb sie **genau dieselbe Anzahl an Zähnen** haben müssen, damit ein Riemenweg beim Spannen mit der gleichen Spannung nicht länger als der andere ist. Dieser spezielle Punkt ist sehr wichtig: Ein einziger Zahnunterschied reicht aus, um eine gute Überlagerung der Kurven zu verhindern. Außerdem ist dies sogar eine der Hauptursachen für Probleme, die in Discord-Resonanztestkanälen gefunden werden.

**Wenn diese drei Parameter erfüllt sind, gibt es keinen Grund, dass die Kurven unterschiedlich sein könnten**, oder du kannst sicher sein, dass es mindestens ein Problem mit einem der Riemenwege gibt. Außerdem, wenn die Riemen-Diagramme Kurven mit niedriger Amplitude und/oder viel Rauschen haben, wirst du wahrscheinlich auch schlechte Eingabeshaper-Diagramme haben. Bevor du also weitermachst, stelle sicher, dass du gute Riemen-Diagramme hast, indem du zuerst deine mechanischen Probleme behebst.

### Vergleichsdiagramm der Kreuzriemen

Das Kreuzriemen-Diagramm ist eine innovative, coole Methode, um die Frequenzprofile der Riemen an jedem Frequenzpunkt zu vergleichen. In diesem Diagramm markiert jeder Punkt die Amplitudenantwort jedes Riemens bei verschiedenen Frequenzen, verbunden Punkt für Punkt, um das Frequenzspektrum nachzuzeichnen. Idealerweise sollten diese Punkte auf der diagonalen Mittellinie liegen, was darauf hinweist, dass beide Riemen bei jeder Frequenz übereinstimmende Energieantwortwerte haben.

Die gute Zone, breiter am unteren Ende (Niederamplitudenbereiche, wo die Abweichung nicht viel ausmacht) und enger am oberen rechten Ende (Hochenergiebereich, wo die Hauptpeaks liegen), repräsentiert akzeptable Abweichungen. Also **möchtest du, dass alle Punkte nahe der idealen Mittellinie liegen und möglichst viele innerhalb der grünen Zone**, da dies bedeutet, dass die Riemen gut abgestimmt sind und sich ähnlich verhalten.

Gepaarte Peaks bei derselben Frequenz werden am selben Punkt (beschriftet mit α1/α2, β1/β2, ...) liegen, und der Abstand von der Mittellinie zeigt den Unterschied in der Energie. Für gepaarte Peaks, die auch einen Frequenzunterschied haben, werden sie als zwei Punkte dargestellt (beschriftet als α1 und α2, ...) und der zusätzliche Abstand zwischen ihnen entlang der gezeichneten Linie stellt ihren Frequenzunterschied dar.

### Geschätzte Ähnlichkeit und Indikator für mechanische Probleme

  1. **Die geschätzte Ähnlichkeit** liefert eine quantitative Ansicht darüber, wie ähnlich die Frequenzprofile der beiden Riemen über ihren gesamten Bereich sind. Ein Ähnlichkeitswert nahe 100 % bedeutet, dass die Riemen gut abgestimmt sind und ein gleichmäßiges mechanisches Verhalten aufweisen.
  2. **Der Indikator für mechanische Gesundheit** bietet eine weitere Bewertung des Betriebszustands des Druckers basierend auf der geschätzten Ähnlichkeit und wird durch die Anzahl der gepaarten und ungepaarten Peaks beeinflusst. Ein verrauschtes Signal senkt in der Regel den Wert dieses Indikators und weist auf mögliche Probleme hin. Diese Messung kann jedoch manchmal irreführend sein, daher ist es wichtig, sich nicht allein darauf zu verlassen und sie in Verbindung mit den anderen angezeigten Informationen zu betrachten.

  > **Hinweis**:
  >
  > Wenn du dieses Werkzeug verwendest, um die Spannung nach der Installation neuer Riemen zu überprüfen oder anzupassen, solltest du nach einigen Stunden Drucken erneut messen. Denn die Spannung kann sich leicht ändern, wenn sich die Riemen dehnen und ihre endgültige Spannung einnehmen. Aber keine Sorge, ein paar Stunden Drucken sollten mehr als ausreichend sein!
