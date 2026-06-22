# Flash-ROM-SCC-Cartridge

Here you will find all the information concerning the [**Flash ROM SCC cartridge**](https://www.msx.org/wiki/Popolon-fr_Flash-ROM_SCC_Cartridge), from its use to the technical specifications for different models and their specific access points for memory and sound management.

## Description

This cartridge provides 2 MB, 16 MB, or more of Flash memory expansion, an advanced memory mapping system, an 8-bit audio DAC, and full compatibility with the Konami SCC sound chip. It is designed for use with standard MSX computers.

It can be used to store multiple ROMs or MegaROMs in a single cartridge with a convenient selection menu, or as a platform for next-generation games requiring large amounts of memory.

Two free software tools are included. The first allows you to create multi-ROM cartridges with an integrated selection menu. The second allows you to load, convert, and program standard ROM images onto the cartridge.

* [**Roms-Collection-Maker**](https://github.com/popolonfr/Roms-Collection-Maker)
* [**FLX**](https://github.com/popolonfr/FLX)

The cartridge is available in the following editions:

* **Basic Edition**
  * Offset Register
  * 2 MB of Flash memory
  * Konami SCC (mapper and sound engine)
  * Capacitive touch button (prevents the ROM from booting at startup)
  * PCB compatible with the Konami SCC cartridge case

* **Multi-ROM Edition**
  * Offset Register
  * 16 MB of Flash memory
  * Konami SCC (mapper and sound engine)
  * 8-bit Digital-to-Analog Converter (Konami-compatible)
  * Capacitive touch button (prevents the ROM from booting at startup)
  * PCB compatible with the Konami SCC cartridge case

* **Game Edition** (Large-Capacity Game)
  * Extended 16-bit mapper registers
  * 16 MB of Flash memory
  * Konami SCC (mapper and sound engine)
  * 8-bit Digital-to-Analog Converter (Konami-compatible)
  * PCB compatible with the Konami SCC cartridge case

* **Developer Edition**
  * Offset Register
  * Extended 16-bit mapper registers
  * 16 MB of Flash memory
  * Konami SCC (mapper and sound engine)
  * 8-bit Digital-to-Analog Converter (Konami-compatible)
  * Capacitive touch button (prevents the ROM from booting at startup)
  * PCB compatible with the Konami SCC cartridge case

### Minimum requirements
 * MSX1 computer with at least 8 kB of RAM
 * One free cartridge slot (the -/+12 V lines are not used)
 * Disk drive

 * **Loader Requirements**
   * MSX-DOS 1/2: 64 kB RAM minimum
   * MSX-DOS 2 or Nextor: 128 kB RAM minimum

### Capacitive Touch Button

Touching the upper-left corner of the cartridge with your index finger while the computer is starting up prevents the cartridge's contents from booting. This allows you to erase or replace the contents by loading a different ROM, or to use the audio hardware independently of the ROM's contents.

This function disables access to the Flash memory while the touch button is held. However, once a ROM loaded in the cartridge is running, the touch function is ignored until the system is reset.

### How to Use It

To program a ROM image into the cartridge, you will need an MSX computer with at least 64 KB of RAM and a storage device (floppy disk, hard disk, etc.) containing a bootable MSX-DOS 1 or MSX-DOS 2 system, the ROM image file to be programmed, and the ROM loader [**FLX**](https://github.com/popolonfr/FLX).

Insert the [**Flash ROM SCC cartridge**](https://www.msx.org/wiki/Popolon-fr_Flash-ROM_SCC_Cartridge) into the slot. Turn on the computer and wait for MSX-DOS to boot. At the MSX-DOS prompt, type the ROM loader command followed by the name of the ROM image file to be loaded, then press Enter to begin the programming process.

```
A:> FLX IMG-FILE.ROM
```

To erase the cartridge's Flash memory, use the command with the /E option alone.

### Detect the cartridge

The simplest method for a program is to use the [**Autoselect**](https://github.com/popolonfr/Flash-ROM-SCC-Cartridge#autoselect) command to check the Flash memory identifier. For further refinement, the SCC [**Waveform**](https://github.com/popolonfr/Flash-ROM-SCC-Cartridge#waveform-control) registers and the [**Knm_DAC_Ctrl**](https://github.com/popolonfr/Flash-ROM-SCC-Cartridge#digital-to-analog-converter-dac) register may be tested, as bit 4 disables access to Flash memory commands when set.


## Register access

To read from or write to a cartridge register, you must first select the slot containing the cartridge. Once the slot is selected, the register can be accessed by reading from or writing to its address.

Registers below 4000h are typically accessed through the BIOS inter-slot routines, as the BIOS is usually mapped in this address range. Registers above 4000h, however, can be accessed directly after the slot has been selected, eliminating the need for repeated inter-slot operations.

### Proprietary register

These are the registers specific to the cartridge, as well as those that extend the original hardware functionality, such as the SCC mapper.

#### ROM mapper

| REGISTER NAME        | MODE | ADRESS IN 8-BIT MODE | ADRESSES IN 16-BIT MODE | MIRROR RANGE |
| ---------------------| ---- | -------------------- | ----------------------- | ------------ |
| Page_0               |   w  | 5000h                | 5000h~5001h             | 5000h–57FFh  |
| Page_1               |   w  | 7000h                | 7000h~7001h             | 7000h–77FFh  |
| Page_2               |   w  | 9000h                | 9000h~9001h             | 9000h–97FFh  |
| Page_3               |   w  | B000h                | B000h~B002h             | B000h–B7FFh  |

The cartridge uses a Konami SCC mapper, providing access to up to 2 MB of ROM through 8 KB segments. Four independent 8 KB memory pages can be mapped, each selecting one segment from the ROM. Because the mapper uses 8-bit page registers, only 256 segments (2 MB) can be addressed. To overcome this limitation, and the limited flexibility of the offset, a cartridge-specific 16-bit mode was added to the Game Edition and Developer Edition. This mode extends the mapper's registers (see the "Operating Mode" section).

Although the hardware initializes the four mapper pages with segments 0, 1, 2, and 3, ROMs designed for this mapper must still perform their own initialization. When the computer is powered on or reset, the BIOS scans each slot for memory and expansion devices. During this process, it may modify the cartridge's mapper registers by writing to them, with page_2 being particularly affected on some MSX models. Therefore, software must initialize the mapper before relying on its register contents or operating mode.

The default values ​​are `Page_0` = **0**, `Page_1` = **1**, `Page_2` = **2**, `Page_3` = **3**.

#### Offset

| REGISTER NAME        | MODE | ADDRESS     |  B7 |  B6 |  B5 |  B4 |  B3 |  B2 |  B1 |  B0 |
| ---------------------| ---- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Offset\_L            |   w  |    3800h    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Offset\_H            |   w  |    3801h    |     |     | d13 | d12 | d11 | d10 |  d9 |  d8 |
| Offset    (ver 2 MB) |   w  |    3FFFh    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |

The Offset register specifies the starting location in Flash memory for the Mapper registers. Each increment corresponds to an 8 KB block. Therefore, when the Offset is set to **0**, the Mapper registers start at address 0000h in Flash memory; when it is set to **1**, they start at address 2000h.

The Offset value is immediately added to all Mapper register values. If a value exceeding the size of the Flash memory is assigned, the address pointer will wrap around to the beginning of the memory.

This register is only useful for ROM compilations, as it allows the position of a ROM within Flash memory to be specified and maps the Mapper registers to the ROM's first segment.

The number of SCC Mapper segments accessible to the ROM remains limited to 256, making this feature unsuitable for large ROMs that require additional segments. In such cases, the extended 16-bit Mapper registers available in the developer and Game editions of the cartridge must be used.

Offset_L and Offset_H form a single register and are available only in the Developer, Game and Multi-ROM editions of the cartridge. Bits d0 through d13 represent the number of an 8 KB segment and allow addressing of up to 128 MB of Flash memory. The cartridge currently uses only 16 MB of Flash memory. This register is accessible at addresses 3800h and 3801h and is mirrored from 3802h through 3FFFh.

Offset (2 MB edition) is available only in the Basic edition of the cartridge. Bits d0 through d7 represent the number of an 8 KB segment and allow addressing of up to 2 MB of Flash memory. This register is accessible only at address 3FFFh.

The default Offset value is **0**.

#### Operating mode

| REGISTER NAME        | MODE | ADDRESS     |  B7 |  B6 |  B5 |  B4 |  B3 |  B2 |  B1 |  B0 |
| ---------------------| ---- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Operating_Mode       |   w  |    3804h    |     |     |     |     |     |     |   M |   R |

This register controls the behavior of the mapper registers. It is implemented only in the Developer and Game cartridge editions. Currently, only bit **`M`** is used. Bit **`R`** is reserved for future use and is not described here.

* **Mapper in 8-bit mode**
  * When **`M`** = **0**, the mapper operates in 8-bit mode, allowing up to 256 segments of 8 KB each. This mode is fully compatible with the Konami SCC Mapper. A segment is assigned to one of the four pages by writing an 8-bit value to the register associated with that page.

  * **Register size 1 byte**
    * 5000h
    * 7000h
    * 9000h
    * B000h 

* **Mapper in 16-bit mode.**
  * When  **`M`** = **1**, the mapper operates in 16-bit mode, allowing up to 16,384 segments of 8 KB each. A segment is assigned to one of the four pages by writing a 16-bit value to the register associated with that page.

  * **Register size 2 bytes**
    * 5000h~5001h
    * 7000h~7001h
    * 9000h~9001h
    * B000h~B002h

The default value for **`M`** is **0**.

#### Digital-to-analog converter (DAC)

| REGISTER NAME        | MODE | ADDRESS     |  B7 |  B6 |  B5 |  B4 |  B3 |  B2 |  B1 |  B0 |
| ---------------------| ---- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Knm_DAC_Data ⁽¹⁾     |   w  |    4000h    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Knm_DAC_Ctrl ⁽²⁾     |   w  |    98FBh    |     |     |     |   D |     |     |     |     |
| DAC_Data ⁽²⁾         |   w  |    98FCh    |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |

<sub>(1) The Konami DAC mode must be selected. (2) Segment 3Fh in page 9000h must be selected.</sub>

The cartridge includes an 8-bit unsigned DAC controlled through a single register, to which each byte must be written manually at the desired frequency. There are two methods of use: the first is standard, while the second exists only for compatibility with Konami ROMs.

* **DAC_Data**
  * Writing an 8-bit unsigned value to this register sends it to the DAC. This register resides in the same address space as the SCC registers; to access it, segment 3Fh must first be mapped to page 2.

* **Knm_DAC_Ctrl**
  * When **`D`** = **1**, the Konami-type DAC is enabled. Address 4000h (mirrored through 4FFFh) is then reserved for sample writes. Konami ROMs that write samples to address 5000h must be patched to use address 4000h instead. While the DAC is enabled, the 4000h–4FFFh address range is unavailable for Flash memory commands. This register resides in the same address space as the SCC registers; to access it, segment 3Fh must first be mapped to page 2.

    All other bits are reserved and must be set to **0**.

* **Knm_DAC_Data**
  * Writing an 8-bit unsigned value to this register sends it to the DAC. This register is available only when the Konami-type DAC is enabled (see the Knm_DAC_Ctrl register).

### SCC audio registers

This section briefly describes the registers of the sound engine, an SCC-compatible sound processor developed by Konami. To access it, segment 3Fh must first be mapped to page 2.

The SCC (Sound Creative Chip) is a 5-channel wavetable sound generator in which each channel plays a 32-byte 8-bit waveform stored in RAM, with frequency controlled by a 12-bit pitch register.

#### Waveform control

| REGISTER NAME        | MODE | ADDRESSES   |  B7 |  B6 |  B5 |  B4 |  B3 |  B2 |  B1 |  B0 |
| ---------------------| ---- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Waveform_Ch1         | r/w  | 9800h~981Fh |  S  |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Waveform_Ch2         | r/w  | 9820h~983Fh |  S  |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Waveform_Ch3         | r/w  | 9840h~985Fh |  S  |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Waveform_Ch4 ⁽³⁾     | r/w  | 9860h~987Fh |  S  |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |

<sub>(3) Channel 5 shares the waveform of channel 4.</sub>

These registers store the 32-byte waveform played by each channel. Sample data is stored as 8-bit signed two's-complement values.

When **`S`** = **1**, the sample value is negative. For example, 80h = -128 and FFh = -1.

#### Frequency control

| REGISTER NAME        | MODE | ADDRESS     |  B7 |  B6 |  B5 |  B4 |  B3 |  B2 |  B1 |  B0 |
| ---------------------| ---- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Frequency_Ch1_L      |   w  |   9880h     |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Frequency_Ch1_H      |   w  |   9881h     |     |     |     |     | d11 | d10 |  d9 |  d8 |
| Frequency_Ch2_L      |   w  |   9882h     |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Frequency_Ch2_H      |   w  |   9883h     |     |     |     |     | d11 | d10 |  d9 |  d8 |
| Frequency_Ch3_L      |   w  |   9884h     |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Frequency_Ch3_H      |   w  |   9885h     |     |     |     |     | d11 | d10 |  d9 |  d8 |
| Frequency_Ch4_L      |   w  |   9886h     |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Frequency_Ch4_H      |   w  |   9887h     |     |     |     |     | d11 | d10 |  d9 |  d8 |
| Frequency_Ch5_L      |   w  |   9888h     |  d7 |  d6 |  d5 |  d4 |  d3 |  d2 |  d1 |  d0 |
| Frequency_Ch5_H      |   w  |   9889h     |     |     |     |     | d11 | d10 |  d9 |  d8 |

These registers define the playback frequency of each channel via a 12-bit tone period value (TP) stored in bits **`d0`**~**`d11`**; a higher TP results in a lower output frequency, while a lower TP produces a higher pitch.

* The output frequency is derived from the system clock as follows:

  * $f_{tone} = \frac{3579545}{32 \times (TP + 1)}$

* Conversely, the tone period value can be calculated from a desired frequency:

  * $TP = \left(\frac{3579545}{32 \times f_{tone}}\right) - 1$

3,579,545 Hz is the frequency of the Z80 and 32 is the number of bytes in the sample.

#### Volume control

| REGISTER NAME        | MODE | ADDRESS     |  B7 |  B6 |  B5 |  B4 |  B3 |  B2 |  B1 |  B0 |
| ---------------------| ---- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Volume_Ch1           |   w  |   988Ah     |     |     |     |     |  d3 |  d2 |  d1 |  d0 |
| Volume_Ch2           |   w  |   988Bh     |     |     |     |     |  d3 |  d2 |  d1 |  d0 |
| Volume_Ch3           |   w  |   988Ch     |     |     |     |     |  d3 |  d2 |  d1 |  d0 |
| Volume_Ch4           |   w  |   988Dh     |     |     |     |     |  d3 |  d2 |  d1 |  d0 |
| Volume_Ch5           |   w  |   988Eh     |     |     |     |     |  d3 |  d2 |  d1 |  d0 |

These registers control the output amplitude of each channel using a 4-bit volume value.

When **`d0`**~**`d3`** = **15**, the volume is at its maximum , and when it = **`0`**, it is silent.

#### Channel control

| REGISTER NAME        | MODE | ADDRESS     |  B7 |  B6 |  B5 |  B4 |  B3 |  B2 |  B1 |  B0 |
| ---------------------| ---- | ----------- | --- | --- | --- | --- | --- | --- | --- | --- |
| Enable_Channel       |   w  |   988Fh     |     |     |     |  C5 |  C4 |  C3 |  C2 |  C1 |

This register enables or disables the output of individual channels.

When **`C`** = **1**, the channel is activated.

## Flash Memory and Command Set

Flash memory is organized into a specified number of sectors, each with a defined size, depending on the memory type. It provides a set of commands for programming data, erasing individual sectors or the entire chip, and accessing status registers. Flash memory requires an erase operation before any write operation.

### 2 MB flash ROM

|                      | MODE | ADDRESS     |  DATA |
| ---------------------| ---- | ----------- | ----- |
| Flash_CMD_1st        |   w  |   4555h     |  CMD  |
| Flash_CMD_2nd        |   w  |   42AAh     |  CMD  |
| Manufacturer_ID      |   r  |   4000h     |  01h  |
| Device_ID            |   r  |   4001h     |  ADh  |


The device is organized into 32 sectors of 64 KB each.

### 16 MB flash ROM

|                      | MODE | ADDRESS     |  DATA |
| ---------------------| ---- | ----------- | ----- |
| Flash_CMD_1st        |   w  |   4AAAh     |  CMD  |
| Flash_CMD_2nd        |   w  |   4555h     |  CMD  |
| Manufacturer_ID      |   r  |   4000h     |  20h  |
| Device_ID1           |   r  |   4002h     |  7Eh  |
| Device_ID2           |   r  |   401Ch     |  21h  |
| Device_ID3           |   r  |   401Eh     | 0h/1h |

The device is organized into 128 sectors of 128 KB each.

### Commands

To execute a command, a sequence of data must be written alternately to two command addresses (Flash_CMD: 1st, 2nd). These addresses depend on the memory type.

| COMMAND       | 1st | 2nd | 1st | 2nd      | 1st | 2nd     |
| ------------- | --- | --- | --- | -------- | --- | ------- |
| Reset/Read    | F0H |     |     |          |     |         |
| Reset/Read    | AAH | 55H | F0H | RD ⁽⁴⁾   |     |         |
| Autoselect    | AAH | 55H | 90H |          |     |         |
| Byte Program  | AAH | 55H | A0H | DATA ⁽⁴⁾ |     |         |
| Chip Erase    | AAH | 55H | 80H | AAH      | 55H | 10H ⁽⁴⁾ |
| Sector Erase  | AAH | 55H | 80H | AAH      | 55H | 30H ⁽⁴⁾ |
| Erase Suspend | B0H |     |     |          |     |         |
| Erase Resume  | 30H |     |     |          |     |         |
 
<sub>(4) At this stage, the memory address or sector to be accessed must be selected before reading or writing.</sub>

#### Reset/Read
There are two ways to achieve the same result: a short version and a long version. The short version is sufficient in most cases: writing F0h exits Status mode and returns the device to Read mode. The long version requires issuing an extended command sequence that includes the address of the memory location to be read.

#### Autoselect
This command allows you to retrieve the manufacturer code, device code, and sector protection status. This information replaces the Flash memory contents and remains accessible until a Reset/Read command is issued.

#### Byte program
Programs a byte of Flash memory (the target byte must be erased beforehand). A program command must be issued for each byte written. Since the write address space overlaps with the Flash memory segment selection register, this register must first be used to select the target segment. The program command must then be reissued for each subsequent byte write, and the original segment selection must be restored immediately afterward. This behavior is specific to the cartridge hardware.

#### Chip erase
Erases the entire memory array except for protected sectors. If an error occurs during the operation, reads from the Flash memory will return status information instead of memory contents until a Reset/Read command is issued.

#### Sector erase 
Erases a sector. Additional sectors can be erased by repeating the last write cycle of the command for each sector address. If an error occurs during the operation, reading from the Flash memory will return status information instead of the memory contents until a Reset/Read command is issued.

#### Erase suspend
Temporarily suspends an ongoing sector erase operation. While the erase is suspended, data can be read from or programmed to other sectors. The erase operation can later be resumed using the Erase Resume command (30h).

This command is only valid during a sector erase operation.

#### Erase resume
Resumes a sector erase operation previously suspended with the Erase Suspend command. The device must be in Read mode for this command to be accepted.

### Status register

The status register provides a set of bits indicating the current operation status and the result of program and erase operations. During program and erase operations, bus read operations from any address return the status register instead of the Flash memory contents. It is also read during erase suspend when accessing a block being erased. To access the Flash memory array again, issue a Reset/Read command to return the device to normal read mode.

* **DQ7** (Data Polling bit)
  * DQ7 is used to monitor the progress of program and erase operations. During a program operation, DQ7 outputs the complement of the data being written. When the operation completes, it returns the actual data stored at the programmed address. During an erase operation, DQ7 outputs “0”. After completion, it returns “1”, reflecting the erased state. During erase suspend, DQ7 changes from “0” to “1”, indicating that the erase operation has been suspended. In this mode, reading a block being erased requires the correct address to observe DQ7.
 
* **DQ6** (Toggle bit)
  * DQ6 indicates whether a program or erase operation is still in progress. During a program or erase operation, DQ6 toggles between “0” and “1” on successive read operations, regardless of the address. When the operation completes, DQ6 stops toggling and remains stable, allowing valid data to be read. During erase suspend, DQ6 may continue to toggle when reading a block that is still being erased, and stops toggling once the erase controller is suspended.

* **DQ5** (Error / failure bit)
  * DQ5 indicates a failure condition during program or erase operations. It is set to “1” if the operation fails to complete correctly, for example when timing limits are exceeded or when an invalid program attempt is made (such as trying to set a bit from “0” back to “1”). When DQ5 is set, the device may not complete the operation, and Data Polling (DQ7/DQ6) may no longer indicate valid results. In this case, a Reset/Read command is required before issuing new commands.

* **DQ3** (Erase timer bit)
  * DQ3 indicates the status of the erase command window. During a block erase sequence, DQ3 remains “0” while the device is still accepting additional erase commands. Once the internal erase operation begins, DQ3 is set to “1”, indicating that the erase cycle has started and no further erase commands will be accepted. DQ3 can be used to confirm whether additional blocks may still be added to the current erase operation.

* **DQ2** (Alternative Toggle bit)
  * DQ2 is used together with DQ6 to monitor erase operations and erase suspend states. During an erase operation, DQ2 toggles between “0” and “1” when reading from sectors being erased. When the erase operation completes, DQ2 stops toggling. During erase suspend, DQ2 continues to toggle when reading from the suspended sector, while it remains stable (logic “1”) when reading from sectors not affected by erase suspend. DQ2 can also be used to identify which sector is currently being erased or has failed an erase operation, as only affected sectors will show toggling behavior.

* **DQ1** (Buffered program abort bit)
  * DQ1 indicates an abort condition during buffered program operations. It is set to “1” when a buffered program or enhanced buffered program operation fails or is aborted. In this case, the device must be reset using the Buffered Program Abort and Reset command before normal read or command operations can resume.

<sub>Note: Applies only to 2 MB memory.</sub>
  
© 2026 popolon-fr
