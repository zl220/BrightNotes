# Rendering / QA / pptx-conversion / publish toolchain (cross-platform)

Command recipes for building and checking an HTML deck. They work on macOS, Linux, and Windows (Git Bash) —
just point two variables at your local binaries first. No Playwright or Poppler required; the approach is
**headless Chrome/Chromium + PyMuPDF**.

## Prerequisites

- **A Chromium-family browser** (Chrome, Chromium, Edge, or Brave) for headless screenshots/PDF.
- **Python** with **PyMuPDF** (`pip install pymupdf`) and **Pillow** (`pip install pillow`).
- Optional: **LibreOffice** (only for converting a `.pptx` to images).
- Optional: **`gh`** CLI (only for publishing to GitHub Pages).

Set these once per shell to the paths on your machine:

```bash
# --- Chrome/Chromium (pick the line for your OS) ---
CHROME="google-chrome"                                                   # Linux (or: chromium, chromium-browser)
# CHROME="/Applications/Google Chrome.app/Contents/MacOS/Google Chrome"  # macOS
# CHROME="/c/Program Files/Google/Chrome/Application/chrome.exe"         # Windows (Git Bash); Edge also works

# --- LibreOffice soffice (only needed for pptx conversion) ---
SOFFICE="soffice"                                                        # Linux / on PATH (or: libreoffice)
# SOFFICE="/Applications/LibreOffice.app/Contents/MacOS/soffice"         # macOS
# SOFFICE="/c/Program Files/LibreOffice/program/soffice.exe"             # Windows
```

Use a temp dir you can write to — `TMP=/tmp` on mac/Linux, e.g. `TMP=/d/tmp` on Windows. Use absolute
`file://` URLs to the deck (`file:///abs/path/index.html` on mac/Linux; `file:///C:/abs/path/index.html` on
Windows).

## Screenshot one slide at the true 1280×720 size

The deck reads `location.hash` and shows slide N, so append `#N` to the URL. At a 1280×720 window the stage
scales to exactly 1:1.

```bash
"$CHROME" --headless=new --disable-gpu --hide-scrollbars --window-size=1280,720 \
  --screenshot="$TMP/s-07.png" "file:///ABS/PATH/index.html#7"
```

Loop over all slides (pad numbers so they sort):

```bash
for n in $(seq 1 41); do
  "$CHROME" --headless=new --disable-gpu --hide-scrollbars --window-size=1280,720 \
    --screenshot="$TMP/all-$(printf %02d $n).png" "file:///ABS/PATH/index.html#$n" >/dev/null 2>&1
done
```

## Contact sheet to scan the whole deck at once

Reading one montage beats opening 40 PNGs. ~12 thumbnails per sheet, each labeled with its page number.

```python
from PIL import Image, ImageDraw
import math
files=[f'all-{i:02d}.png' for i in range(1,42)]
cols,tw,per=3,380,12
for s in range(0,len(files),per):
    grp=files[s:s+per]; rows=math.ceil(len(grp)/cols); th=int(tw*720/1280)
    sheet=Image.new('RGB',(cols*tw+(cols+1)*8, rows*(th+22)+8),'white'); d=ImageDraw.Draw(sheet)
    for i,f in enumerate(grp):
        im=Image.open(f).resize((tw,th)); c=i%cols; r=i//cols
        x=8+c*(tw+8); y=8+r*(th+22)
        d.text((x,y), f.replace('all-','p').replace('.png',''), fill='black'); sheet.paste(im,(x,y+16))
    sheet.save(f'scan-{s//per+1}.png')
```

Zoom into a suspect region: `Image.open('all-11.png').crop((x0,y0,x1,y1)).resize((W,H)).save('zoom.png')`.

## Test a single icon before trusting it

When an icon looks wrong, iterate on just that `<symbol>` — far faster than re-rendering the whole deck.

```bash
cat > "$TMP/icontest.html" <<'EOF'
<!doctype html><meta charset=utf-8><body style="background:#2a2a2e;padding:30px;display:flex;gap:40px;align-items:center">
<svg width=0 height=0><symbol id="t" viewBox="0 0 64 64" fill-rule="evenodd"><!-- PATHS --></symbol></svg>
<svg style="width:140px;height:140px;fill:#1ec8e0"><use href="#t"/></svg>
<svg style="width:40px;height:40px;fill:#1ec8e0"><use href="#t"/></svg>
</body>
EOF
"$CHROME" --headless=new --disable-gpu --window-size=500,220 --screenshot="$TMP/icontest.png" "file:///$TMP/icontest.html"
```

## Export the whole deck to a 16:9 PDF

With the template's `@page{size:1280px 720px;margin:0}`, headless print yields true 16:9 pages (960×540 pt).

```bash
"$CHROME" --headless=new --disable-gpu --no-pdf-header-footer \
  --print-to-pdf="$TMP/deck.pdf" "file:///ABS/PATH/index.html"
```

Render PDF pages to PNGs to inspect (run from the dir containing the PDF; pass a plain filename — PyMuPDF
can be picky about some path styles):

```python
import fitz
d=fitz.open('deck.pdf'); print('pages', d.page_count, '| page0 pt', round(d[0].rect.width),'x',round(d[0].rect.height))
for i in range(d.page_count): d[i].get_pixmap(dpi=96).save(f'pg-{i+1:02d}.png')
```

End users get the PDF by clicking the deck's **下载 PDF** button → browser print dialog → destination
"Save as PDF". Backgrounds survive because of `print-color-adjust:exact`.

## Reading a .pptx faithfully (CJK-safe)

`markitdown` can turn CJK text into `????` on some setups, so read the deck as **images**, not text.

```bash
# 1) pptx -> pdf. Call the soffice BINARY directly; some skill wrappers use a soffice.py helper that
#    relies on socket.AF_UNIX and fails on Windows — avoid it there.
"$SOFFICE" --headless --convert-to pdf --outdir "$TMP/render" "deck.pptx"
# 2) pdf -> per-page PNGs with PyMuPDF (run from $TMP/render):
python -c "import fitz; d=fitz.open('deck.pdf'); [p.get_pixmap(dpi=110).save(f'slide-{i+1:02d}.png') for i,p in enumerate(d)]"
# then view the slide-NN.png images to transcribe content accurately.
```

Extract embedded media (the pptx is a zip):

```bash
cd "$TMP" && mkdir -p unpack && cd unpack && unzip -q "/abs/path/deck.pptx"
ls ppt/media/    # image-*.png / *.jpeg — copy the ones you need into the deck's assets/ (rename, drop spaces)
```

Map media → slide by viewing the images; filenames aren't a reliable guide. Keep complex raster diagrams as
images; redraw simple vector charts (curves, bars, water-level) in inline SVG; rebuild flat glyph icons from
the symbol library.

## Publish to GitHub Pages (folder deploy, normal push = always-latest)

```bash
cp -r deck-folder /path/to/PublicRepo/              # folder = index.html + assets/
cd /path/to/PublicRepo && git add -A && git commit -m "add: <deck>" && git push
# enable Pages once (source = main / root):
gh api -X POST repos/<owner>/<repo>/pages -f 'source[branch]=main' -f 'source[path]=/'
# verify after ~1 min:
gh api repos/<owner>/<repo>/pages/builds/latest     # status: built
curl -s -o /dev/null -w "%{http_code}\n" https://<owner>.github.io/<repo>/<deck-folder>/
```

The live Pages URL always serves the latest push, so re-pushing "overwrites" what viewers see; commit history
stays as a private backup (page visitors don't see it). A plain `git push` per update is enough — no
force-push needed.
