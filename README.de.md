# FANUC Rezeptverwaltung (KAREL + XML + Web)

🌐 [English](README.md) | [Türkçe](README.tr.md) | **Deutsch**

Ein minimales, allgemeines Beispiel für **Rezeptspeicherung und -verwaltung auf einer FANUC-Robotersteuerung**.
Es werden nur integrierte Funktionen verwendet: KAREL, XML-Dateien auf der Steuerung (FR:), Register und eine Webserver-Seite (iPendant / Browser).

Ein Rezept besteht hier nur aus **Name + 3 Zahlen** (Kartonlänge, -breite, -höhe) – beliebig erweiterbar.

**Getestet auf:** FANUC **CRX-25iA** (iPendant). Andere Steuerungen können abweichen — Menüs, Optionen und Sicherheit vor Produktiveinsatz prüfen.

![Rezeptmanager am iPendant — Übersicht mit Edit- und geladenen Registern](docs/hmi-ipendant-overview.png)

*Beispiel: `box_small.xml` gewählt, Abmessungen 300 × 200 × 150 mm. Rechts **Edit**-Register (SR[2], R[11–13]) und geladene **Loaded**-Register (SR[3], R[1–3]).*

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

Die Screenshots stammen von einem **CRX-25iA** iPendant; die Menüpfade gelten analog für R-30iB / R-50iA.

### 1. KAREL aktivieren

**Setup → KAREL Enbl** öffnen und **`$KAREL_ENB`** auf **TRUE** (`1`) setzen. Ohne diese Option laufen KAREL-Programme nicht (ggf. Neustart der Steuerung).

### 2. KAREL-Programme laden

`recipe_save.kl` und `recipe_load.kl` übersetzen (ROBOGUIDE oder `ktrans`) und die `.pc`-Dateien auf die Steuerung laden.

### 3. Webseite kopieren

`recipe_hmi.stm` in den Speicherort für Webseiten kopieren. **FANUC-Webseiten müssen die Endung `.stm` haben.**

### 4. Seitenpfad in `$TX_SCREEN` eintragen

**Setup → TxScr Screen** öffnen. **Gerät** und **Verzeichnis** von `recipe_hmi.stm` eintragen und mit **Save** speichern.

![Setup → TxScr Screen — Gerät, Verzeichnis, Save für $TX_SCREEN](docs/setup-tx-screen.png)

### 5. Rezeptordner anlegen

Auf der Steuerung **`FR:\recipes\`** anlegen. Optional **`box_small.xml`** aus diesem Repository dorthin kopieren.

### 6. Webaufruf für KAREL freigeben

Unter **Setup → Host Comm** (HTTP-Authentifizierung) den Zugriff so einstellen, dass die `.stm`-Seite die KAREL-Programme aufrufen kann (**KAREL CGI** — laut Steuerungshandbuch).

### 7. Rezeptseite öffnen

**Am Bedienhandgerät:** iPendant-Menü **Browser** → Seitenname aus `$TX_SCREEN` wählen.

**Im PC-Browser:**  
`http://<Roboter-IP>/<konfiguriertes-Verzeichnis>/recipe_hmi.stm`

**View Recipe** / **Load Recipe** lesen XML in Register; **Save Recipe** schreibt die Edit-Felder nach `FR:\recipes\<Name>.xml`.

![iPendant-Browser — Rezeptmanager-Seite (Combobox, Eingaben, Load / Save)](docs/hmi-recipe-manager-browser.png)

## Hinweise
- Werte werden als Integer (mm) gespeichert. Für Dezimalstellen mit x10 oder x100 speichern.
- `TPOUT[5] = FALSE` dient als „Roboter beschäftigt“-Verriegelung, damit Rezepte nicht mitten im Zyklus geändert werden – an das eigene Signal anpassen.
- Die XML wird Tag für Tag mit `XML_ADDTAG` / `XML_SCAN` / `XML_GETDATA` gelesen.

## Haftungsausschluss

Dieses Repository ist **Beispiel- / Lehrcode** — kein fertiges Produkt und keine offizielle FANUC-Lösung.

Vor dem Einsatz am live Roboter oder in der Produktion bitte **an der eigenen Anlage testen**, die Sicherheitsvorgaben vor Ort beachten und die FANUC-Dokumentation zur Steuerung heranziehen.

**Der Autor übernimmt keine Haftung** für Folgen aus der Nutzung dieses Codes.

## Lizenz

Veröffentlicht unter der [MIT License](LICENSE).

## Autor
**Muratcan Cetinkaya**
E-Mail: mcetinkaya40@gmail.com
Web: https://www.muratcancetinkaya.net
