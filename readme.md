# nimPNG
Portable Network Graphics Encoder and Decoder written in Nim store lossless image with good compression

all PNG standard color mode are supported:

  -  LCT_GREY = 0,       # greyscale: 1,2,4,8,16 bit
  -  LCT_RGB = 2,        # RGB: 8,16 bit
  -  LCT_PALETTE = 3,    # palette: 1,2,4,8 bit
  -  LCT_GREY_ALPHA = 4, # greyscale with alpha: 8,16 bit
  -  LCT_RGBA = 6        # RGB with alpha: 8,16 bit

both interlaced and non-interlaced mode supported

recognize all PNG standard chunks:
IHDR, IEND, PLTE, IDAT, tRNS, bKGD, pHYs, tIME, iTXt, zTXt
tEXt, gAMA, cHRM, sRGB, iCCP, sBIT, sPLT, hIST

unknown chunks will be handled properly

the following chunks are supported (generated/interpreted) by both encoder and decoder:

-    IHDR: header information
-    PLTE: color palette
-    IDAT: pixel data
-    IEND: the final chunk
-    tRNS: transparency for palettized images
-    tEXt: textual information
-    zTXt: compressed textual information
-    iTXt: international textual information
-    bKGD: suggested background color
-    pHYs: physical dimensions
-    tIME: modification time

the following chunks are parsed correctly, but not used by decoder:
cHRM, gAMA, iCCP, sRGB, sBIT, hIST, sPLT

Supported color conversions:

- anything to 8-bit RGB, 8-bit RGBA, 16-bit RGB, 16-bit RGBA
- any grey or grey+alpha, to grey or grey+alpha
- anything to a palette, as long as the palette has the requested colors in it
- removing alpha channel
- higher to smaller bitdepth, and vice versa

### Planned Feature(s):
- streaming for progressive loading

## Basic Usage
```nimrod
import nimPNG

let png = loadPNG32("image.png")
#is equivalent to:
#let png = loadPNG("image.png", LCT_RGBA, 8)
#will produce rgba pixels:
#png.width -> width of the image
#png.height -> height of the image
#png.data -> pixels data in RGBA format
```

if you already have the whole file in memory:

```nimrod
let png = decodePNG32(raw_bytes)
#will do the same as above
```

other variants:

* loadPNG24 -> will produce pixels in RGB format 8 bpp
* decodePNG24 -> load png from memory instead of file

to create PNG:

* savePNG32("output.png", rgba_pixels, width, height) or savePNG24
* encodePNG32(rgba_pixels, width, height) or encodePNG24

special notes:

* Use **loadPNG** or **savePNG** if you need specific input/output format by supplying supported **colorType** and **bitDepth** information. 
* Use **encodePNG** or **decodePNG** to do *in-memory* encoding/decoding by supplying desired colorType and bitDepth information

pixels are stored as raw bytes using Nim's string as container:

|           Byte Order           |      Format      |
|:------------------------------:|:----------------:|
| r1,g1,b1,a1,...,rn,gn,bn,an    | RGBA 8 bit       |
| r1,g1,b1,r2,g2,b2,...,rn,gn,bn | RGB 8 bit        |
| grey1,grey2,grey3, ..., greyn  | GREY 8 bit       |
| grey1,a1,grey2,a2,...,greyn,an | GREY ALPHA 8 bit |
