# Setup workspace
```
git clone git@github.com:rikka0w0/openwrt-fast3864op.git
cd openwrt
git checkout v21.02.3-fast3864op

# Update the feeds
./scripts/feeds update -a
./scripts/feeds install -a

# Download my configuration for F@st fast_3864op
wget .../openwrt.config -O .config

# Configure the firmware image and the kernel (Optional)
make menuconfig
make -j $(nproc) kernel_menuconfig
```

# Build Image
```
make -j $(nproc) download clean world
```
The outputs will be placed at `openwrt/bin/targets/bcm63xx/smp/`. Currently, my configuration does not generate any flashable image, it only generates a RAM boot image named `openwrt-21.02.3-bcm63xx-smp-sagem_fast-3864op-initramfs.elf`. The RAM boot image will not make any modification to the NAND Flash.

To do the RAM boot:
1. Setup a TFTP server (e.g. `tftpd-hpa`) on your PC.
2. Connect one of the LAN port to the second network interface of your PC (Can be a cheap 100M USB ethernet adapter), configure the network interface to use a static IPv4 `192.168.1.100`, netmask `255.255.255.0`, and gateway `192.168.1.1`.
3. Break into the CFE console (See README.md) and run `r openwrt-21.02.3-bcm63xx-smp-sagem_fast-3864op-initramfs.elf`
4. Wait for OpenWRT

# Work with patches
See also: https://openwrt.org/docs/guide-developer/toolchain/use-patches-with-buildsystem#adding_or_editing_kernel_patches
```
make target/linux/{clean,prepare} V=s QUILT=1
cd build_dir/target-*/linux-*/linux-*
quilt push platform/533-board-fast_3864op.patch
quilt edit arch/mips/bcm63xx/boards/board_bcm963xx.c
quilt diff
quilt refresh
cd ../../../../
make target/linux/update package/index V=s
```
