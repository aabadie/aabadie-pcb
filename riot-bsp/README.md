RIOT support for "aabadie-pcb"
=============================

This directory contains the [RIOT](https://github.com/RIOT-OS/RIOT) for the
"aabadie-pcb" board.

How to build using RIOT
-----------------------

**Prerequisites:** An ARM toolchain and flashing tools such as dfu-util and
JLink are required. To access the serial port, you'll also need
[pyserial](https://pypi.org/project/pyserial/).

1. Clone RIOT:
  ```
  git clone https://github.com/RIOT-OS/RIOT.git
  ```

2. Clone this repository:
  ```
  git clone https://github.com/aabadie/aabadie-pcb.git
  ```

3. In RIOT, use the `EXTERNAL_BOARD_DIRS` variable to specify the location of
   the DotBot port.
   ```
   EXTERNAL_BOARD_DIRS=$(realpath ./boards) make BOARD=aabadie-bsp -C RIOT/examples/hello-world
   ```

Flashing
--------

The board exposes SWD pins so it can be flashed using JLink. Once pins are
connected to a JLink programmer, run the following command:

```
EXTERNAL_BOARD_DIRS=$(realpath ./boards) PROGRAMMER=jlink make BOARD=aabadie-bsp -C RIOT/examples/hello-world flash
```

The board can also be flashed using DFU, which is the default programmer. To switch the
board to DFU mode, put the switch button down and power up the board by plugin it to an USB port.
You should see in the system logs messages about the STMicroelecronics BOOTLOADER:
```
New USB device strings: Mfr=1, Product=2, SerialNumber=3
Product: STM32  BOOTLOADER
Manufacturer: STMicroelectronics
```
To flash using DFU, run the following command:
```
EXTERNAL_BOARD_DIRS=$(realpath ./boards) make BOARD=aabadie-bsp -C RIOT/examples/hello-world flash
```

Access to stdio
---------------

By default stdio is available directly via the USB port. On Linux, one has to
configure udev rules to access `/dev/ttyACM0`. These rules are specific to the
default RIOT USB vendor and product IDs which should only be used for
development.

Create/Edit `/etc/udev/rules.d/99-riot-os.rules` and add the following content:
```
SUBSYSTEMS=="usb", ATTRS{idVendor}=="1209", ATTRS{idProduct}=="7d00", \
    MODE:="0666", \
    SYMLINK+="riot-os_%n"
```
Then reload the udev rules:
```
$ sudo udevadm control --reload-rules
$ sudo udevadm trigger
```

Once this is done (once), access RIOT stdio using the `term` target:
```
EXTERNAL_BOARD_DIRS=$(realpath ./boards) make BOARD=aabadie-bsp -C RIOT/examples/hello-world term
```
