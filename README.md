# doesitpenplot
List of files I develop for penplotting

Settings:
Tool offset: 4mm
Lift z-height: 4mm
max size: 150x150 mm (usually much less)
alignment, left, left. 

# Generating GCode for the D3D Universal Plotter with Open Source Software

The purpose of this page is to document several specific workflows of generating GCode for the D3D Universal Plotter.

> **Warning:** This wiki is a work in progress and some of the instructions haven't been tested.

## Before You Start

- These instructions assume a D3D Max build volume is 6x6x6 inches (150x150x150 mm). You can adapt the instructions according to your build volume.
- Recommend using OSE Linux 2 or higher as it comes preinstalled with Inkscape software and gcodetools and gcodeplot plugins.

---

## Working with Images in Inkscape

### Create a Template

Follow these instructions to create a file you can use to start a plotter project.

1. Open Inkscape.
2. Go to **File > Document Properties**. *It opens on the **Page** tab.*
3. For the **Custom size** section, enter `150` into width and height fields, then press **Enter**. *Make sure the units are mm.*
4. Go to the **Metadata** tab. Fill out information that can help you work with your file in the future.
5. Go to the **License** tab. *For OSE work, use CC Attribution Share Alike.*
6. Exit the screen.
7. Save the file, name it `General_Plotter_Template.svg`.
8. Use the terminal to move it into the Templates folder:
```bash
   sudo mv General_Plotter_Template.svg /usr/share/inkscape/templates
```

Now you can use **File > New from Template** to start a new project.

---

### Use Inkscape to Turn PNG to SVG, and from SVG Generate GCode

