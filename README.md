# Segamcom F@st 3864OP
There is an unsoldered 4-pin header (J708) near the power jack, which provides an interactive console (3.3V TTL @ 115200 baud rate). You can interrupt the boot process and enter the CFE bootloader console by pressing any key repeatedly, or wait for it boots and then login to the management console using the following credentials:
```
user: admin
password: 0ptU%1M5
```
If the above does not work, then try [the other keys](https://github.com/mattimustang/optus-sagemcom-fast-3864-hacks).The management console offers a very limited set of commands, the `sh` command will drop you to a BusyBox Linux shell.

# Known facts
Boot log of the stock firmware is available in ["stock_bootlog.txt"](./stock_bootlog.txt). The dumped stock firmware is also available in this repo, named `mtdblock?.bin`, where `?` is a number between 0 and 3. `cferam.000` and `vmlinux.lz` extracted from `mtdblock1.bin` seem to be the part of the CFE boot loader and the compressed Linux kernel, respectively. ["stock.config"](./stock.config) is the extracted compilation config of the stock kernel.
## Hardware configuration
1. CPU: BCM63168D0, MIPS: 400MHz, DDR: 400MHz, Bus: 200MHz
2. RAM: [NT5CC64M16GP-DI](https://www.nanya.com/en/Product/3747/NT5CC64M16GP-DI), DDR3 128MB
3. NAND: [W29N01HVSINA](https://au.mouser.com/datasheet/2/949/w29n01hvxina_revc-1489886.pdf), TSOP1 package, 128MB, blocksize: 64 pages, page size: 2048 bytes + 64-byte OOB data.
4. Switch: SOC built-in switch + BCM53124SKMMLG (CFE reports 53125, but the boot log of the stock firmware shows that it forces to use 53115 driver)
5. Wifi: built-in?
6. Other chips: [74HC164D](https://assets.nexperia.com/documents/data-sheet/74HC_HCT164.pdf), [LMX4181](https://www.renesas.com/in/en/products/interface-connectivity/wireless-communications/dect/lmx4181-high-frequency-dect-transceiver-standard-cmos-technology), [SI32260-C-FM1](https://au.mouser.com/datasheet/2/472/Si32260_61-2507288.pdf), [BCM6302](https://www.digikey.co.uk/en/products/detail/broadcom-limited/BCM6302KMLG/6147288)

## Network
This router has 2 switches! The BCM63168D0 SOC has 8 internet interfaces (as [OpenWRT source code](https://github.com/openwrt/openwrt/blob/dc2da6a23369c8da069321dcfd593a9cf8c993c6/target/linux/bcm63xx/patches-5.10/339-MIPS-BCM63XX-add-support-for-BCM63268.patch#L738) suggests), 3xFE, 1xGE, and 4xRGMII, all from a built-in switch. In this router, the GE port (id=3) is used for WAN RJ45 connection, the first RGMII (id=4) connects to an external switch (BCM53125?), all accessible LAN ports are from the external switch. __Why didn't they just simply connect 4 PHYs at the RGMIIs to make 4xGE LAN ports?__

## LEDs and Buttons
* WAN Port: Yellow(460+6, gpio38), Green(460+7, gpio39)
* WLAN Led (D12): (460+4, gpio36) Logic XOR (460+5, gpio37), this is weird!
* Internet Led (D17): Green(480+8, gpio8), Red(74HC164D@Q2)
* DSL Bonding Led (D17): Green(480+9, gpio9)
* Power Led (D15): Green(480+20, gpio20), Red(480+15, gpio15)
* DSL (D7): Green(74HC164D@Q3)
* FXS (D11, Foreign Exchange Station, the phone ports, not to be confused with the xDSL port): Green(74HC164D@Q4), Red(74HC164D@Q5)
* WPS Led (D13): Green(74HC164D@Q0), Red(74HC164D@Q1)
* Reset button (SW5): (460+0, gpio32)
* WPS button (SW3): (460+1, gpio33)
* WLAN button (SW2): (460+2, gpio34)
* Some LEDs are driven by 74HC164D:
    * Clock (CP) = gpio0 (/sys/class/gpio/gpio480), serial_led_clk
    * DSA (Data input) = gpio1 (/sys/class/gpio/gpio481), serial_led_data
    * DSB (AND with DSA), MR(Output Async Reset) are pulled high.

## NAND partitions
Extracted from the boot log of the stock firmware:
```
Creating 6 MTD partitions on "brcmnand.0":
0x000000020000-0x000003d80000 : "rootfs"
0x000003d80000-0x000007ae0000 : "rootfs_update"
0x000007b00000-0x000007f00000 : "data"
0x000000000000-0x000000020000 : "nvram"
0x000000020000-0x000003d80000 : "image"
0x000003d80000-0x000007ae0000 : "image_update"
```
1. `0x000000000000-0x000000020000 : "nvram"`: an 128kB partition with unclear layout, also known as "cferom" in OpenWRT, it stores the CFE bootloader. See `mtdblock0.bin`.
2. `0x000000020000-0x000003d80000 : "rootfs"`: an 62846kB (61.375MB) JFFS2 partition, which holds the rootfs and the compressed kernel image. See `mtdblock1.bin`.
3. `0x000003d80000-0x000007ae0000 : "rootfs_update"`: a blank 62846kB (61.375MB) JFFS2 partition, seems to be a good place to put the OpenWRT installation, so that we dont have to take the risk of brick the entire device.
4. `0x000007b00000-0x000007f00000 : "data"`: an 256kB JFFS2 partition with unknown usage. See `mtdblock3.bin`.
5. The "image" and "image_update" partitions are mapped to "rootfs" and "rootfs_update", respectively, not sure why they are there.

# Current Progress
The framework of the new device support has been made (see [the repo](https://github.com/rikka0w0/openwrt-fast3864op) and [work_with_openwrt.md](./work_with_openwrt.md) for instructions), including a patch for `board_bcm963xx.c`("openwrt/target/linux/bcm63xx/patches-5.4"), a new device tree("openwrt/target/linux/bcm63xx/dts/bcm63168-sagem-fast-3864op.dts"), a new make target("openwrt/target/linux/bcm63xx/image/bcm63xx_nand.mk", and new configuration scripts ("openwrt/target/linux/bcm63xx/base-files/etc/board.d/02_network").

## What's working
1. The WAN port is functional, by default, it acts as a DHCP client. Note that the WAN port LEDs are not functional at the moment, see TODO 2.
2. All LAN ports work, but VLAN tagging on the external switch does not work at all, see TODO 1.
3. All USB ports are working as expected. Additional kernel modules may be required to operate the USB devices.
4. `lspci` shows the root PCIE bridge, but the devices (should exist? If looking at the stock boot log...)

## TODOs
1. VLAN tagging on the external switch. By looking at similar platforms, it is very likely that the external switch is controlled by the SOC via HSSPI or LSSPI. Howerver, there is still a chance that the external switch was left uncontrolled and the VLAN tagging won't be possible. It is believed to be a device tree and boardinfo problem. It is still not clear about the connection between the SOC and the external switch (53125?). We are trying to probe both SPI interfaces for possible attached devices. The connection between the SOC and the external switch is surprisingly similar to [Sercomm H500S](https://github.com/openwrt/openwrt/blob/ec6293febc244d187e71a6e54f44920be679cde4/target/linux/bcm63xx/dts/bcm63167-sercomm-h500-s.dtsi) and [Huawei HG635](https://openwrt.org/toh/huawei/hg635). [bcm63169-comtrend-vg-8050](https://github.com/openwrt/openwrt/blob/ec6293febc244d187e71a6e54f44920be679cde4/target/linux/bcm63xx/dts/bcm63169-comtrend-vg-8050.dts), [bcm6369-comtrend-wap-5813n](https://github.com/openwrt/openwrt/blob/ec6293febc244d187e71a6e54f44920be679cde4/target/linux/bcm63xx/dts/bcm6369-comtrend-wap-5813n.dts) and [bcm6362-huawei-hg253s-v2](https://github.com/openwrt/openwrt/blob/ec6293febc244d187e71a6e54f44920be679cde4/target/linux/bcm63xx/dts/bcm6362-huawei-hg253s-v2.dts) may provide some hints. An [image](https://openwrt.org/_detail/media/huawei/hg253sv2-front.jpg?id=toh%3Ahuawei%3Ahg253s_v2) of Huawei HG253s-V2.
The switch cannot be connected on HSSPI.CS6 and HSSPI.CS7, as GPIO8 and GPIO9 are occupied by LEDs.
2. WAN LEDs are not properly controlled.
3. `lspci` shows no device attached.
4. WiFi does not work at the moment.
5. ADSL/VDSL does not work.
6. __Dump the DTB__. Currently, I cannot find the location of the device tree (DTB), the CFE boot loader does not seem to supply its path/address to the kernel. Extracting and decompiling the kernel may help solving TODO 1. Someone suggested that the DTB might be appended to the kernel image. We should try: https://github.com/PabloCastellano/extract-dtb or https://github.com/MoetaYuko/split-appended-dtb. See also: https://reverseengineering.stackexchange.com/questions/19495/re-zyxel-pmg5318-b20c-jffs2-vmlinux-lz.
7. [No driver to control the shift register 74HC164D?](https://forum.openwrt.org/t/adding-support-for-segamcom-f-st-3864op-missing-driver-for-74hc164d-led-expander/131836)

# References
* https://github.com/mattimustang/optus-sagemcom-fast-3864-hacks/issues/27
* https://openwrt.org/docs/techref/hardware/port.gpio
* https://forum.openwrt.org/t/bcm5325-switch-spi-on-bcm6358/85099/4
* https://forum.openwrt.org/t/b53-failed-to-detect-switch-on-bcm5358/20011
