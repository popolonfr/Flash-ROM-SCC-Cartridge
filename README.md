# Flash-ROM-SCC-Cartridge

## Description

Here you will find all the information concerning the [**Flash ROM SCC cartridge**](https://www.msx.org/wiki/Popolon-fr_Flash-ROM_SCC_Cartridge), from its use to the technical specifications for different models and their specific access points for memory and sound management.

* **Basic Version** (Dual)
  * Offset Register
  * 2 MB of flash memory
  * Konami SCC (Mapper & Sound Engine)
  * PCB compatible with the Konami SCC cartridge case

* **User version** (ROM collection)
  * Offset Register
  * 16 MB of flash memory
  * Konami SCC (Mapper & Sound Engine)
  * DAC (Konami compatible)
  * PCB compatible with the Konami SCC cartridge case

* **Developer version** (Large capacity game)
  * Extended 16-bit registers for the ROM Mapper
  * 16 MB of flash memory
  * Konami SCC (Mapper & Sound Engine)
  * DAC (Konami compatible)
  * PCB compatible with the Konami SCC cartridge case

## Details of proprietary registers

| REGISTER NAME        | MODE | ADDRESS(ES) |  B7 |  B6 |  B5 |  B4 |  B3 |  B2 |  B1 |  B0 |
| ---------------------| ---- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Offset\_L            |   w  |    3800h    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Offset\_H            |   w  |    3801h    |     |     | d13 | d12 | d11 | d10 |  d9 |  d8 |
| Offset    (ver 2 MB) |   w  |    3FFFh    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Control_Mode         |   w  |    3804h    |     |     |     |     |     |     |   M |   R |
| Knm_DAC_Out ⁽¹⁾      |   w  |    4000h    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Knm_DAC_Ctrl ⁽²⁾     |   w  |    98FBh    |     |     |     |   D |     |     |     |     |
| DAC_Output ⁽²⁾       |   w  |    98FCh    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
###### (1) The Konami DAC mode must be selected. (2) Segment 3Fh in page 9000h must be selected.

### Offset

The Offset register specifies the starting location in flash memory for the Mapper registers. Each increment corresponds to an 8 KB block. Therefore, when the Offset is set to 0, the Mapper registers start at address 0000h in flash memory; when it is set to 1, they start at address 2000h.

The Offset value is immediately added to all Mapper register values. If a value exceeding the size of the flash memory is assigned, the address pointer will wrap around to the beginning of the memory.

This register is only useful for ROM compilations, as it allows the position of a ROM within flash memory to be specified and maps the Mapper registers to the ROM's first segment.

The number of SCC Mapper segments accessible to the ROM remains limited to 256, making this feature unsuitable for large ROMs that require additional segments. In such cases, the extended 16-bit Mapper registers available in the developer version of the cartridge must be used.

Offset_L and Offset_H form a single register and are available only in the User and Developer versions of the cartridge. Bits d0 through d13 represent the number of an 8 KB segment and allow addressing of up to 128 MB of flash memory. The cartridge currently uses only 16 MB of flash memory. This register is accessible at addresses 3800h and 3801h and is mirrored from 3802h through 3FFFh.

Offset (2 MB version) is available only in the Basic version of the cartridge. Bits d0 through d7 represent the number of an 8 KB segment and allow addressing of up to 2 MB of flash memory. This register is accessible only at address 3FFFh.

### Control mode

Operating_Mode
