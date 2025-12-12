# color-change-gcode

Two tiny static web pages to help with **manual filament/color changes** during a single-extruder 3D print.

- generator.html (PC/laptop): pick a .gcode file -> parses it locally -> generates a share link + QR code
- viewer.html (phone/PC): opens via #d=... in the URL -> shows a checklist + saves checkbox state in localStorage

No companion app. No backend. Your G-code never leaves your machine.

## Live Pages

After GitHub Pages is enabled:

- Generator: https://downthecrop.github.io/color-change-gcode/index.html
- Viewer:    https://downthecrop.github.io/color-change-gcode/viewer.html

## Repo Layout

Put both files in docs/:

docs/
  generator.html
  viewer.html
README.md

## Enable GitHub Pages

Repo -> Settings -> Pages

- Source: Deploy from a branch
- Branch: main
- Folder: /docs

## OrcaSlicer Setup (required for color names)

This tool expects your G-code to contain a marker telling it what filament you’re switching from -> to at each pause.

In OrcaSlicer:

Printer Settings -> Machine G-code -> On filament change:

;CHANGE_FILAMENT from={filament_preset[previous_extruder]} to={filament_preset[next_extruder]}
M117 CHANGE TO {filament_preset[next_extruder]}
M25

Notes:
- The filament preset names are what show up in the checklist (e.g. Grey, Blue, Black, WhitePLA).
  Name them exactly how you want them displayed.
- Insert your color changes as filament changes (so Orca triggers “On filament change”), not just random pauses.

## How It Works

### Generator
1) Open generator.html
2) Choose your .gcode
3) Click Generate QR
4) Scan the QR on your phone (or click Open viewer / copy the link)

### Viewer
- Opens a checklist from the encoded URL hash: viewer.html#d=...
- Checkboxes persist using localStorage keyed to the checklist content, so you can refresh or switch apps without losing your place.

## Local Testing (optional)

If you want to test without Pages:

python3 -m http.server 8000

Then open:
- http://localhost:8000/docs/generator.html
- http://localhost:8000/docs/viewer.html

(Use a server; don’t open as file:// if your browser blocks features.)

## Troubleshooting

### Detected steps = 0
Your G-code probably doesn’t contain the ;CHANGE_FILAMENT marker + pause combo.
Verify your Orca “On filament change” matches the snippet above, and that the print actually includes filament changes.

### TO color is missing / (unknown)
A pause was found, but no nearby ;CHANGE_FILAMENT ... to=... or M117 CHANGE TO ... was bound to it.
Check the injected lines are immediately before the pause (usually M25).

### QRCode is not defined
The QR library failed to load (no internet / blocked CDN).
This repo loads QR from cdnjs:
https://cdnjs.cloudflare.com/ajax/libs/qrcode/1.5.0/qrcode.min.js

If you need fully offline: commit qrcode.min.js into the repo and reference it locally.

## License

MIT (recommended). Add a LICENSE file with MIT text if you want.