See also: [Using Inkscape to Generate Gcode](https://wiki.opensourceecology.org/wiki/Using_Inkscape_to_Generate_Gcode)

According to a thread in the [Inkscape forum](https://inkscape.org/cs/forums/cutplot/), the issue of a plotter repeating every curve twice is solved by using the **Pass by Pass** option in the GCode panel.

#### PNG/JPEG to SVG

*Simple line art*

1. Select PNG or JPEG.
2. Go to **Path > Trace Bitmap**.
3. Turn on **Live Preview** using the checkbox.
4. Adjust **Brightness cutoff Threshold** until the image looks good to you.
5. Click **OK**.
6. Click and drag the image away — you'll see the JPEG. Delete the JPEG and bring back your trace.

#### SVG to GCode

1. Select all and go to **Path > Object to Path**.
2. Select all and go to **Object > Ungroup**. *You may need to select all and ungroup multiple times.*
3. Save as **3-axis gcode plotter (\*.gcode)**. See [Gcodeplot Settings](https://wiki.opensourceecology.org/wiki/Using_Inkscape_to_Generate_Gcode#Gcodeplot_Settings).
4. After saving your `.gcode`, open it in nc-viewer.
5. Add the following start code to the top of the file:

```gcode
G00 S1; endstops
G00 E0; no extrusion
G01 S1; endstops
G01 E0; no extrusion
G21; millimeters
G91 G0 F2100.0 Z4.000; pen park !!Zsafe
G90; absolute
G28 X; home
G28 Y; home
G28 Z; home
G29; autolevel - needs to be over +20 x
G90; absolute
```

#### SVG into DXF

1. Select everything and go to **Object > Ungroup**.
2. With everything still selected, go to **Path > Break Apart**. *You should see boxy artifacts.*
3. Save as DXF.
4. Uncheck boxes at the Desktop Cutting Plotter prompt and click **OK**.

---

### How to Fill Closed Outlines with Hatching

There are a couple of plugins that help with hatching.

#### KM-Laser Plugin

See: [KM-Laser on GitHub](https://github.com/KnoxMakers/KM-Laser)

1. Open plotter template.
2. Import bitmap image.
3. Use **Trace Bitmap**.
4. Select all — clear fill, add stroke.
5. Go to **Extensions > KM LASER > GENERATE: HATCH FILL**.
6. Save as gcode.

#### AxiDraw Plugin

See: [AxiDraw on GitHub](https://github.com/evil-mad/AxiDraw/)

> **Warning:** If you install AxiDraw plugins, **do NOT replace** `hershey.py` and the hershey SVG files. AxiDraw's hershey text isn't working, and it will break the **Render > Hershey Text** extension pre-installed in Inkscape.

1. Open plotter template.
2. Import bitmap image.
3. Use **Trace Bitmap**.
4. Select all — clear fill, add stroke.
5. Go to **Extensions > AxiDraw Utilities > Hatchfill**.
6. Go to **Extensions > AxiDraw Utilities > Plot Optimization**.
7. Save as gcode.

---

### How to Render a Bitmap Image Using Tiled SVG Clones

Reference: [Render Bitmap Images using Tiled SVG Clones](http://web.archive.org/web/20100206013751/http://sidux.com/index.php?module=Wikula&tag=InkscapeTiledCloneToolBitmapRenderEN)

1. Open your general plotter template.
2. Import a bitmap file.
3. Add a layer above the one with your bitmap.
4. Use the circle tool to make a small circle, close to your drawing in its upper-right.
   - Set the stroke and fill of the circle to undefined.
5. Select the circle and go to **Edit > Clone > Create Tiled Clones**.
6. Go to the **Trace** tab and select **Trace the drawing...**.
7. Check all the boxes for the 3rd field.
8. Set the **width** and **height** fields to the dimensions of your drawing.
9. Click **Create**.
10. Save your file as SVG. *This helps in case you mess up the next steps and need to start over.*
11. You have your circles. Use the [SVG to GCode](#svg-to-gcode) instructions to complete.

---

### How to Create Contour Lines

1. Threshold image
2. Erode image
3. Line trace
4. Erode again
5. Repeat

---

## Working with Text in Inkscape

### Install Fonts

See [OSE Fonts — How to Install in OSE Linux](https://wiki.opensourceecology.org/wiki/OSE_Fonts#How_to_Install_in_OSE_Linux)

Install OSE Fonts, or for more fonts see Creative Commons and open-licensed fonts:
- [Google Fonts](https://fonts.google.com/)
- [Font Library](https://fontlibrary.org/)
- [FontSpace Open](https://www.fontspace.com/category/open)
- [Open Foundry](https://open-foundry.com/)

#### How to Create Custom Fonts

See: [Inkscape Manuals — Creating Custom Fonts](https://inkscape-manuals.readthedocs.io/en/latest/creating-custom-fonts.html)

---

### How to Generate Stroke Text

Stroke text is text that mimics handwriting. This is good for small text such as addressing a letter or the inside of a greeting card.

1. Go to **Render > Hershey Text**.
2. Insert your text into the field. *You'll enter it one line at a time.*

---

### How to Generate Outline Text

Outline text is text that is graphical in nature. This is good for big decorative text or fancy fonts. This requires having an extension that provides hatchfill — AxiDraw and KM-Laser are two extension sets which include hatchfill.

1. Click and open the text tool.
2. Type your desired text and make it the desired size and other features. *You may want to save your file at this point, in case you want to make small edits to the text.*
3. Select the text and go to **Path > Object to Path**.
4. Go to **Object > Fill and Stroke**.
5. Remove the fill. Add stroke.
6. Select hatchfill from either **AxiDraw Utilities** or **KM Laser**.
7. Edit the settings according to your preference.

---

## Inkscape Applied Examples

### How to Address Regular Mailing Envelopes

- Most envelopes will probably be longer than the plotter bed, but since you only need to print a portion of the area, it's okay.
- Before you get started, either look up common dimensions for an address window on an envelope, or determine for yourself the area on the envelope you want to print. *In the US, refer to [USPS guidelines](https://pe.usps.com/text/dmm300/202.htm). A common #10 envelope is 4⅛ inches high by 9½ inches long.*
- Assume that when you print, you'll align the bottom left of the envelope with the bottom left of the plotter bed.

1. Open your general plotter template.
2. Use guides to bound the area where you want to add the address. *Hint: You may need to temporarily change your units to inches in Document Properties, and zoom to 100%.*
3. You can save this file as an envelope template, if you want.
4. Go to **Extensions > Render > Hershey Text...**.
5. Enter the first line of the address and click **Apply**.
6. Place your text image where you want it.
7. Repeat until complete.
8. Save as gcode.

See also: [Working with Text — Stroke Text](#how-to-generate-stroke-text)

---

### How to Create a Greeting Card

Instructions for making an OSE-themed greeting card and envelope. Check out [OSE Graphic Guidelines](https://wiki.opensourceecology.org/wiki/OSE_Graphic_Guidelines) for the latest assets.

**Common greeting card sizes (W × H):**
- 3.5 × 5 in (postcard-style, no fold)
- 13 cm × 9.5 cm (opens to 19 cm) (fold down)
- 3.75 cm × 5 cm (opens to 7.5 cm) (folds open)

**Make the card:**

1. Open your general plotter template.
2. Use the **rectangle** tool (`F4`) to create a rectangle that is the same size as your desired card, approximately where you want to print on the bed.
3. Make sure your rectangle is selected, then press **Shift+G**, or go to **Objects > Object to Guides**. *You can save this file as a template for future greeting cards.*
4. Go to **File > Import** to fetch an image to use.
5. Move your image where you want and add your text (see [Outline Text](#how-to-generate-outline-text) or [Stroke Text](#how-to-generate-stroke-text) instructions).
6. Save your file as `.SVG` — just in case you mess up the next steps and need to start over.
7. Use the [SVG to GCode](#svg-to-gcode) instructions to complete.

---

## Use Processing to Create SVGs

### Create a Spirograph

1. Download zip from [processing-spirograph on GitHub](https://github.com/rspt/processing-spirograph).
2. Extract, click the `.pde` file.
3. Edit the size to about 400×500 px; edit the stroke to about 3px.
4. Run.
5. Move the mouse around to get different spirographs.
6. Click `s` to export to PDF (with SVG).
7. Import into Inkscape, ungroup all (several times), convert object to path, make sure document properties have the right size, save as gcode.

---

### Create with SquiggleDraw

1. Install [Processing](https://processing.org/download/).
2. Add [controlP5](http://www.sojamo.de/libraries/controlP5/) to your Sketchbook library.
3. Download the [SquiggleDraw zip](https://github.com/gwygonik/SquiggleDraw) from the repo and extract it where you want.
4. Add images to `SquiggleDraw-master/SquiggleDraw/data`. *The images need easy, simple names you can remember. Don't use images that are too large — resize them first.*
5. Go to `SquiggleDraw-master/SquiggleDraw` and click `SquiggleDraw.pde`.
6. Find the bit of code that says:
```java
   String imageName = 'Rachel-Carson.jpg'
```
   and change it to the file name of the image you want to SquiggleDraw.
7. Click the run button.
8. Adjust settings as desired.

---

### Create a Stippled File Using StippleGen

OSE Linux 2 already includes Processing.

See [StippleGen](https://wiki.evilmadscientist.com/StippleGen) for instructions on how to download and use StippleGen.

If there are details you especially want captured by the stippling process, consider using the [Burn and Dodge tools](https://docs.gimp.org/en/gimp-tool-dodge-burn.html) in GIMP to create a sort of radical contour. You can also experiment with the [Levels tool](https://docs.gimp.org/en/gimp-tool-levels.html), [contrast/saturation](https://docs.gimp.org/en/gimp-tool-brightness-contrast.html), and other photo editing techniques.

---

## Other Methods

### DXF into GCode (with Python)

See: [DXF to G-code Conversion Tutorial](https://wiki.opensourceecology.org/wiki/DXF_to_G-code_Conversion_Tutorial)

### Use GIMP to Create Stippling

See: [Producing a stippled image with GIMP](https://wiki.evilmadscientist.com/Producing_a_stippled_image_with_Gimp)

### Create a Signature

You can use this browser-based tool: [jSignature Demo](https://willowsystems.github.io/jSignature/#/demo/)

---

## See Also

- [D3D Plotter Log](https://wiki.opensourceecology.org/wiki/D3D_Plotter_Log)
