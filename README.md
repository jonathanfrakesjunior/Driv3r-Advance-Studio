# Driv3r Advance Studio

Modding tool for **Driv3r** (Game Boy Advance, European version, code B3RP, 8 MB).
Runs in the browser without installation: **double-click `index.html`** (or `Driv3r Advance Studio starten.bat`).
No server, no Python, no internet connection needed. Tested with Chrome/Edge.

**Languages:** Deutsch, English, Français, Español, Italiano – choose at the top right (remembered).

Expected ROM: SHA-1 `3e4da1cd2a5915d2218cd59e061baeb3a08be1c6`.

## Getting started

1. Open `index.html`, click **Load ROM** (or drop the .gba onto the window).
2. Choose a section on the left, export, edit, import.
3. Save the project regularly under **ROM & project** (`.d3studio` – contains only your changes).
4. **Save ROM** creates the finished `.gba` (header checksum is fixed). Always test in mGBA.
   Alternatively: **Create IPS patch** to share without the ROM.

The original file is never modified. When space runs low, the Studio moves data to the end of the ROM automatically,
updates all pointers and expands the ROM to 16 MB if needed (the EEPROM save does not allow more).

## Sections

| Section | Content | Format |
|---|---|---|
| Images & textures | Title screen, main menu, 2 menu 3D textures, 10 city texture atlases 256×256 | indexed PNG; VC compression automatic |
| Cutscenes | 213 story stills 240×160, 33 sequences with subtitles (EN/FR/DE/ES/IT) | PNG/JPG in, JPEG re-encoded to fit |
| Sprites | 23 vehicles (all angles), character animations, effects | PNG per frame (also in a new size) or as a sheet (8 frames/row) |
| HUD, font & skyline | HUD icons, menu font, **in-game font** (256 characters 8×16 for all messages), 6 skyline panoramas | 4-bit PNG, glyph editor |
| Palettes | 16 palette files (cities day/evening/night, menus, HUD) | colour picker, JASC .pal, .act, PNG |
| Texts | Mission messages in 5 languages, credits, menu texts | editor + CSV |
| Sound & music | 10 songs (tracker GBAMOD30), 16 instruments, 38 effects | WAV, MIDI export/import, raw .gbamod |
| Map editor | Miami & Nice as a textured top-down view from the 3D data, traffic lanes | **Build new buildings, roads with AI traffic, squares, water, ramps, bridges**; delete/move/retexture faces, clear/restore cells, descriptors |
| Missions | 25 missions (Miami 1–12, Nice 13–25) with all steps, all 19 commands named | edit fields/hex, insert/delete/move steps, drag points on the map, JSON |
| Complete package | everything out as a ZIP and back in | imported unchanged, the ROM stays byte-identical |

### Tips

* **Images:** Indexed PNGs keep their indices. RGB images are mapped to the palette. For completely new
  full-screen images tick "Generate new colours" – the Studio computes its own palette within the allowed range.
* **City textures** are mapped onto the 3D faces via descriptors (frames). The "Texture frames" option shows them in the atlas.
  Keep motifs in the same place or adjust the descriptor in the map editor.
* **Music via MIDI:** Assign an instrument to each MIDI channel; chords are spread over up to 8 GBA channels.
  "Preview" renders the song in the browser without saving. Import your own instruments as WAV first.
* **Building (map editor → Build):** *Building*: click corner points, double-click builds (convex footprint, height,
  storey, facade/roof texture, solid, occlusion). *Ground/road*: drag a rectangle – road, grass, water or
  graphics only; different start/end height = ramp, height above water = bridge. Roads 64 units wide or more
  automatically get two opposite lanes with AI traffic (right-hand traffic), connected to nearby lanes (up to 200 units)
  – so it is best to start at a junction. The "Traffic" layer shows all lanes.
  *Faces*: click individual faces to delete, move, retexture them or change their material.
  On the first build the city is copied to the end of the ROM (ROM becomes 16 MB); the original stays untouched.
* **Missions:** 00 mission start (texture set, time of day, vehicle, start position), 02 checkpoint,
  03 targets with time limit, 04/0B/10 drive to (zone, marker or object, with radar arrow), 05 cutscene,
  06 message (number = message ID in the Texts tab), 07 timer, 08 roadblock (6 objects), 09 AI vehicle with route,
  0A actor with countdown, 0C chase, 0D formation, 0E wait for actor, 0F race, 11 path, 12 mission passed.
  The map editor shows the points of the selected mission; they can be moved with the mouse.
* **In-game font:** under "HUD, font & skyline" → group "Font". Newly drawn characters (e.g. lower-case letters)
  are then allowed in the text editor.

## Technical notes (for the curious)

* "VC" compression (VD-dev): header `VC`, u32 size, mode 0–9; bit stream with gamma codes (decoder in the game at 0x08254338).
* Graphics: mode 4 (8-bit bitmap), HUD/font/skyline as 4-bit objects in 1D mode.
* City: 128×128 cells of 512 units, grid at the map base (Miami 0x5F8800, Nice 0x4B0000),
  texture descriptors at base+0xC000, mission table via base+0x11020, collision surfaces via base+0x1101C,
  occluder walls via base+0x11000, traffic lanes via base+0x11064 (per lane: nodes X/height/Y + successors).
  Cell entries: 0 geometry (must come first, provides the ground height), 1 occlusion, 2 detail, 3 objects,
  4 collision, 5 traffic spawn points.
* Mission interpreter at 0x0816E178 (init table 0x0817137C, update table 0x0816E324).
* In-game font: pointer at 0x234E54, 256 characters of 8×16 pixels, 4 bit.
* Sound: "LS_Play (C) Logik State 2003", mixing rate 10512 Hz, songs "GBAMOD30" (RLE-coded channel columns).
* Cutscenes: standard JPEG, only the SOS marker FFDA is stored as FF1A.


**Your own cities:** `werkzeuge/stadt_aus_bild.py <image.png> <output.gba> [units per pixel]` (Python with numpy,
scipy, scikit-image, Pillow). Template colours: blue = water, black = road, yellow = beach, green = park,
grey = buildable area, white = tall buildings. The original ROM is read from the Studio folder.

## Limitations

* Building footprints must be convex (compose L or U shapes from several buildings).
* Objects in the cells (type 3: street lamps, hydrants etc.) are shown and kept, but not edited individually.
* The song preview in the browser is a recreation of the tracker player – in the game it may sound slightly different.
