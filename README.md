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
| City from image | painted map sketch → completely new city (Miami or Nice slot) | PNG/JPG; preview, start point by click, density, textures; mission points are adjusted |
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

* **City from image – custom colours (since 1.2):** switch to *Custom colours* under "Colour mapping". The Studio extracts the
  main colours of the image and suggests a mapping (road = the colour that forms lines; blended colours are ignored).
  Each colour can be assigned to an area type, changed, removed or added from the image with the *eyedropper*.
  The **colour tolerance** sets how far a pixel may deviate from a palette colour; pixels outside it (magenta in the preview)
  take the area of their neighbours – ideal for JPEG artefacts. *Smoothing* removes noise inside areas without touching roads.
  If an image does not match the standard colours, the Studio switches to custom colours automatically.

* **3D view (map editor → 3D):** shows the real city geometry around the view point. Drag with the left mouse button = orbit,
  right button (or Shift + drag) = pan, wheel = zoom, "View distance" sets how many cells are loaded.
  In the 3D view everything is textured as long as the "Textures" box is ticked.
  The editor works exactly as in the top-down view: a click selects a face or cell, building and placing ready-made buildings
  work with a click, and selection and build previews are shown as outlines. Requires WebGL.
* **Ready-made buildings (one click):** choose the *Ready-made building* mode, pick a shape (rectangle, L, U, T, cross, block with
  courtyard, hexagon, octagon, round tower, tower on a podium, setback tower, twin towers), set width/depth/rotation/height and
  click on the map - the building appears instantly with walls, roof, collision and occlusion. Multi-part shapes are made of
  several convex bodies internally.

* **Draw road (map editor → Draw road):** click control points, a smooth curve runs between them (rounded corners +
  Catmull-Rom). Double-click, right-click or Enter builds it, Backspace undoes the last point.
  It creates the carriageway, pavements on both sides (width 0 = off), traffic lanes in both directions (connected to nearby
  lanes) and, on request, a collision surface for bridges/ramps. Start and end height give a slope.
  Works in the top-down view and in 3D.

* **Catalogue (map editor → Catalogue):** the Studio collects every building and 3D object that occurs in the loaded city
  (Miami: 234 entries), shows them as thumbnails and places the chosen one with a single click - with its original
  textures, collision and occlusion. Filters for towers, houses and small parts; rotation in 45° steps, size in per cent
  and a height offset can be set.
  The Studio additionally reads the buildings of the **unchanged original ROM**, so the original buildings stay available
  even after "City from image" has replaced the city completely (filter "from the original ROM").
* **Reference image (map, bottom right):** load your own image - city map, sketch, screenshot - and model on top of it.
  It appears in the top-down view and flat in the 3D view, with adjustable opacity, units per pixel, rotation, centre and
  height; optionally below the city. "Move image" sets the centre by clicking on the map. The image is only a template and
  is never written into the ROM. In the "City from image" tab a button takes the loaded image over as the reference.

* **Junctions:** the *Junction* mode places a complete junction (3 or 4 arms) with one click - carriageway, pavements and
  full turning lanes; the arm ends are connected to nearby lanes. When a newly drawn road crosses existing lanes, those
  lanes are now split at the nearest node and linked in both directions, so traffic turns there instead of doing a U-turn
  at the end of the road. The cells' spawn-point entries are remapped to the new lane numbers automatically.
* **3D controls:** the *Rotate/Pan* button decides what the left mouse button does; the right button always does the other
  one. The *view distance* ranges from 3 k to 33 k world units (steps marked ⚠ cost noticeable performance depending on
  your machine); the number of drawn triangles is shown in the toolbar.

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

## Mod: Vice City (mods/Driv3r - Vice City Mod.gba)

Miami is completely replaced by a city based on the template `werkzeuge/vice_city_karte.png` (Nice stays original):
two main islands with beach, parks, airport, bridges and small islands, 365 road sections with AI traffic
(730 lanes), about 740 buildings along the roads (tall blocks on the white areas of the template), quay walls, water.
The building density is chosen so that the game runs at 20 frames/s like the original.
The player start and starting cars are in the downtown of the west island; all mission points were moved to the nearest
new road (mission 1 entirely relative to the new start). The missions are therefore playable but no longer follow
the original Miami geography – they can be adjusted further in the mission editor.
The ROM can be opened in the Studio and edited further (building, faces, traffic).

**Your own cities:** directly in the Studio under **World → City from image** (no Python needed; an example template is included there). Alternatively still `werkzeuge/stadt_aus_bild.py <image.png> <output.gba> [units per pixel]` (Python with numpy,
scipy, scikit-image, Pillow). Template colours: blue = water, black = road, yellow = beach, green = park,
grey = buildable area, white = tall buildings. The original ROM is read from the Studio folder.

## Limitations

* Building footprints must be convex (compose L or U shapes from several buildings).
* Objects in the cells (type 3: street lamps, hydrants etc.) are shown and kept, but not edited individually.
* The song preview in the browser is a recreation of the tracker player – in the game it may sound slightly different.
