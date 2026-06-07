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
| Operating_Mode       |   w  |    3804h    |     |     |     |     |     |     |   M |   R |
| **Kon_DAC_Out** ¹    |   w  |    4000h    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| **Kon_DAC_Ctrl** ²   |   w  |    98FBh    |     |     |     |   D |     |     |     |     |
| **DAC_Output** ²     |   w  |    98FCh    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
###### (1) The Konami DAC mode must be selected. (2) Segment 3Fh in page 9000h must be selected.

### Offset

The Offset is a register used to indicate the starting point in flash memory for the Mapper registers. Its step size is 8KB. Thus, when the Offset is 0, the Mapper registers will start at address 0000h of the flash memory, but when it is 1, the Mapper registers will start at address 2000h of the flash memory.

It immediately affects all the mapper's registers by summing them, and if you assign a value that exceeds the size of the flash memory, the pointer will loop back to the beginning of the memory.

This register is only useful for ROM collections because it allows you to specify the ROM's position in memory and will map the mapper's registers to the first segment of the ROM.

The number of SCC Mapper segments usable by the Rom will remain 256 and will not be suitable for large Roms which need more segments; in this case, it will be necessary to use the 16-bit register mode of the Mapper from the developer version of the cartridge.

**Offset_L** and **Offset_H** form a single register and are only present on the user and developer versions of the cartridge. Bits d0 to d13 represent the number of an 8 KB segment and allow manipulation of up to 128 MB of flash memory. Currently, the cartridge only uses 16 MB of memory. This register is accessible at address 3800h~3801h but is mirrored from 3802h to 3FFFh.

**Offset (ver 2 MB)** is only present on the basic version of the cartridge. Bits d0 to d7 represent the number of an 8 KB segment and allow manipulation of up to 2 MB of flash memory. This register is only accessible at address 3FFFh.

