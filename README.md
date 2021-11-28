## Bare setup STM32
The goal is to setup a simple Blinky program on the STM32F4 discovery board using only a terminal.

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
make
```

Install `gdb-multiarch` (`arm-arm-none-gdb` that works on RPI's ARM platform)

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
make burn
```

Now press the black reset button, the red led should start blinking.

### Step 4: Debug setup

The debug setup will require 2 terminal windows.

In the first terminal window, run `st-util`:

```
cd ~/stlink/bin
st-util
```

In the second terminal window, run `gdb-multiarch`:

```
cd ~/stm32-rpi-blinky
gdb-multiarch blinky.elf
```

You should get something like this:

![debug-setup](/images/debug-setup.png)
