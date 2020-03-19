+++
title = "Network"
weight = 4
+++

## Network 설정  

> dhcp 사용시  

```no-highlight
ifconfig_em0="DHCP"
```

> Static 설정  
> em0 의 경우 Nic Device 이름 입니다.  

```no-highlight
ifconfig_em0="inet 192.168.0.10 netmask 255.255.255.0"
defaultrouter="192.168.0.2"
```

> network 재시작  

```no-highlight
root@BSD11:~ # service netif restart
Stopping Network: lo0 em0.
lo0: flags=8048<LOOPBACK,RUNNING,MULTICAST> metric 0 mtu 16384
        options=600003<RXCSUM,TXCSUM,RXCSUM_IPV6,TXCSUM_IPV6>
        nd6 options=21<PERFORMNUD,AUTO_LINKLOCAL>
        groups: lo
em0: flags=8802<BROADCAST,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=9b<RXCSUM,TXCSUM,VLAN_MTU,VLAN_HWTAGGING,VLAN_HWCSUM>
        ether 00:0c:29:9b:2a:24
        hwaddr 00:0c:29:9b:2a:24
        nd6 options=29<PERFORMNUD,IFDISABLED,AUTO_LINKLOCAL>
        media: Ethernet autoselect (1000baseT <full-duplex>)
        status: active
Starting Network: lo0 em0.
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> metric 0 mtu 16384
        options=600003<RXCSUM,TXCSUM,RXCSUM_IPV6,TXCSUM_IPV6>
        inet6 ::1 prefixlen 128
        inet6 fe80::1%lo0 prefixlen 64 scopeid 0x2
        inet 127.0.0.1 netmask 0xff000000
        nd6 options=21<PERFORMNUD,AUTO_LINKLOCAL>
        groups: lo
em0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=9b<RXCSUM,TXCSUM,VLAN_MTU,VLAN_HWTAGGING,VLAN_HWCSUM>
        ether 00:0c:29:9b:2a:24
        hwaddr 00:0c:29:9b:2a:24
        inet 192.168.0.10 netmask 0xffffff00 broadcast 192.168.0.255
        nd6 options=29<PERFORMNUD,IFDISABLED,AUTO_LINKLOCAL>
        media: Ethernet autoselect (1000baseT <full-duplex>)
        status: active
root@BSD11:~ #
```


