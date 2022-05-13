# flygeminiv2 notes

- [store](https://www.aliexpress.com/item/3256803350581362.html?spm=a2g0o.store_pc_home.0.0.6312552c92mAaV&gatewayAdapt=4itemAdapt)
- [github](https://github.com/Mellow-3D/Fly-Gemini-V2)

#  words

|       |                                             |
| ----- | ------------------------------------------- |
| Core  | the linux computer part<br>Allwinner H5<br> |
| Board | the 3d printer parts<br>STM32F405           |

## core
- https://linux-sunxi.org/H5
- mainline from  4.12 U-Boot v2017.05

# creds
root:mellow
fly:mellow

# getting started - klipper
- board ships blank
- download SBC (Core) OS and flash to SD card
  - connect to USB3 and boot, you will be dropped into a root prompt
  - screen /dev/tty.usbserial-12430 115200
- ssh in as fly
- cd klipper
- make menuconfig
  - Micro-controller Architecture (STMicroelectronics STM32)
  - Processor model (STM32F405)
  - Bootloader offset (32KiB bootloader)
  - Communication interface (USB (on PA11/PA12))
- make -j 4
- out/klipper.bin

- on the mac
  - cd /Volumes/NO\ NAME
  - scp fly@192.168.2.116:klipper/out/klipper.bin firmware.bin

- put the sd card in the board sdcard slot and reboot
- set dip2 to 00110000
- stm32 should come up on the core
  - lsusb
```
lsusb
Bus 008 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
Bus 005 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 007 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
Bus 004 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 006 Device 002: ID 1d50:614e OpenMoko, Inc. stm32f405xx
Bus 006 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0001 Linux Foundation 1.1 root hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 009 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```
  
now you can configure klipper




# Firmwares
|                     |                                                                                         |
| ------------------- | --------------------------------------------------------------------------------------- |
| jay_s_uk 04/27/2022 | https://upyun.pan.zxkxz.cn/IMG/Build/FLY-v2.9_Flygemini_bullseye_current_5.10.85.img.xz |

# other references
|            |                                                                      |
| ---------- | -------------------------------------------------------------------- |
| v1         | https://teamgloomy.github.io/fly_gemini_v1.1_connected_sbc.html      |
| a build    | https://github.com/TeamGloomy/rrf_stm32_sbc/releases                 |
| gloomy git | https://github.com/TeamGloomy/rrf_stm32_sbc.git                      |
|            | https://3dpandme.com/2022/01/03/fly-gemini-klipper-mainsail-install/ |


# ports

|      |                                    |                             |         |
| ---- | ---------------------------------- | --------------------------- | ------- |
| hdmi | USB3                               | USB4                        | stm32   |
|      | core uart                          | board usb (mapped with sw2) | ttyACM0 |
|      | can power                          | can power                   |         |
|      | not pd 5v usb, serial port (ch340) | not PD, 5v USB              |         |
|      | console=ttyS0,115200               |


# jumpers
## sw1 (the one near the stepper drivers)
board spi2 <-> core spi0

| switch | schematic    |     | core pin name | board pin name                      | pin |
| ------ | ------------ | --- | ------------- | ----------------------------------- | --- |
| 1      | sbc_transfer |     | I2C1_SCL      | PC9-TM3_C4TIM8_CH4/SDIO_D1/I2C3_SDA | 40  |
| 2      | SPI2_CS      |     | SPI0_CS       | SPI2_CS                             | 33  |
| 3      | SPI2 MISO    |     | SPI0_MISO     | SPI2 MISO                           | 35  |
| 4      | SPI2 SCK     |     | SPI0_CLK      | SPI2 SCK                            | 34  |
| 5      | SPI2 MOSI    |     | SPI0_MOSI     | SPI2 MOSI                           | 36  |

## sw2 (the one near the usb port)
usb port 4 (the one on the right) <-> various things

| switch | core        | schematic | schematic | board pin | board pin name      |
| ------ | ----------- | --------- | --------- | --------- | ------------------- |
| 1      | core usb1   | usb1-dp   | can-d1    |           | CAN 1 hdr           |
| 2      | core usb1   | usb1-dm   | can-dm    |           | CAN 1 hdr           |
| 3      | core usb1   | usb1-dp   | usb-dp    | 45        | USB-DP/CAN1_TX-PA12 |
| 4      | core usb1   | usb1-dm   | usb-dm    | 44        | USB-DM/CAN1_RX-PA11 |
| 5      | CAN1 header | CAN TX    | usb-dp    | 45        | USB-DP/CAN1_TX-PA12 |
| 6      | CAN1 header | CAN RX    | usb-dm    | 44        | USB-DM/CAN1_RX-PA11 |
| 7      | usb port 4  | usb4 dp   | usb-dp    | 45        | USB-DP/CAN1_TX-PA12 |
| 8      | usb port 4  | usb4 dm   | usb-dm    | 44        | USB-DM/CAN1_RX-PA11 |

## connect stm32 to usb4 port
12345678
00000011

## connect stm32 to core usb1
12345678
00110000

# buttons
## K1 reset
pulls core reset (v6) low
reset's core CPU

## k2 uboot
pulls core reset (w6) low





# usb serial
Product ID:	0x7523  
Vendor ID:	0x1a86  
Version:	81.32  
Speed:	Up to 12 Mb/s  
Location ID:	0x00124300 / 11  
Current Available (mA):	500  
Current Required (mA):	100  
Extra Operating Current (mA):	0  

# FLY-v2.9_Flygemini_bullseye_current_5.10.85
- write with etcher


mdio_mux               16384  1 dwmac_sun8i
dwmac-sun8i
[   18.693303] dwmac-sun8i 1c30000.ethernet eth0: PHY [0.1:01] driver [Generic PHY] (irq=POLL)