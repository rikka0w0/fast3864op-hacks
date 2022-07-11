It is believed that the SOC has PCI or PCIE buses and devices attached. The following came from the shell of the stock firmware:
```
# cd /sys/bus/pci
# ls devices/ -al
drwxr-xr-x    2 admin    root             0 Jul 10 04:02 .
drwxr-xr-x    5 admin    root             0 Jul 10 04:00 ..
lrwxrwxrwx    1 admin    root             0 Jul 10 04:02 0000:00:00.0 -> ../../../devices/pci0000:00/0000:00:00.0
lrwxrwxrwx    1 admin    root             0 Jul 10 04:02 0000:00:09.0 -> ../../../devices/pci0000:00/0000:00:09.0
lrwxrwxrwx    1 admin    root             0 Jul 10 04:02 0000:00:0a.0 -> ../../../devices/pci0000:00/0000:00:0a.0
lrwxrwxrwx    1 admin    root             0 Jul 10 04:02 0000:01:00.0 -> ../../../devices/pci0000:01/0000:01:00.0
# ls drivers -al
drwxr-xr-x    6 admin    root             0 Jul 10 04:02 .
drwxr-xr-x    5 admin    root             0 Jul 10 04:00 ..
drwxr-xr-x    2 admin    root             0 Jul 10 04:03 ehci_hcd
drwxr-xr-x    2 admin    root             0 Jul 10 04:03 ohci_hcd
drwxr-xr-x    2 admin    root             0 Jul 10 04:03 pcieport
drwxr-xr-x    2 admin    root             0 Jul 10 04:03 wl
# cd /sys/devices/
# ls
pci0000:00  pci0000:01  platform    system      virtual
# cd /sys/module
# ls
8021q                   l2tp_core               pwrmngtd
adsldd                  l2tp_ppp                qcserial
bcm_bpm                 nciTMSkmod              rcutree
bcm_enet                nf_conntrack            sch_htb
bcm_ingqos              nf_conntrack_ftp        scsi_mod
bcmarl                  nf_conntrack_h323       spurious
bcmfap                  nf_conntrack_ipv4       tcp_cubic
bcmvlan                 nf_conntrack_irc        ubi
bcmxtmcfg               nf_conntrack_pptp       ubifs
block                   nf_conntrack_proto_gre  usb_storage
bridge                  nf_conntrack_rtsp       usb_wwan
chipinfo                nf_conntrack_tftp       usbcore
dect                    nf_defrag_ipv4          usblp
dectshim                nf_nat                  usbserial
ehci_hcd                nf_nat_ftp              wl
endpointdd              nf_nat_h323             xt_DSCP
fuse                    nf_nat_irc              xt_LOG
ip6_tables              nf_nat_pptp             xt_SKIPLOG
ip6table_mangle         nf_nat_proto_gre        xt_TCPMSS
ip_gre                  nf_nat_rtsp             xt_conntrack
ip_tables               nf_nat_tftp             xt_dscp
ipt_MASQUERADE          nfnetlink               xt_limit
ipt_REDIRECT            nfnetlink_queue         xt_mac
iptable_filter          option                  xt_mark
iptable_mangle          p8021ag                 xt_multiport
iptable_nat             pcie_aspm               xt_state
ipv6                    pcmshim                 xz_dec
ipw                     pktflow
kernel                  printk

# cat /proc/iomem
00000000-07ecdfff : System RAM
  00010000-003f111f : Kernel code
  003f1120-004e897f : Kernel data
10000200-10000383 : brcmnand
10002500-100025ff : ehci_hcd
10002600-100026ff : ohci_hcd
11000000-11efffff : bcm63xx pcie memory space
a0f00000-a0ffffff : bcm63xx pci memory space
# cat /proc/ioports
a2000000-a200ffff : bcm63xx pci IO space

```

The following is the output of `lspci` on the OpenWRT port:
```
root@OpenWrt:/sys/class/gpio# lspci -v
00:00.0 PCI bridge: Broadcom Inc. and subsidiaries Device 6326 (rev 80) (prog-if 00 [Normal decode])
        Flags: fast devsel, IRQ 48
        Bus: primary=00, secondary=01, subordinate=01, sec-latency=0
        I/O behind bridge: 00000000-00000fff [size=4K]
        Memory behind bridge: [disabled]
        Prefetchable memory behind bridge: [disabled]
        Capabilities: [48] Power Management version 3
        Capabilities: [ac] Express Root Port (Slot-), MSI 00
        Capabilities: [100] Advanced Error Reporting
        Kernel driver in use: pcieport
lspci: Unable to load libkmod resources: error -12

root@OpenWrt:/sys/bus/pci# ls devices/ -al
drwxr-xr-x    2 root     root             0 Jan  1  1970 .
drwxr-xr-x    5 root     root             0 Jan  1  1970 ..
lrwxrwxrwx    1 root     root             0 Jan  1  1970 0000:00:00.0 -> ../../../devices/pci0000:00/0000:00:00.0

root@OpenWrt:/sys/bus/pci# ls drivers/ -al
drwxr-xr-x    6 root     root             0 Jul 11 10:51 .
drwxr-xr-x    5 root     root             0 Jan  1  1970 ..
drwxr-xr-x    2 root     root             0 Jul 11 10:52 b43-pci-bridge
drwxr-xr-x    2 root     root             0 Jul 11 10:52 bcma-pci-bridge
drwxr-xr-x    2 root     root             0 Jul 11 10:52 brcmfmac
drwxr-xr-x    2 root     root             0 Jul 11 10:52 pcieport

root@OpenWrt:/# cat /proc/iomem
00000000-07ffffff : System RAM
  00010000-006327f3 : Kernel code
  006327f4-00731c3f : Kernel data
  01610000-01644757 : Kernel bss
100000b0-100000bf : 10000200.nand nand-int-base
100000c0-100000c7 : 100000c0.pin-controller dirout
100000c8-100000cf : 100000c0.pin-controller dat
100000d0-100000d3 : 100000c0.pin-controller led
100000d8-100000db : 100000c0.pin-controller mode
100000dc-100000df : 100000c0.pin-controller ctrl
100000f8-100000fb : 100000c0.pin-controller basemode
10000180-10000197 : 10000180.serial serial@10000180
10000200-1000037f : 10000200.nand nand
10000600-100007ff : 10000200.nand nand-cache
10000800-10000f0b : 10000800.spi spi@10000800
10001000-100015ff : 10001000.spi spi@10001000
10001900-10001923 : 10001900.led-controller led-controller@10001900
11000000-11efffff : bcm63xx PCIe memory space
b000009c-b00000a7 : bcm63xx-wdt
b0002500-b00025ff : ehci-platform
  b0002500-b00025ff : ehci-platform ehci-platform
b0002600-b00026ff : ohci-platform
  b0002600-b00026ff : ohci-platform ohci-platform
b000d800-b000d8ff : bcm63xx_enet_shared.0
  b000d800-b000d8ff : bcm63xx_enet_shared.0 bcm63xx_enet_shared.0
b000da00-b000dbff : bcm63xx_enet_shared.0
  b000da00-b000dbff : bcm63xx_enet_shared.0 bcm63xx_enet_shared.0
b000dc00-b000ddff : bcm63xx_enet_shared.0
  b000dc00-b000ddff : bcm63xx_enet_shared.0 bcm63xx_enet_shared.0
b0700000-b070ffff : bcm63xx_enetsw.0

```
