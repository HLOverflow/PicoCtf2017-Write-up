# Meta Find Me [ 70 Pts ]

## Problem

Find the location of the flag in the image: [image.jpg](./image.jpg). Note: Latitude and longitude values are in degrees with no degree symbols,/direction letters, minutes, seconds, or periods. They should only be digits. The flag is not just a set of coordinates - if you think that, keep looking!

## Approach

Meta data can be extracted easily using a exiftool.

```
XXXXXXX: ~$ exiftool image.jpg 

ExifTool Version Number         : 10.10
File Name                       : image.jpg
Directory                       : .
File Size                       : 43 kB
File Modification Date/Time     : 2017:04:01 07:11:10+08:00
File Access Date/Time           : 2018:06:22 00:30:47+08:00
File Inode Change Date/Time     : 2018:06:22 00:30:47+08:00
File Permissions                : rw-rw-r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
Exif Byte Order                 : Big-endian (Motorola, MM)
Orientation                     : Horizontal (normal)
X Resolution                    : 72
Y Resolution                    : 72
Resolution Unit                 : inches
Software                        : Adobe Photoshop CS6 (Windows)
Modify Date                     : 2016:02:10 11:55:56
Color Space                     : sRGB
Exif Image Width                : 500
Exif Image Height               : 500
GPS Version ID                  : 2.3.0.0
GPS Latitude                    : 88 deg 0' 0.00"
GPS Longitude                   : 4 deg 0' 0.00"
Compression                     : JPEG (old-style)
Thumbnail Offset                : 414
Thumbnail Length                : 6989
IPTC Digest                     : 00000000000000000000000000000000
Displayed Units X               : inches
Displayed Units Y               : inches
Global Angle                    : 120
Global Altitude                 : 30
Photoshop Thumbnail             : (Binary data 6989 bytes, use -b option to extract)
Photoshop Quality               : 9
Photoshop Format                : Progressive
Progressive Scans               : 3 Scans
XMP Toolkit                     : Adobe XMP Core 5.3-c011 66.145661, 2012/02/06-14:56:27
Creator Tool                    : Adobe Photoshop CS6 (Windows)
Create Date                     : 2016:02:10 11:55:56-05:00
Metadata Date                   : 2016:02:10 11:55:56-05:00
Instance ID                     : xmp.iid:EF8C102717D0E511933AD0D375780F3A
Document ID                     : xmp.did:EE8C102717D0E511933AD0D375780F3A
Original Document ID            : xmp.did:EE8C102717D0E511933AD0D375780F3A
Format                          : image/jpeg
Color Mode                      : RGB
ICC Profile Name                : sRGB IEC61966-2.1
History Action                  : created, saved
History Instance ID             : xmp.iid:EE8C102717D0E511933AD0D375780F3A, xmp.iid:EF8C102717D0E511933AD0D375780F3A
History When                    : 2016:02:10 11:55:56-05:00, 2016:02:10 11:55:56-05:00
History Software Agent          : Adobe Photoshop CS6 (Windows), Adobe Photoshop CS6 (Windows)
History Changed                 : /
Profile CMM Type                : Lino
Profile Version                 : 2.1.0
Profile Class                   : Display Device Profile
Color Space Data                : RGB
Profile Connection Space        : XYZ
Profile Date Time               : 1998:02:09 06:49:00
Profile File Signature          : acsp
Primary Platform                : Microsoft Corporation
CMM Flags                       : Not Embedded, Independent
Device Manufacturer             : IEC
Device Model                    : sRGB
Device Attributes               : Reflective, Glossy, Positive, Color
Rendering Intent                : Media-Relative Colorimetric
Connection Space Illuminant     : 0.9642 1 0.82491
Profile Creator                 : HP
Profile ID                      : 0
Profile Copyright               : Copyright (c) 1998 Hewlett-Packard Company
Profile Description             : sRGB IEC61966-2.1
Media White Point               : 0.95045 1 1.08905
Media Black Point               : 0 0 0
Red Matrix Column               : 0.43607 0.22249 0.01392
Green Matrix Column             : 0.38515 0.71687 0.09708
Blue Matrix Column              : 0.14307 0.06061 0.7141
Device Mfg Desc                 : IEC http://www.iec.ch
Device Model Desc               : IEC 61966-2.1 Default RGB colour space - sRGB
Viewing Cond Desc               : Reference Viewing Condition in IEC61966-2.1
Viewing Cond Illuminant         : 19.6445 20.3718 16.8089
Viewing Cond Surround           : 3.92889 4.07439 3.36179
Viewing Cond Illuminant Type    : D50
Luminance                       : 76.03647 80 87.12462
Measurement Observer            : CIE 1931
Measurement Backing             : 0 0 0
Measurement Geometry            : Unknown
Measurement Flare               : 0.999%
Measurement Illuminant          : D65
Technology                      : Cathode Ray Tube Display
Red Tone Reproduction Curve     : (Binary data 2060 bytes, use -b option to extract)
Green Tone Reproduction Curve   : (Binary data 2060 bytes, use -b option to extract)
Blue Tone Reproduction Curve    : (Binary data 2060 bytes, use -b option to extract)
DCT Encode Version              : 100
APP14 Flags 0                   : [14]
APP14 Flags 1                   : (none)
Color Transform                 : YCbCr
Comment                         : "Your flag is flag_2_meta_4_me_<lat>_<lon>_06c3. Now find the GPS coordinates of this image! (Degrees only please)"
Image Width                     : 500
Image Height                    : 500
Encoding Process                : Progressive DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:4:4 (1 1)
Image Size                      : 500x500
Megapixels                      : 0.250
Thumbnail Image                 : (Binary data 6989 bytes, use -b option to extract)
GPS Position                    : 88 deg 0' 0.00", 4 deg 0' 0.00"
```

The flag is flag_2_meta_4_me_88_4_06c3

(Completed)
