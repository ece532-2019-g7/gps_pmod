# Using the GPS PMOD

This file contains some tips on using the GPS PMOD.


## Integrating the GPS PMOD Into Your Project

To make use of the GPS PMOD in your own project, follow these steps:

1. Download the latest version of [Digilent's Vivado Library](https://github.com/Digilent/vivado-library)
2. Install the [Digilent Board Support Files](https://github.com/Digilent/vivado-boards) if you haven't already done so
3. Launch Vivado and create a new project
    - Don't add any sources
    - Be sure to select your board in the project creation wizard
4. Add the Digilent Vivado Library to the list of IP repositories used by the project
5. Create a new block design
6. Build a basic MicroBlaze design
    - You can use the instructions located [here](https://reference.digilentinc.com/vivado/getting-started-with-ipi/start) as a reference
    - You'll need to have the following blocks in your design:
        - MicroBlaze
        - MicroBlaze Debug Module
        - Clocking Wizard
        - Processor System Reset
        - AXI Interconnect
        - AXI Interrupt Controller
        - Concat
        - AXI Uartlite
        - PmodGPS_v1_1
    - Ensure the interrupt port of the GPS and UART blocks are connected to the Concat block
7. Run Generate Bitstream
8. When bitstream generation is complete, export the hardware
    - File > Export > Export Hardware
    - **Ensure _Include bitstream_ is selected**
9. Launch SDK
    - File > Launch SDK
    - Accept the defaults
10. Create a new blank C application project
11. Create a new source file
12. Copy the contents of the [example source code](https://github.com/Digilent/vivado-library/blob/master/ip/Pmods/PmodGPS_v1_1/drivers/PmodGPS_v1_1/examples/main.c) into the new file and save
13. Power on and program the board with the bitstream
14. Connect the SDK terminal to the board
    - Select one of the COM ports
    - Baud Rate: 9600
15. Launch the software on the board using SDK

> The LED on the GPS PMOD will blink until it is able to obtain a position fix. Until that time, the software will only print the number of satellites to the terminal.


## C & C++

Given that the GPS driver code is written in C, this may present a problem when you need to write the rest of your software in C++ (e.g. when WiFi is involved). The GPS driver code _cannot_ be compiled using `mb-g++`. To explicitly build the GPS code using a C compiler, follow these steps:

> These steps assume the GPS example code is located in a file called `gps.c` and that global variables and functions are declared in `gps.h`. ([Example](https://github.com/ece532-2019-g7/G7_Suspicious-Package-Detection-and-Alert/tree/master/sw/gps_wifi/sdk/src))

1. Add all the source files to your existing C++ project
2. Right click on `gps.c` in the _Project Explorer_ and choose _Properties_
3. In the left pane, navigate to `C/C++ Build > Settings`
4. Change the contents of the _Command_ field to `mb-gcc`
5. When including `gps.h`, wrap the `#include` as follows:

```C
extern "C" {
    #include "gps.h"
}
```


## Interrupts

You _do not_ need to leverage interrupts to make use of the GPS PMOD. To poll the device for an update, use the following function:

```C
// Poll PMOD for GPS Data
// GPS is an instance of PmodGPS
GPS_getData(&GPS);
```


## Connectivity Issues

**Do not** think that you'll be able to use the GPS module inside a building. Based on personal experience, the antenna has a difficult time obtaining a position fix even when standing outside. _Don't make this an integral part of your project/demo._
