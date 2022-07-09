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
