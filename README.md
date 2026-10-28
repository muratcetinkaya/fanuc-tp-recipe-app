# FANUC Recipe Manager (KAREL + XML + Web)

🌐 **English** | [Türkçe](README.tr.md) | [Deutsch](README.de.md)

A minimal, generic example of **recipe storage and management on a FANUC robot controller**,
using only built-in features: KAREL, XML files on the controller (FR:), registers and a Web Server page (iPendant / browser).

A recipe here is just a **name + 3 numbers** (box length, width, height) – add as many fields as you like.

**Tested on:** FANUC **CRX-25iA** (iPendant). Other controllers may differ; verify menus, options, and safety on your system before use in production.

![Recipe Manager on the iPendant — overview with edit vs loaded registers](docs/hmi-ipendant-overview.png)

*Example: `box_small.xml` selected, dimensions 300 × 200 × 150 mm. **Edit** registers (SR[2], R[11–13]) vs **Loaded** active registers (SR[3], R[1–3]) are shown on the right.*

## How it works

```
 Web page (recipe_hmi.stm)
   |  combo box  -> SR[1]   (selected file, e.g. "box_small.xml")
   |  edit boxes -> SR[2], R[11..13]   (EDIT side)
   v
 recipe_save.kl   SAVE / CREATE / DELETE   -> FR:\recipes\<name>.xml
 recipe_load.kl   VIEW (-> edit regs)  /  LOAD (-> active regs SR[3], R[1..3])
```

| Button | Program | What it does |
|---|---|---|
| New Recipe | recipe_save | clears the edit fields |
| Save Recipe | recipe_save | writes SR[2] + R[11..13] to an XML file |
| View Recipe | recipe_load | reads the selected XML into the edit fields |
| Load Recipe | recipe_load | reads the selected XML into the **active** registers the robot program uses |
| Delete Recipe | recipe_save | deletes the selected XML file |

Edit vs. Active separation means you can browse/modify recipes while the robot keeps running with the loaded one.

## Register map

| Register | Meaning |
|---|---|
| SR[1] | selected file (combo box) |
| SR[2] / SR[3] | recipe name – edit / active |
| R[11..13] | length, width, height – edit |
| R[1..3] | length, width, height – active (use these in your TP/KAREL program) |

## Setup

Screenshots below are from a **CRX-25iA** iPendant; the same menus apply on typical R-30iB / R-50iA setups.

### 1. Enable KAREL

Open **Setup → KAREL Enbl** and set **`$KAREL_ENB`** to **TRUE** (`1`). Without this, KAREL programs will not run (a controller restart may be required after changing the flag).

### 2. Load the KAREL programs

Translate `recipe_save.kl` and `recipe_load.kl` (ROBOGUIDE or `ktrans`) and load the resulting `.pc` files onto the controller.

### 3. Copy the web page

Copy `recipe_hmi.stm` to the controller memory location you will use for web pages. **FANUC web pages must use the `.stm` extension.**

### 4. Register the page path in `$TX_SCREEN`

Open **Setup → TxScr Screen**. Enter the **device** and **directory** where `recipe_hmi.stm` is stored, then press **Save**. If this is wrong, the built-in web server will not find your page.

![Setup → TxScr Screen — device, directory, and Save for $TX_SCREEN](docs/setup-tx-screen.png)

### 5. Create the recipes folder

On the controller, create **`FR:\recipes\`**. Optionally copy the sample file **`box_small.xml`** from this repository into that folder.

### 6. Allow web calls to KAREL

In **Setup → Host Comm** (HTTP authentication), enable access so the `.stm` page can invoke the KAREL programs (**KAREL CGI** / program call from web — match your controller option manual).

### 7. Open the Recipe Manager page

**On the teach pendant:** open the iPendant **Browser** menu and select the page **name** you configured in `$TX_SCREEN`.

**From a PC browser:**  
`http://<robot-ip>/<configured-dir>/recipe_hmi.stm`

Use **View Recipe** / **Load Recipe** to move data between XML files and registers; **Save Recipe** writes edit fields to `FR:\recipes\<name>.xml`.

![iPendant Browser — Recipe Manager page (combo box, edit fields, Load / Save)](docs/hmi-recipe-manager-browser.png)

## Notes
- Values are saved as integers (mm). If you need decimals, store them x10 or x100.
- `TPOUT[5] = FALSE` is used as a "robot busy" interlock so recipes can't be changed mid-cycle – adapt it to your signal.
- The XML is read one tag at a time with `XML_ADDTAG` / `XML_SCAN` / `XML_GETDATA`.

## Disclaimer

This repository is **sample / educational code**, not a product certified for any application.

**The author accepts no responsibility or liability** for damage, downtime, injury, data loss, or any other consequence arising from downloading, copying, modifying, or running this code on a robot, controller, or production line — including misuse, incomplete safety integration, or mismatch with your controller version or options.

You are solely responsible for risk assessment, safety review (including applicable standards and site rules), testing on your equipment, and compliance with FANUC documentation and local regulations. **Use at your own risk.**

## License

Released under the [MIT License](LICENSE) (see file for full text).

## Author
**Muratcan Cetinkaya**
Mail: mcetinkaya40@gmail.com
Web: https://www.muratcancetinkaya.net
