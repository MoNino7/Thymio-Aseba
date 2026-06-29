# Thymio II - Aseba Robotics Behaviors

## Über Thymio und Aseba
Der **Thymio II** ist ein mobiler Roboter, der mit einer Vielzahl von Sensoren (Infrarot-Näherungssensoren, Bodensensoren, Beschleunigungsmesser, Mikrofon) und Aktuatoren (Motoren, LEDs, Lautsprecher) ausgestattet ist. 

Programmiert wird der Roboter mit **Aseba**, einer ereignisbasierten (event-driven) Skriptsprache, die speziell für Mikrocontroller entwickelt wurde. Aseba führt Codeblöcke reaktiv aus, sobald bestimmte Ereignisse eintreten (z. B. `onevent prox` für Sensor-Updates oder `onevent timer0` für zeitgesteuerte Abläufe). Da Aseba keine Fließkommazahlen (Floats) oder pausierende Befehle (wie `sleep`) unterstützt, erfordert die Programmierung den gezielten Einsatz von Zustandsautomaten (State Machines) und Integer-Mathematik.

---

## Projektübersicht & Problemstellungen
Dieses Repository enthält drei Kernskripte, die verschiedene Konzepte der mobilen Robotik demonstrieren.

### 1. Kalibrierung und Odometrie (`gerade_fahren.aesl`)
**Das Problem:** 
Der Roboter erhält Befehle für die Motorgeschwindigkeit als abstrakte Zielwerte (`motor.target`), die nicht direkt physikalischen Einheiten entsprechen. Zudem muss die zurückgelegte Strecke (Odometrie) ausschließlich mit Integer-Werten berechnet werden.

**Die Lösung:** 
Durch manuelle Kalibrierung wurde der lineare Zusammenhang zwischen Zielwert und echter Geschwindigkeit ermittelt. Ein Timer-Event ruft alle 100 ms die tatsächliche Umdrehungsgeschwindigkeit (`motor.speed`) der Räder ab und akkumuliert die gefahrene Distanz in Millimetern.

### 2. Linienverfolgung mit Hindernisvermeidung (`obstacle_avoidance.aesl`)
**Das Problem:** 
Der Roboter soll auf einer Linie (weißes Papier auf dunklem Grund) zentriert fahren und plötzlichen Hindernissen ausweichen, ohne diese frontal zu rammen. Nach dem Ausweichen muss er das Hindernis umrunden und selbstständig auf die Strecke zurückfinden – eine Herausforderung, da der Thymio keine echten seitlichen Sensoren besitzt.

**Die Lösung:** 
*   **P-Regler:** Für das mittige Fahren wird die Differenz der Bodensensoren berechnet und als proportionaler Korrekturwert fließend auf die Motoren angewendet.
*   **State Machine & Wall-Hugging:** Erkennen die Frontsensoren ein Hindernis, stoppt die Vorwärtsfahrt und der Roboter rotiert auf der Stelle. Ein "Gedächtnis" (`memory`-Variable) speichert, auf welcher Seite das Objekt zuletzt detektiert wurde. Durch kontinuierliches Pendeln in die gespeicherte Richtung tastet der Roboter sich an der Wand des Hindernisses entlang, bis die Bodensensoren die Linie wiedererkennen.

### 3. Thymio als interaktives Instrument (`musik_theremin.aesl`)
**Das Problem:** 
Die horizontalen Infrarotsensoren sollen als kontaktloses Musikinstrument genutzt werden, bei dem jeder Sensor eine andere Note anschlägt.

**Die Lösung:** 
Die Sensorwerte werden auf spezifische Audio-Frequenzen (`sound.freq`) einer C-Dur-Tonleiter gemappt. Eine Status-Variable (`active_key`) sorgt für das nötige Debouncing, sodass Töne nicht flackern oder mehrfach angeschlagen werden, wenn sich die Hand im Erfassungsbereich befindet. Jeder Ton ist synchron mit einer eigenen LED-Farbcodierung gekoppelt.

---

## Demonstration

Hier ist ein kurzes Video, das das Verhalten des Roboters in Aktion zeigt:

<video src="demo_tymio.mp4" controls="controls" style="max-width: 100%;">
  Dein Browser unterstützt das Video-Tag nicht. 
  <a href="demo_tymio.mp4">Lade das Video hier herunter</a>.
</video>
