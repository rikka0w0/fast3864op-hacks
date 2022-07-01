# Segamcom F@st 3864OP
There is an unsoldered 4-pin header (J708) near the power jack, which provides an interactive console (3.3V TTL @ 115200 baud rate). You can interrupt the boot process and enter the CFE bootloader console by pressing any key repeatedly, or wait for it boots and then login to the management console using the following credentials:
```
user: admin
password: 0ptU%1M5
```
The management console offers a very limited set of commands, the `sh` command will drop you to a BusyBox Linux shell.

# Known facts
## Hardware configuration
1. CPU: BCM63168D0, MIPS: 400MHz, DDR: 400MHz, Bus: 200MHz
2. RAM: [NT5CC64M16GP-DI](https://www.nanya.com/en/Product/3747/NT5CC64M16GP-DI), DDR3 128MB
3. NAND: [W29N01HVSINA](https://au.mouser.com/datasheet/2/949/w29n01hvxina_revc-1489886.pdf), TSOP1 package, 128MB, blocksize: 64 pages, page size: 2048 bytes + 64-byte OOB data.
4. External switch: BCM53125?
5. Wifi: built-in?

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

# References
https://github.com/mattimustang/optus-sagemcom-fast-3864-hacks/issues/27
