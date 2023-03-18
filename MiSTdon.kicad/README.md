# MiSTdon - KiCAD project

MiSTdon is an addon for any FPGA to be nearly compatible with the [MiST FPGA](https://github.com/mist-devel/mist-board/wiki) platform. 

Project has been developed with KiCAD 6.0.11. 

### Status

STATUS (17/03/23):  first PCB assembled. After first testing, all seems to work well.  Some changes will be introduced in next revision (see TODO list below).

STATUS (25/02/23):  prototype design work finished. v0.7 gerbers sent to JLCPCB for manufacturing.

### Credits

Acknowledgements and thanks for help go to Jepalza, Volton and Manuferhi.

### **Schematic**

 [MiSTdon.pdf](MiSTdon.pdf) 

### **Features**

* Socket for AT91SAM7S256 board (MCUzone)
* Socket for USB Host Shield Mini (MAX3421E) 
* PMOD for SD card (Digilent)
* PMOD for FPGA interface (fits nicely in [Deca retro cape 2](https://github.com/somhi/DECA_retro_cape_2))
* Two DB9 joystick ports
* OSD and USER button for core, and pins for SW2 debug buttons
* Reset button for ARM, USB and FPGA (if used)
* UART debug pins
* Passive serial pins
* Power pins

See main repository [Readme](../README.md) for materials list



### **Important Usage notes**

* **Better do not connect/disconnect interfaces while the board is powered**

* **Read jumper selection below.**

  


### **Jumper Selection**

Defaults are OPEN (jumper not placed or not soldered) and CLOSED (jumper placed or soldered)

* JP1 jumper closed connects 5V to USB VBus (**make sure before place jumper to cut the R222 track in MAX3421E USB Mini shield**). In case of doubt do not place this jumper, so not all USB peripherals will work at 3V3 which is applied to VBUS when jumper is disconnected.

* JP2 solder jumper. Central position with GND for SD Cards without this signal. Central position with CD for Digilent Pmod SD card.  In case of doubt solder central position and GND.

* JP3 place jumper in RST pins (leave GND pin unconnected).

  

### **Power supply and Reset external wires**

Connect wires from 5V and GND pins of P7 header (AT91SAM7S256 board) to J6 header 5V and GND pins.

Connect wire from 5V FPGA pin to J6 header 5V pin.  

Connect wire from RST pin 3 of JTAG header (AT91SAM7S256 board) to J6 header NRST pin.





### 3D model

![DECA_retro_cape_1](1.png)



![DECA_retro_cape_2](2.png)



### Changelog

v0.7 finished prototype desing. Gerber sent to JLCPCB for manufacturing

v0.8 see changes below:

* change schematic text -> NRST WIRED EXTERNALLY TO SAM7S (JTAG PIN 3)
* R4 changed to 10K
* Move connector J6 to avoid collision with MAX3421E module
* Silkscreen changes: move MiSTdon text to a visible location, pmod sdcard gnd & 3V3, mark pin 1 in headers, notes to clarify orientation of MAX3421E and SAM7S









