**OSPF**


**PC1**
```
192.168.0.2 
255.255.255.0
192.168.0.1
```

**PC2**
```
172.16.0.2
255.255.255.0
172.16.0.1
```
**Router0**

```
enable
configure terminal
hostname R1
interface GigabitEthernet0/0
ip address 1.0.0.1 255.255.255.252
no shutdown
exit
interface GigabitEthernet0/0
ip address 10.0.0.1 255.255.255.252
no shutdown
exit
interface GigabitEthernet0/1
ip address 192.168.0.1 255.255.255.0
no shutdown
exit
```

**Router1**

```
enable
configure terminal
hostname R2
interface GigabitEthernet0/0
ip address 10.0.0.2 255.255.255.252
no shutdown
exit
interface GigabitEthernet0/1
ip address 172.16.0.1 255.255.255.0
no shutdown
exit
```

**PC1**, **PC2**

```
Ping
```
```
ping 172.16.0.2

Pinging 172.16.0.2 with 32 bytes of data:

Reply from 192.168.0.1: Destination host unreachable.
Request timed out.
Reply from 192.168.0.1: Destination host unreachable.
Reply from 192.168.0.1: Destination host unreachable.

Ping statistics for 172.16.0.2:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss)
```



**OSPF**

**R1**

```
configure terminal
router ospf 1
passive-interface GigabitEthernet0/1
network 192.168.0.0 0.0.0.255 area 1
network 10.0.0.0 0.0.0.3 area 1
exit
exit
```

**R2**
```
router ospf 1
passive-interface GigabitEthernet0/1
network 172.16.0.0 0.0.0.255 area 1
network 10.0.0.0 0.0.0.3 area 1
exit
exit
```
```
R1#show ip ospf database 
            OSPF Router with ID (192.168.0.1) (Process ID 1)

                Router Link States (Area 1)

Link ID         ADV Router      Age         Seq#       Checksum Link count
192.168.0.1     192.168.0.1     449         0x80000003 0x00fbf1 2
172.16.0.1      172.16.0.1      449         0x80000003 0x006a88 2

                Net Link States (Area 1)
Link ID         ADV Router      Age         Seq#       Checksum
10.0.0.1        192.168.0.1     449         0x80000001 0x0081c6
```

```
R2#show ip ospf neighbor 


Neighbor ID     Pri   State           Dead Time   Address         Interface
192.168.0.1       1   FULL/DR         00:00:30    10.0.0.1        GigabitEthernet0/0
```
```
C:\>ping 172.16.0.2

Pinging 172.16.0.2 with 32 bytes of data:

Reply from 172.16.0.2: bytes=32 time<1ms TTL=126
Reply from 172.16.0.2: bytes=32 time<1ms TTL=126
Reply from 172.16.0.2: bytes=32 time<1ms TTL=126
Reply from 172.16.0.2: bytes=32 time=6ms TTL=126

Ping statistics for 172.16.0.2:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 6ms, Average = 1ms
```
**Router ID**

**R1**
```
configure terminal
router ospf 1
router-id 1.1.1.1
exit
do clear ip ospf process
exit
copy running-config startup-config
```
```
**R2**
configure terminal
router ospf 1
router-id 2.2.2.2
do clear ip ospf process
exit
exit
copy running-config startup-config
```

**Test**

```
R1#show ip ospf database 
            OSPF Router with ID (1.1.1.1) (Process ID 1)

                Router Link States (Area 1)

Link ID         ADV Router      Age         Seq#       Checksum Link count
2.2.2.2         2.2.2.2         38          0x80000008 0x00ef69 2
192.168.0.1     192.168.0.1     954         0x80000003 0x00fbf1 2
172.16.0.1      172.16.0.1      954         0x80000003 0x006a88 2
1.1.1.1         1.1.1.1         38          0x80000005 0x00793f 2

                Net Link States (Area 1)
Link ID         ADV Router      Age         Seq#       Checksum
10.0.0.1        1.1.1.1         38          0x80000003 0x006bf0
```
```R2#show ip ospf neighbor 


Neighbor ID     Pri   State           Dead Time   Address         Interface
1.1.1.1           1   FULL/DR         00:00:34    10.0.0.1        GigabitEthernet0/0
```

