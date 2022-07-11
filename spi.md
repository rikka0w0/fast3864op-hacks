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
