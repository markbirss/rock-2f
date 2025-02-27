# rock-2f

Meshtastic running with latest Arbmian on Raxda Rock 2F using my Starter Edition HAT (GPIO to SPI)

![image](https://github.com/user-attachments/assets/8ccbafa0-ce56-4e33-8606-491728911492)


Rock 2F support recently landed
https://github.com/armbian/build/pull/7820

Compile a OS Image (Noble)
```
sudo ./compile.sh build BOARD=rock-2f BRANCH=vendor BUILD_DESKTOP=yes BUILD_MINIMAL=no DESKTOP_APPGROUPS_SELECTED= DESKTOP_ENVIRONMENT=gnome DESKTOP_ENVIRONMENT_CONFIG_NAME=config_base KERNEL_CONFIGURE=no RELEASE=noble DOWNLOAD_MIRROR=us
```

Compile and install the required spidev and i2c0 overlays
```
git clone https://github.com/radxa-pkg/radxa-overlays
cd radxa-overlays
make build-dtbo -j$(nproc)

sudo cp./arch/arm64/boot/dts/rockchip/overlays/rk3528-spi0-cs1-spidev.dtbo /boot/dtb/rockchip/overlay/
sudo cp ./arch/arm64/boot/dts/rockchip/overlays/rk3528-i2c0-m1.dtbo /boot/dtb/rockchip/overlay/
```

Enable the overlays
modify /boot/armbianEnv.txt
```
verbosity=1
bootlogo=true
console=both
overlay_prefix=rk35xx
fdtfile=rockchip/rk3528-rock-2f.dtb
rootdev=UUID=6457ccf1-edc1-4c9c-935f-fdb8dc320999
rootfstype=ext4
overlays=rk3528-spi0-cs1-spidev, rk3528-i2c0-m1
usbstoragequirks=0x2537:0x1066:u,0x2537:0x1068:u
```

Connect and test i2c
```
sudo i2cdetect -r -y 0
[sudo] password for user: 
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:                         -- -- -- -- -- -- -- -- 
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
50: 50 -- -- -- -- -- -- -- 58 -- -- -- -- -- -- -- 
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- 
70: -- -- -- -- -- -- -- -- 
```

Install - meshtasticd
https://meshtastic.org/docs/software/linux/installation/?os=ubuntu
```
# Install requirements for add-apt-repository
sudo apt install software-properties-common
# Add Meshtastic repo
sudo add-apt-repository ppa:meshtastic/beta
# Install meshtasticd
sudo apt install meshtasticd
```

lora-raxda-rock2f-starter-edition-hat.yaml
```
Lora:

### Raxda Rock 2F running Armbian Linux 6.1.99-vendor-rk35xx
### https://github.com/markbirss/lora-starter-edition-sx1262-i2c
### https://github.com/radxa-pkg/radxa-overlays/blob/main/arch/arm64/boot/dts/rockchip/overlays/rk3528-spi0-cs1-spidev.dts
### Require install of https://github.com/radxa-pkg/radxa-overlays and rk3528-spi0-cs1-spidev.dtbo copied to /boot/dtb/rockchip/overlay and enabled 
### in  /boot/armbianEnv.txt - overlays=rk3528-spi0-cs1-spidev
### The Radxa Rock 2F employs multiple gpio chips.
### Each gpio pin must be unique, but can be assigned to a specific gpio chip and line.
### In case solely a no. is given, the default gpio chip and pin == line will be employed.
###
  Module: sx1262  # Radxa Rock 2F + Starter Edition SX1262 HAT by Mark Birss
  DIO2_AS_RF_SWITCH: true
  DIO3_TCXO_VOLTAGE: 1.8
  spidev: spidev0.1
  CS:             # NSS     PIN_24 -> chip 4, line 14
    pin: 24
    gpiochip: 4
    line: 14
  SCK:            # SCK     PIN_23 -> chip 4, line 12
    pin: 23
    gpiochip: 4
    line: 12
  Busy:           # BUSY    PIN_7 -> chip 4, line 6
    pin: 7
    gpiochip: 4
    line: 6
  MOSI:           # MOSI    PIN_19 -> chip 4, line 10
    pin: 19
    gpiochip: 4
    line: 10
  MISO:           # MISO    PIN_21 -> chip 4, line 11
    pin: 21
    gpiochip: 4
    line: 11
  Reset:          # NRST    PIN_12 -> chip 1, line 13
    pin: 12
    gpiochip: 1
    line: 13
  IRQ:            # DIO1    PIN_15 -> chip 4, line 12
    pin: 15
    gpiochip: 4
    line: 22
#  RXen:           # RXEN    PIN_22 -> chip 3!, line 17
#    pin: 22
#    gpiochip: 3
#    line: 17
#  TXen: RADIOLIB_NC # TXEN   no PIN, no line, fallback to default gpio chip
```


Support my work and considder **buying  me a coffee**

https://buymeacoffee.com/mark.birss
