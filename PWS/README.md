# AnyCubic Photon S - PWS File Format

## Introduction

AnyCubic introduced a new PWS File Format with printer firmware version P_V341 and slicing software Photon_WorkShop_V2.0.23 in early August 2019.
This document records the results of trying to reverse engineer the new file format.

## File Structure

The binary file seems to be divided into five sections FileHeader, Header, Preview, LayerDef each marked with a magic text value; and then the RLE encoded layer image data.

The text values used for section magics could be 12 bytes with zero byte padding, or 8 bytes with zero padded and a zero 4 byte value.
Integer values have little-endian byte order.

Note: Fields with unknown usage are named and numbered from the offset in bytes from the beginning of the section magic value.

| Name | Length | Type | Value from  R_E_R_F.pws| Note |
| --- | --- | --- | --- | --- |
| FileHeaderMagic | 12 | Text | ANYCUBIC | |
| FileHeaderUnknown12 | 4 | Unknown | 0x01000000 | |
| FileHeaderUnknown16 | 4 | Unknown | 0x04000000 | |
| FileHeaderUnknown20 | 4 | Unknown | 0x30000000 | |
| FileHeaderUnknown24 | 4 | Unknown | 0x00000000 | |
| FileHeaderUnknown28 | 4 | Unknown | 0x90000000 | |
| FileHeaderUnknown32 | 4 | Unknown | 0x00000000 | |
| FileHeaderUnknown36 | 4 | Unknown | 0xac260100 | |
| FileHeaderUnknown40 | 4 | Unknown | 0x00000000 | |
| FileHeaderUnknown44 | 4 | Unknown | 0xc04a0100 | |
| HeaderMagic | 12 | Text | HEADER | |
| HeaderLength | 4 | Int | 80 | |
| HeaderUnknown16 | 4 | Unknown | 0x00003d42 | |
| HeaderUnknown20 | 4 | Unknown | 0xcdcc4c3d | |
| HeaderUnknown24 | 4 | Unknown | 0x00008040 | |
| HeaderUnknown28 | 4 | Unknown | 0x0000803f | |
| HeaderUnknown32 | 4 | Unknown | 0x00007042 | |
| HeaderUnknown36 | 4 | Unknown | 0x00004040 | |
| HeaderUnknown40 | 4 | Unknown | 0x0000c040 | |
| HeaderUnknown44 | 4 | Unknown | 0x00004040 | |
| HeaderUnknown48 | 4 | Unknown | 0x00004040 | |
| HeaderUnknown52 | 4 | Unknown | 0x531bac41 | |
| HeaderUnknown56 | 4 | Unknown | 0x01000000 | |
| HeaderLayerX | 4 | Int | 1440 | |
| HeaderLayerY | 4 | Int | 2560 | |
| HeaderUnknown68 | 4 | Unknown | 0x531bac41 | |
| HeaderUnknown72 | 4 | Unknown | 0x531bac41 | |
| HeaderUnknown76 | 4 | Unknown | 0x00000000 | |
| HeaderUnknown80 | 4 | Unknown | 0x00000000 | |
| HeaderUnknown84 | 4 | Unknown | 0x00000000 | |
| HeaderUnknown88 | 4 | Unknown | 0x00000000 | |
| HeaderUnknown92 | 4 | Unknown | 0x00000000 | |
| PreviewMagic | 12 | Text | PREVIEW | |
| PreviewLength | 4 | Int | 75276 | |
| PreviewWidth | 4 | Int | 224 | |
| PreviewUnknown20 | 4 | Unknown | 0x2a000000 | |
| PreviewHeight | 4 | Int | 168 | |
| PreviewImage | PreviewWidth * PreviewHeight * 2 | Image | | 16-bit RGB565 or RGB555 color image. |
| LayerdefMagic | 12 | Text | LAYERDEF | |
| LayerdefLength | 4 | Int | 9220 | |
| LayerdefCount | 4 | Int | 288 | |
| Layerdef | LayerdefCount * 32 | Binary | 0xc04a01005c8900000000c0400000404000007042000000000000000000000000 0x1cd401005c8900000000c0400000404000007042000000000000000000000000 ... | TODO |
| LayerImages | Variable | Binary | 0x7d7d7d7d7d7d7d7d7d7d7d7d7d7d7d7d ... | Bit-level RLE (0x7d gives 125 zero bits and 0xfd gives 125 one bits).   Decode HeaderLayerX bits to produce a layer image line then repeat HeaderLayerY times to produce a 2-bit layer image. |

## Tools Used

AnyCubic has only released Windows and Mac builds of it's new slicer, and the Windows build of Photon_WorkShop_V2.0.23 crashes when trying to use it with Wine on Linux.
Vim and bvi were used for some initial exploration of the R_E_R_F.pws file.
The trial version of [Hexinator](https://hexinator.com/) helped with identifying some of the interdependency between sections.
A simple Java program was written to dump the file contents and extract preview and layer images.

## TODO

* Get more sample pws files with known settings.
* Reverse engineer Layerdef values.
* Maybe release Java dump tool.

