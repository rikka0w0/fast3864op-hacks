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
```
