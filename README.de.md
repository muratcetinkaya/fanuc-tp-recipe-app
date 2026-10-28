# FANUC Rezeptverwaltung (KAREL + XML + Web)

🌐 [English](README.md) | [Türkçe](README.tr.md) | **Deutsch**

Ein minimales, allgemeines Beispiel für **Rezeptspeicherung und -verwaltung auf einer FANUC-Robotersteuerung**.
Es werden nur integrierte Funktionen verwendet: KAREL, XML-Dateien auf der Steuerung (FR:), Register und eine Webserver-Seite (iPendant / Browser).

Ein Rezept besteht hier nur aus **Name + 3 Zahlen** (Kartonlänge, -breite, -höhe) – beliebig erweiterbar.

## Funktionsweise

```
 Webseite (recipe_hmi.stm)
   |  Combobox     -> SR[1]   (gewählte Datei, z. B. "box_small.xml")
   |  Eingabefelder -> SR[2], R[11..13]   (EDIT-Seite)
   v
 recipe_save.kl   SAVE / CREATE / DELETE   -> FR:\recipes\<Name>.xml
 recipe_load.kl   VIEW (-> Edit-Register)  /  LOAD (-> aktive Register SR[3], R[1..3])
```

| Schaltfläche | Programm | Funktion |
|---|---|---|
| New Recipe | recipe_save | leert die Eingabefelder |
| Save Recipe | recipe_save | schreibt SR[2] + R[11..13] in eine XML-Datei |
| View Recipe | recipe_load | liest die gewählte XML in die Eingabefelder |
| Load Recipe | recipe_load | liest die gewählte XML in die **aktiven** Register, die das Roboterprogramm nutzt |
| Delete Recipe | recipe_save | löscht die gewählte XML-Datei |

Durch die Trennung von Edit und Aktiv kann man Rezepte ansehen und ändern, während der Roboter weiter mit dem geladenen Rezept arbeitet.

## Registerbelegung

| Register | Bedeutung |
|---|---|
| SR[1] | gewählte Datei (Combobox) |
| SR[2] / SR[3] | Rezeptname – Edit / aktiv |
| R[11..13] | Länge, Breite, Höhe – Edit |
| R[1..3] | Länge, Breite, Höhe – aktiv (diese im TP-/KAREL-Programm verwenden) |

## Einrichtung
1. **Zuerst KAREL aktivieren: in den Systemvariablen `$KAREL_ENB` von `0` auf `1` setzen.** Ohne dies können KAREL-Programme nicht ausgeführt werden.
2. `recipe_save.kl` und `recipe_load.kl` übersetzen (ROBOGUIDE oder `ktrans`) und die `.pc`-Dateien auf die Steuerung laden.
3. Die Webseite `recipe_hmi.stm` auf die Steuerung kopieren. **FANUC-Webseiten müssen die Endung `.stm` haben.**
4. **Das Verzeichnis der Seite in der Systemvariable `$TX_SCREEN` eintragen.** Das Gerät/Verzeichnis, in dem `recipe_hmi.stm` liegt, muss in `$TX_SCREEN` definiert sein, sonst findet der Webserver der Steuerung die Seite nicht.
5. Den Ordner `FR:\recipes\` anlegen (optional `example/box_small.xml` hineinkopieren).
6. Den Aufruf der KAREL-Programme über das Web erlauben (Setup > Host Comm / HTTP-Authentifizierung, KAREL-CGI aktiviert).
7. Die Seite lässt sich auf zwei Arten öffnen:
   - **Am Bedienhandgerät:** im iPendant das Menü **Browser** öffnen und die Seite über den in `$TX_SCREEN` vergebenen **Namen** auswählen.
   - **Im PC-Browser:** über das in `$TX_SCREEN` konfigurierte Verzeichnis: `http://<Roboter-IP>/<konfiguriertes-Verzeichnis>/recipe_hmi.stm`

## Hinweise
- Werte werden als Integer (mm) gespeichert. Für Dezimalstellen mit x10 oder x100 speichern.
- `TPOUT[5] = FALSE` dient als „Roboter beschäftigt“-Verriegelung, damit Rezepte nicht mitten im Zyklus geändert werden – an das eigene Signal anpassen.
- Die XML wird Tag für Tag mit `XML_ADDTAG` / `XML_SCAN` / `XML_GETDATA` gelesen.

## Autor
**Muratcan Cetinkaya**
E-Mail: mcetinkaya40@gmail.com
Web: https://www.muratcancetinkaya.net
