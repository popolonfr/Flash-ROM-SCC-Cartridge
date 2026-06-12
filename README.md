# Flash-ROM-SCC-Cartridge

## Description

Here you will find all the information concerning the [**Flash ROM SCC cartridge**](https://www.msx.org/wiki/Popolon-fr_Flash-ROM_SCC_Cartridge), from its use to the technical specifications for different models and their specific access points for memory and sound management.

* **Basic Edition**
  * Offset Register
  * 2 MB of flash memory
  * Konami SCC (Mapper & Sound Engine)
  * PCB compatible with the Konami SCC cartridge case

* **Multi-ROM Edition**
  * Offset Register
  * 16 MB of flash memory
  * Konami SCC (Mapper & Sound Engine)
  * Digital to Analog Converter (Konami compatible)
  * PCB compatible with the Konami SCC cartridge case

* **Game Edition** (Large-Capacity Game)
  * extended 16-bit Mapper registers for the ROM Mapper
  * 16 MB of flash memory
  * Konami SCC (Mapper & Sound Engine)
  * Digital to Analog Converter (Konami compatible)
  * PCB compatible with the Konami SCC cartridge case

* **Developer Edition**
  * Offset Register
  * extended 16-bit Mapper registers for the ROM Mapper
  * 16 MB of flash memory
  * Konami SCC (Mapper & Sound Engine)
  * Digital to Analog Converter (Konami compatible)
  * PCB compatible with the Konami SCC cartridge case

## Details of proprietary registers

| REGISTER NAME        | MODE | ADDRESS(ES) |  B7 |  B6 |  B5 |  B4 |  B3 |  B2 |  B1 |  B0 |
| ---------------------| ---- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Offset\_L            |   w  |    3800h    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Offset\_H            |   w  |    3801h    |     |     | d13 | d12 | d11 | d10 |  d9 |  d8 |
| Offset    (ver 2 MB) |   w  |    3FFFh    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Operating_Mode       |   w  |    3804h    |     |     |     |     |     |     |   M |   R |
| Knm_DAC_Data ⁽¹⁾     |   w  |    4000h    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Knm_DAC_Ctrl ⁽²⁾     |   w  |    98FBh    |     |     |     |   D |     |     |     |     |
| DAC_Data ⁽²⁾         |   w  |    98FCh    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
###### (1) The Konami DAC mode must be selected. (2) Segment 3Fh in page 9000h must be selected.

### Offset

The Offset register specifies the starting location in flash memory for the Mapper registers. Each increment corresponds to an 8 KB block. Therefore, when the Offset is set to **0**, the Mapper registers start at address 0000h in flash memory; when it is set to **1**, they start at address 2000h.

The Offset value is immediately added to all Mapper register values. If a value exceeding the size of the flash memory is assigned, the address pointer will wrap around to the beginning of the memory.

This register is only useful for ROM compilations, as it allows the position of a ROM within flash memory to be specified and maps the Mapper registers to the ROM's first segment.

The number of SCC Mapper segments accessible to the ROM remains limited to 256, making this feature unsuitable for large ROMs that require additional segments. In such cases, the extended 16-bit Mapper registers available in the developer and Game editions of the cartridge must be used.

Offset_L and Offset_H form a single register and are available only in the Developer, Game and Multi-ROM editions of the cartridge. Bits d0 through d13 represent the number of an 8 KB segment and allow addressing of up to 128 MB of flash memory. The cartridge currently uses only 16 MB of flash memory. This register is accessible at addresses 3800h and 3801h and is mirrored from 3802h through 3FFFh.

Offset (2 MB edition) is available only in the Basic edition of the cartridge. Bits d0 through d7 represent the number of an 8 KB segment and allow addressing of up to 2 MB of flash memory. This register is accessible only at address 3FFFh.

The default Offset value is **0**.

### Operating mode

This register controls the behavior of the mapper registers. It is implemented only in the Developer and Game cartridge editions. Currently, only bit **`M`** is used. Bit **`R`** is reserved for future use and is not described here.

* **Mapper in 8-bit mode**
  * When **`M`** = 0, the mapper operates in 8-bit mode, allowing up to 256 segments of 8 KB each. This mode is fully compatible with the Konami SCC Mapper. A segment is assigned to one of the four pages by writing an 8-bit value to the register associated with that page.

    | PAGE | ADRESS | MIRROR RANGE |
    | ---- | ------ | ------------ |
    | 0    | 5000h  | 5000h–57FFh  |
    | 1    | 7000h  | 7000h–77FFh  |
    | 2    | 9000h  | 9000h–97FFh  |
    | 3    | B000h  | B000h–B7FFh  |

* **Mapper in 16-bit mode.**
  * When M = 1, the mapper operates in 16-bit mode, allowing up to 16,384 segments of 8 KB each. A segment is assigned to one of the four pages by writing a 16-bit value to the register associated with that page.

    | PAGE | ADRESSES     | MIRROR RANGE |
    | ---- | ------------ | ------------ |
    | 0    | 5000h~5001h  | 5000h–57FFh  |
    | 1    | 7000h~7001h  | 7000h–77FFh  |
    | 2    | 9000h~9001h  | 9000h–97FFh  |
    | 3    | B000h~B002h  | B000h–B7FFh  |

The default value for **`M`** is **0**.

Although the hardware initializes the four mapper pages with segments 0, 1, 2, and 3, ROMs designed for this mapper must still perform their own initialization. This is because, when the computer is powered on or reset, the BIOS scans each slot for memory and expansion devices. During this process, it may modify the cartridge's mapper registers by writing to them. Therefore, software must initialize the mapper before relying on its contents or operating mode.

## Digital-to-Analog Converter (DAC)

The cartridge includes an 8-bit unsigned DAC controlled through a single register, to which each byte must be written manually at the desired frequency. There are two methods of use: the first is standard, while the second exists only for compatibility with Konami ROMs.

* **DAC_Data**
  * Writing an 8-bit unsigned value to this register sends it to the DAC. This register resides in the same address space as the SCC registers; to access it, segment 3Fh must first be mapped to page 2.
