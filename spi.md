The following came from the shell of the stock firmware:

```
# ls /sys/devices/platform -al
drwxr-xr-x    6 admin    root             0 Jul 11 22:21 .
drwxr-xr-x    7 admin    root             0 Jan  1  1970 ..
drwxr-xr-x    2 admin    root             0 Jul 11 22:22 alarmtimer
drwxr-xr-x    3 admin    root             0 Jul 11 22:22 bcmhs_spi.1
drwxr-xr-x    3 admin    root             0 Jul 11 22:22 bcmleg_spi.0
drwxr-xr-x    2 admin    root             0 Jul 11 22:22 brcmnand.0
-rw-r--r--    1 admin    root          4096 Jul 11 22:22 uevent
# ls /sys/devices/platform/bcmhs_spi.1/ -al
drwxr-xr-x    3 admin    root             0 Jul 11 22:22 .
drwxr-xr-x    6 admin    root             0 Jul 11 22:21 ..
lrwxrwxrwx    1 admin    root             0 Jul 11 22:22 driver -> ../../../bus/platform/drivers/bcmhs_spi
-r--r--r--    1 admin    root          4096 Jul 11 22:22 modalias
drwxr-xr-x    3 admin    root             0 Jul 11 22:22 spi_master
lrwxrwxrwx    1 admin    root             0 Jul 11 22:22 subsystem -> ../../../bus/platform
-rw-r--r--    1 admin    root          4096 Jul 11 22:22 uevent
/# ls /sys/devices/platform/bcmleg_spi.0/ -al
drwxr-xr-x    3 admin    root             0 Jul 11 22:22 .
drwxr-xr-x    6 admin    root             0 Jul 11 22:21 ..
lrwxrwxrwx    1 admin    root             0 Jul 11 22:27 driver -> ../../../bus/platform/drivers/bcmleg_spi
-r--r--r--    1 admin    root          4096 Jul 11 22:27 modalias
drwxr-xr-x    3 admin    root             0 Jul 11 22:24 spi_master
lrwxrwxrwx    1 admin    root             0 Jul 11 22:27 subsystem -> ../../../bus/platform
-rw-r--r--    1 admin    root          4096 Jul 11 22:27 uevent
# ls /sys/devices/platform/bcmleg_spi.0/spi_master/ -al
drwxr-xr-x    3 admin    root             0 Jul 11 22:24 .
drwxr-xr-x    3 admin    root             0 Jul 11 22:22 ..
drwxr-xr-x    2 admin    root             0 Jul 11 22:24 spi0
# ls /sys/devices/platform/bcmhs_spi.1/spi_master/ -al
drwxr-xr-x    3 admin    root             0 Jul 11 22:22 .
drwxr-xr-x    3 admin    root             0 Jul 11 22:22 ..
drwxr-xr-x    2 admin    root             0 Jul 11 22:22 spi1
```

Looks like the BCM53124 switch is connected to HSSPI.CS1 (spi1). SPI0 (LSSPI) in OpenWRT seems to be the `bcmleg_spi`.

# Spare SPI Flash
There's an unsoldered SPI Flash (SOIC16) on the bottom of the board. I soldered a SOIC8 Winbon "W25Q64" with jumper wires and loaded "HSSPI.CS0" with spidev driver.
Then, I'm able to access the SPI Flash using the `flashrom-spi` tool and `spi-tools` (available in the OpenWRT opkg repo):
```
root@OpenWrt:/# flashrom-spi -p linux_spi:dev=/dev/spidev1.0
flashrom v1.2 on Linux 5.4.188 (mips)
flashrom is free software, get the source code at https://flashrom.org

Using clock_gettime for delay loops (clk_id: 1, resolution: 1ns).
Using default 2000kHz clock. Use 'spispeed' parameter to override.
Found Winbond flash chip "W25Q64.V" (8192 kB, SPI) on linux_spi.
No operations were specified.

root@OpenWrt:/# echo -n -e \\x9F\\xFF\\xFF\\xFF | spi-pipe -d /dev/spidev1.0 -b4 -n1 | hexdump -C
00000000  ff ef 40 17                                       |..@.|
00000004
```
