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
4. External switch: BCM53125?
5. Wifi: built-in?

## LEDs
* WAN Port: Yellow(460+6), Green(460+7)
* WLAN Led (D12): (460+4) Logic or (460+5), this is weird!
* Internet Led (D17): Green(480+8), Red(?)
* DSL Bonding Led (D17): Green(480+9)
* Power Led (D15): Green(480+20), Red(480+15)
* DSL (D7): Green(?)
* FXS (D11): Green(?), Red(?)
* WPS Led (D13): Green(?), Red(?)



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
1. The WAN port is functional, by default, it acts as a DHCP client. Note that the WAN port LEDs are not functional at the moment, see TODO 1.
2. All USB ports are working as expected. Additional kernel modules may be required to operate the USB devices.

## TODOs
1. __NONE of the LAN ports works once boot to the current OpenWRT build. (Fixing this should take the highest priority!)__ It is believed to be a device tree and boardinfo problem. It is still not clear about the connection between the SOC and the external switch (53125?). [bcm63169-comtrend-vg-8050](https://github.com/openwrt/openwrt/blob/ec6293febc244d187e71a6e54f44920be679cde4/target/linux/bcm63xx/dts/bcm63169-comtrend-vg-8050.dts), [bcm6369-comtrend-wap-5813n](https://github.com/openwrt/openwrt/blob/ec6293febc244d187e71a6e54f44920be679cde4/target/linux/bcm63xx/dts/bcm6369-comtrend-wap-5813n.dts) and [bcm6362-huawei-hg253s-v2](https://github.com/openwrt/openwrt/blob/ec6293febc244d187e71a6e54f44920be679cde4/target/linux/bcm63xx/dts/bcm6362-huawei-hg253s-v2.dts) may provide some hints. An [image](https://openwrt.org/_detail/media/huawei/hg253sv2-front.jpg?id=toh%3Ahuawei%3Ahg253s_v2) of Huawei HG253s-V2.
2. LEDs are not defined in the device tree.
3. WiFi does not work at the moment.
4. ADSL/VDSL does not work.
5. __Dump the DTB__. Currently, I cannot find the location of the device tree (DTB), the CFE boot loader does not seem to supply its path/address to the kernel. Extracting and decompiling the kernel may help solving TODO 1. Someone suggested that the DTB might be appended to the kernel image. We should try: https://github.com/PabloCastellano/extract-dtb or https://github.com/MoetaYuko/split-appended-dtb. See also: https://reverseengineering.stackexchange.com/questions/19495/re-zyxel-pmg5318-b20c-jffs2-vmlinux-lz.

# References
https://github.com/mattimustang/optus-sagemcom-fast-3864-hacks/issues/27
