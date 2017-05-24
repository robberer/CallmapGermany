Eine Deutschland-Karte der Funkamateure basierend auf der Rufzeichenliste der Bundesnetzagentur

von [Ulrich Thiel, VK2UTL/DK1UT](mailto:u-thiel@gmx.net)

---

Neben **sehr vielen positiven** Kommentaren gab es vereinzelt Beschwerden bezüglich des Datenschutz. Da ich anderweitig relativ beschäftigt bin und auf die Diskussion keinen Wert lege,  habe ich mich entschlossen, die Karte ganz zu entfernen. Ich möchte betonen, dass es nie einen Download irgendwelcher Daten gab und dass die Daten 1:1 aus der öffentlichen PDF-Datei der Bundesnetzagentur stammten. Die Idee sollte jetzt aber klar sein und mit der Anleitung unten kann sich aber jeder selbst eine vollständige Datenbank und Karte erstellen.  


### Vorgehen

Bei der Rufzeichenliste der Bundesnetzagentur handelt es sich um eine PDF-Datei. Diese habe ich zunächst mit dem Linux-Tool ```ps2ascii``` in eine Text-Datei umgewandelt: 

```
ps2ascii Rufzeichenliste_AFU.pdf > calls.txt
``` 

Mit meinem Python-Skript ```makedb.py``` habe ich diese Text-Datei nun verarbeitet und aus den Daten eine SQL-Datenbank erstellt. Dies ist natürlich der schwierigste Teil. Erschwerend kommt hinzu, dass einige Rufzeichen mehrere Adressangaben haben und, dass Adress-Formatierungen teilweise schlicht fehlerhaft sind. Mein Skript sollte beide Fälle einigermaßen gut behandeln.   
Als nächstes habe ich mit meinem Skript ```makegeo.py``` geographische Koordinaten für die Adressen bestimmt (das nennt man *geocoding*). Für Python gibt es eine eigene Bibliothek namens geocoder, die direkten Zugriff auf die Google-API ermöglicht. Einziger Haken hier ist ein täglicher Limit von 2,500 Abfragen. Das könnte man "umgehen", wenn man Zugriff auf mehrere Computer hat. Bei mir sind schließlich ca. 500 Adressen übrig geblieben, für die ein Geocoding nicht erfolgreich war. Zum einen handelt es sich hierbei schlicht um Schreibfehler, die ich in dieser Menge nicht korrigieren wollte. Zum anderen scheinen es veraltete Adressen zu sein (z.B. "...weg" anstatt "...straße"). Nun habe ich mit meinem Python-Skript ```makecsv.py``` aus den Daten der SQL-Datenbank eine CSV-Datei erstellt. Diese Datei habe ich schließlich in eine [Google Fusion Table](usiontables.google.com) geladen, von wo aus sich die Daten sofort visualisieren lassen. 

*Alternative zum Geocoden:* Man könnte das Skript ```makecsv.py``` so modifizieren, dass die Adressen ebenfalls in der CSV-Datei stehen und dann kann man diese Adresse direkt in der Google Fusion Table geocoden, man braucht also genau genommen ```makegeo.py``` nicht; es ist aber komfortabler.

### Bugs & Zu erledigen
Ich werde wegen Zeitmangel nicht mehr wesentlich weiter an der Karte arbeiten, Beiträge (vor allem Bugfixes) sind aber sehr gerne willkommen. 


1. Bei einigen Records wird das Geocoding fehlgeschlagen sein und irgendwo lokalisiert worden sein, ohne dass dies als fehlerhaftes Geocoding erkannt wurde. Das sollten aber nicht mehr als 50 Fälle sein. Man könnte das vielleicht durch zusätzlichen Vergleich mit OSM-Geocoding erkennen.
2. Man müsste nun ein weiteres Skript erstellen, dass die Datenbank aktualisiert, d.h. nicht mehr vorhandene Einträge erkennt und entfernt, neue hinzufügt. Das sollte mit dem SQL-Backend nicht zu schwer sein.