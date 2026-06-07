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
