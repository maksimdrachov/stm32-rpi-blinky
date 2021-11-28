## Bare setup STM32
The goal is to setup a simple Blinky program on the STM32F4 discovery board only using a terminal.

Prerequisites:
- STM32F4 Discovery board
- USB type A to Mini-B cable
- Raspberry Pi (I'm using the [400](https://www.raspberrypi.com/products/raspberry-pi-400/))

### Step 1: Install required software

```
sudo apt-get install -y gcc-arm-none-eabi
```

As a sanity check you can run:

```
arm-none-eabi-gcc --version
```

You should see something like this:

![sanity-check](/images/sanity-check.png)

STM boards use an interface called STLINK for programming and debugging firmware, install this tool:

```
sudo apt-get update
sudo apt-get install cmake
sudo apt-get install libusb-1.0-0-dev
git clone https://github.com/stlink-org/stlink
cd stlink
cmake .
make
```

Now we copy the built binaries to their place:

```
cd ~/stlink/bin
sudo cp st-* /usr/local/bin
cd ../lib
sudo cp *.so* /lib
```

Then udev rules:

```
sudo cp stlink/config/udev/rules.d/49-stlinkv* /etc/udev/rules.d/
```

Restart the terminal, and then connect your STM board. Execute a probe command to see if it works:

```
st-info --probe
```

You should see something like this:

(ADD A SCREENSHOT HERE)

Now we need to install `gdb-multiarch` (`arm-arm-none-gdb` that works on RPI's ARM platform)

```
sudo apt-get install gdb-multiarch
```

In order to allow `.gdbinit` to be loaded from the current directory, you'll need to add a line to `~/.gdbinit`.

First open the file:

```
sudo geany ~/.gdbinit
```

Add the following line:

```
set auto-load safe-path /
```

### Step 2: Download Blinky Example

Clone the repo into your homefolder:

```
git clone https://github.com/maksimdrachov/stm32-rpi-blinky
```

### Step 3: Flash STM32

Connect your board to the RPI, and flash it using the following commands:

```
cd ~/stm32-rpi-blinky/blinky
sudo make burn
```

Now press the black reset button, the red led should start blinking.

Note: the last command can also be done without "sudo" if you set the libusb permissions.

### Step 4: Debug setup

The debug setup will require 2 terminal windows.

In the first terminal window, run `st-util`:

```
cd ~/stlink/bin
st-util
```

In the second terminal window, run `gdb-multiarch`:

```
cd ~/stm32-rpi-blinky/blinky
gdb-multiarch blinky.elf
```

You should get something like this:

![debug-setup](/images/debug-setup.png)

_Sources_

- [Blink for stm32f4-discovery board on Linux with Makefile](https://liviube.wordpress.com/2013/04/22/blink-for-stm32f4-discovery-board-on-linux-with-makefile/)

- [github.com/rowol/stm32_discovery_arm_gcc](https://github.com/rowol/stm32_discovery_arm_gcc)

- [Installing ST-Link to flash STM32 targets on Linux](https://freeelectron.ro/installing-st-link-v2-to-flash-stm32-targets-on-linux/)

