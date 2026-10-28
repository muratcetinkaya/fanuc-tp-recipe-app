# FANUC Recipe Manager (KAREL + XML + Web)

🌐 **English** | [Türkçe](README.tr.md) | [Deutsch](README.de.md)

A minimal, generic example of **recipe storage and management on a FANUC robot controller**,
using only built-in features: KAREL, XML files on the controller (FR:), registers and a Web Server page (iPendant / browser).

A recipe here is just a **name + 3 numbers** (box length, width, height) – add as many fields as you like.

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
1. **First, enable KAREL: in the system variables set `$KAREL_ENB` from `0` to `1`.** Without this, KAREL programs cannot run.
2. Translate `recipe_save.kl` and `recipe_load.kl` (ROBOGUIDE or `ktrans`) and load the `.pc` files on the controller.
3. Copy the web page `recipe_hmi.stm` to the controller. **FANUC web pages must use the `.stm` extension.**
4. **Register the page directory in the `$TX_SCREEN` system variable.** The device/directory where `recipe_hmi.stm` is stored must be defined in `$TX_SCREEN`, otherwise the controller's web server will not find the page.
5. Create the `FR:\recipes\` folder (optionally copy `example/box_small.xml`).
6. Allow the KAREL programs to be called from the web (Setup > Host Comm / HTTP authentication, KAREL CGI enabled).
7. Open the page in one of two ways:
   - **On the teach pendant:** open the **Browser** menu on the iPendant and select the page by the **name you assigned in `$TX_SCREEN`**.
   - **From a PC browser:** use the directory configured in `$TX_SCREEN`: `http://<robot-ip>/<configured-dir>/recipe_hmi.stm`

## Notes
- Values are saved as integers (mm). If you need decimals, store them x10 or x100.
- `TPOUT[5] = FALSE` is used as a "robot busy" interlock so recipes can't be changed mid-cycle – adapt it to your signal.
- The XML is read one tag at a time with `XML_ADDTAG` / `XML_SCAN` / `XML_GETDATA`.

## Author
**Muratcan Cetinkaya**
Mail: mcetinkaya40@gmail.com
Web: https://www.muratcancetinkaya.net
