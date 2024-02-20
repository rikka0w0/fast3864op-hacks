# Segamcom F@st 3864OP
Now moved to [OpenWrt Wiki page](https://openwrt.org/inbox/toh/sagem/f_st3864op).

# Known facts
| Filename       | [Partition Label](https://openwrt.org/inbox/toh/sagem/f_st3864op#flash_layout) |
|----------------|--------------|
| stock.mtd0.bin | cferom_nvram |
| stock.mtd1.bin | stock_rootfs |
| stock.mtd2.bin | stock_rootfs_update |
| stock.mtd3.bin | stock_hidden1 |
| stock.mtd4.bin | stock_data |
| stock.mtd5.bin | stock_hidden2 |
The dumped stock firmware is also available in this repo, named `mtdblock?.bin`, where `?` is a number between 0 and 3. `cferam.000` and `vmlinux.lz` extracted from `mtdblock1.bin` seem to be the part of the CFE boot loader and the compressed Linux kernel, respectively. ["stock.config"](./stock.config) is the extracted compilation config of the stock kernel. The stock firmware does not seem to contain a DTB.

# References
* https://github.com/mattimustang/optus-sagemcom-fast-3864-hacks/issues/27
* https://openwrt.org/docs/techref/hardware/port.gpio
* https://forum.openwrt.org/t/bcm5325-switch-spi-on-bcm6358/85099/4
* https://forum.openwrt.org/t/b53-failed-to-detect-switch-on-bcm5358/20011
