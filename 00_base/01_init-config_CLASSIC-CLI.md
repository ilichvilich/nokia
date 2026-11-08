#### INIT
- **Increase terminal width**  
`A:SROS-R2# environment terminal width 500`  
- **Provision power**  
```
configure system power-shelf 1 power-shelf-type "ps-a4-shelf-dc"
configure system power-shelf 1 power-module 1 power-module-type "ps-a-dc-6000"
configure system power-shelf 1 power-module 2 power-module-type "ps-a-dc-6000"
configure system power-shelf 1 power-module 3 power-module-type "ps-a-dc-6000"
configure system power-shelf 1 power-module 4 power-module-type "ps-a-dc-6000"
```
```
# show chassis power-shelf
=========================================================================
Power Shelf Summary
=========================================================================
Slot       Provisioned Type             Admin Operational   Zone   Input Output
               Equipped Type (if diff)  State State                Mode
-------------------------------------------------------------------------------
1          ps-a4-shelf-dc               up    up            1      60A   on
```

```
# show chassis power-shelf 1 power-module
============================================================================
Power Shelf Summary
============================================================================
Slot       Provisioned Type             Admin Operational   Zone   Input Output
               Equipped Type (if diff)  State State                Mode
-------------------------------------------------------------------------------
1          ps-a4-shelf-dc               up    up            1      60A   on
============================================================================

============================================================================
Power Module Summary
============================================================================
Slot       Provisioned Type             Admin Operational   Input  Zone
               Equipped Type (if diff)  State State         A B
-------------------------------------------------------------------------------
1          ps-a-dc-6000                 up    up            Y Y    1
2          ps-a-dc-6000                 up    up            Y Y    1
3          ps-a-dc-6000                 up    up            Y Y    1
4          ps-a-dc-6000                 up    up            Y Y    1
```
- **Provision ports**
```
configure card 1 mda 1 mda-type "s18-100gb-qsfp28" level "he1200g+"
```
```
# show card
============================================================================
Card Summary
============================================================================
Slot      Provisioned Type                         Admin Operational   Comments
              Equipped Type (if different)         State State
-------------------------------------------------------------------------------
1         xcm-1s                                   up    up
A         cpm-1s                                   up    up/active



# show mda
============================================================================
MDA Summary
============================================================================
Slot  Mda   Provisioned Type                            Admin     Operational
                Equipped Type (if different)            State     State
-------------------------------------------------------------------------------
1     1     s18-100gb-qsfp28:he1200g+                   up        up
============================================================================



# show port
===============================================================================
Ports on Slot 1
===============================================================================
Port          Admin Link Port    Cfg  Oper LAG/ Port Port Port   C/QS/S/XFP/
Id            State      State   MTU  MTU  Bndl Mode Encp Type   MDIMDX
-------------------------------------------------------------------------------
1/1/c1        Down       Down                             conn   100GBASE-LR4*
1/1/c2        Down       Down                             conn   100GBASE-LR4*
1/1/c3        Down       Down                             conn   100GBASE-LR4*
1/1/c4        Down       Down                             conn   100GBASE-LR4*
1/1/c5        Down       Down                             conn   100GBASE-LR4*
1/1/c6        Down       Down                             conn   100GBASE-LR4*
1/1/c7        Down       Down                             conn   100GBASE-LR4*
1/1/c8        Down       Down                             conn   100GBASE-LR4*
1/1/c9        Down       Down                             conn   100GBASE-LR4*
1/1/c10       Down       Down                             conn   100GBASE-LR4*
1/1/c11       Down       Down                             conn   100GBASE-LR4*
1/1/c12       Down       Down                             conn   100GBASE-LR4*
1/1/c13       Down       Down                             conn   100GBASE-LR4*
1/1/c14       Down       Down                             conn   100GBASE-LR4*
1/1/c15       Down       Down                             conn   100GBASE-LR4*
1/1/c16       Down       Down                             conn   100GBASE-LR4*
1/1/c17       Down       Down                             conn   100GBASE-LR4*
1/1/c18       Down       Down                             conn   100GBASE-LR4*
===============================================================================
Ports on Slot A
===============================================================================
Port          Admin Link Port    Cfg  Oper LAG/ Port Port Port   C/QS/S/XFP/
Id            State      State   MTU  MTU  Bndl Mode Encp Type   MDIMDX
-------------------------------------------------------------------------------
A/1           Up    Yes  Up      1514 1514    - netw null faste  MDI
A/3           Down  No   Down    1514 1514    - netw null faste
A/4           Down  No   Down    1514 1514    - netw null faste
```

- **Set hostname**  
`configure system name SROS-R2`  

- **Set admin password**  
`configure system security user "admin" password Nokia123`  

- **Breakout a 100g port into 10x10g:**
  - **EVE shows p1/1/1 which is 1/1/c1 by default**
    - **After breakout same p1/1/1 becomes 1/1/c1/1**
```
conf port 1/1/c1 connector breakout c10-10g
conf port 1/1/c2 connector breakout c10-10g
```
```
# show port | match 1/1/c11
1/1/c11       Down       Down                             conn   100GBASE-LR4*
# show port | match 1/1/c11
1/1/c11       Down       Down                             conn   100GBASE-LR4*
1/1/c11/1     Down  No   Down    9212 9212    - netw null xgige
1/1/c11/2     Down  No   Down    9212 9212    - netw null xgige
1/1/c11/3     Down  No   Down    9212 9212    - netw null xgige
1/1/c11/4     Down  No   Down    9212 9212    - netw null xgige
1/1/c11/5     Down  No   Down    9212 9212    - netw null xgige
1/1/c11/6     Down  No   Down    9212 9212    - netw null xgige
1/1/c11/7     Down  No   Down    9212 9212    - netw null xgige
1/1/c11/8     Down  No   Down    9212 9212    - netw null xgige
1/1/c11/9     Down  No   Down    9212 9212    - netw null xgige
1/1/c11/10    Down  No   Down    9212 9212    - netw null xgige
```
- **Admin enable a port:**
```
# show port | match 1/1/c1/2
1/1/c1/2      Down  No   Down    9212 9212    - netw null xgige
# configure port 1/1/c1 no shutdown
# configure port 1/1/c1/2 no shutdown
# show port | match 1/1/c1/2
1/1/c1/2      Up    No   Down    9212 9212    - netw null xgige
```

- **Show config**  
`# admin display-config`   
  - **View config current section config with `info` command:**  
  - Can be copied and pasted into another port config
```
R2>cfg>port>eth>lldp>dstmac# info
----------------------------------------------
                    admin-status tx-rx
                    tx-tlvs port-desc sys-name
                    port-id-subtype tx-if-name

R2>config>port# info
----------------------------------------------
        ethernet
            mtu 2000
            lldp
                dest-mac nearest-bridge
                    admin-status tx-rx
                    tx-tlvs port-desc sys-name
                    port-id-subtype tx-if-name
                exit
            exit
        exit
        no shutdown
```
  
- **Set MTU**  
`# configure port 1/1/c1/1 ethernet mtu 2000`  
- **Enable LLDP**
  - LLDP uses **special multicast MACs** to transmit information:
    - **nearest-bridge** — 01:80:C2:00:00:0E
    - **nearest-non-tpmr** — 01:80:C2:00:00:03
    - **nearest-customer** — 01:80:C2:00:00:00
```
configure port 1/1/c1/1 ethernet lldp dest-mac nearest-bridge admin-status tx-rx
configure port 1/1/c1/1 ethernet lldp dest-mac nearest-bridge tx-tlvs port-desc sys-name
configure port 1/1/c1/1 ethernet lldp dest-mac nearest-bridge port-id-subtype ?
  - no port-id-subtype
  - port-id-subtype {tx-if-alias|tx-if-name|tx-local}
 <tx-if-alias|tx-if*> : keywords - Default: tx-local
                        tx-if-alias (1) - transmit the ifAlias IF-MIB string
                        tx-if-name (5) - transmit the ifName IF-MIB string //here
                        tx-local (7) - transmit the interface ifIndex value

*A:SROS-R2# configure port 1/1/c1/1 ethernet lldp dest-mac nearest-bridge port-id-subtype tx-if-name
```


<img width="680" height="560" alt="image" src="https://github.com/user-attachments/assets/5d3ad9e6-8ff4-4f52-93a5-af4b1991c6bd" />

#### Loopback, L3-LAG
- **Loopback in Nokia is an interface named System:**
```
R2#configure router
R2>config>router# info
----------------------------------------------
#--------------------------------------------------
echo "IP Configuration"
#--------------------------------------------------
!
!
        interface "system"
            no shutdown
        exit
!
!
R2>config>router>if# address 198.162.1.2/32
!
!
*A:SROS-R2>config>router>if# info
----------------------------------------------
            address 198.162.1.2/32
            no shutdown
```
- **Create an L3 LAG**  
```
R2# configure lag 1
R2>config>lag$   lacp active
R2>config>lag$ port 1/1/c1/1
R2>config>lag$ no shutdown
-R2>config>lag$ info
----------------------------------------------
        port 1/1/c1/1
        lacp active administrative-key 32768
        no shutdown
```
- **Assign IP:**  
```
A:SROS-R2# configure router interface LAG-1
*A:SROS-R2>config>router>if# port lag-1
*A:SROS-R2>config>router>if$ address 10.0.12.1/31
*A:SROS-R2>config>router>if$ no shutdown
!
*A:SROS-R2>config>router>if# ping 10.0.12.0
PING 10.0.12.0 56 data bytes
64 bytes from 10.0.12.0: icmp_seq=1 ttl=255 time=9.63ms.
64 bytes from 10.0.12.0: icmp_seq=2 ttl=255 time=2.22ms.

```

#### Access port and SVI
- **ACCESS server-facing port**
```
A:SROS-R2# configure port 1/1/c2/1
A:SROS-R2>config>port# shutdown
*A:SROS-R2>config>port# ethernet mode access
*A:SROS-R2>config>port# ethernet encap-type dot1q
```
- **Create a VLAN:**
  - `service vpls 110` - virtual switch inside a router
    - 110 - unique service-id
  - `allow-ip-int-bind` - allows L3 SVI connection to a virtual switch
  - `sap` - ServiceAccessPoint
```
*A:SROS-R2>config# service vpls 110 name VLAN110-VPLS customer 1 create
*A:SROS-R2>config>service>vpls$ allow-ip-int-bind
*A:SROS-R2>config>service>vpls>bind$ back
*A:SROS-R2>config>service>vpls$ sap 1/1/c2/1:0 create      //0 - expect untagged, other - vlanid
*A:SROS-R2>config>service>vpls>sap$ no shutdown
*A:SROS-R2>config>service>vpls>sap$ exit
*A:SROS-R2>config>service>vpls$ no shutdown
```
- **Create an SVI:**
  - ies - InternetEnhancedService - virtual router
    - 1100 - unique service-id
```
*A:SROS-R2>config>service#  ies 1100 name "1100" customer 1 create
*A:SROS-R2>config>service>ies# no shutdown
*A:SROS-R2>config>service>ies# interface "VLAN110" create
*A:SROS-R2>config>service>ies>if#  address 192.168.110.100/24
*A:SROS-R2>config>service>ies>if# vpls "VLAN110-VPLS"
```

#### L2-LAG trunk
- **LAG in mode=access, encaps=dot1q**
```
*A:SROS-R2>config# lag 4
        mode access
        encap-type dot1q
        port 1/1/c4/1
        lacp active 
        no shutdown
```
- **Add SAPs in VPLS services:**
```
*A:SROS-R2>config>service#  vpls 110
*A:SROS-R2>config>service>vpls# sap lag-4:110 create
*A:SROS-R2>config>service>vpls>sap$ back
*A:SROS-R2>config>service>vpls# back
*A:SROS-R2>config>service#  vpls 210
*A:SROS-R2>config>service>vpls# sap lag-4:210 create
*A:SROS-R2>config>service>vpls>sap$ back
```
- **Verify:**
```
#VPLS service config:
  vpls 110 name "VLAN110-VPLS" customer 1 create
            allow-ip-int-bind
            exit
            stp
                shutdown
            exit
            sap lag-4:110 create
                no shutdown
            exit
            sap 1/1/c2/1:0 create
                no shutdown
            exit
            no shutdown
        exit
        vpls 210 name "VLAN210" customer 1 create
            allow-ip-int-bind
            exit
            stp
                shutdown
            exit
            sap lag-4:210 create
                no shutdown
            exit
            sap 1/1/c3/1:0 create
                no shutdown
            exit
!
!
#Trunk LAG config:
A:SROS-R2# configure lag 4
A:SROS-R2>config>lag# info
        mode access
        encap-type dot1q
        port 1/1/c4/1
        lacp active administrative-key 32769
        no shutdown
!
!
#Access port config:
A:SROS-R2>config# port 1/1/c4
A:SROS-R2>config>port# info
----------------------------------------------
        connector
            breakout c10-10g
        exit
        no shutdown
!
A:SROS-R2>config>port# back
A:SROS-R2>config# port 1/1/c4/1
A:SROS-R2>config>port# info
----------------------------------------------
        ethernet
            mode access
            encap-type dot1q
            lldp
                dest-mac nearest-bridge
                    admin-status tx-rx
                    tx-tlvs port-desc sys-name
                    port-id-subtype tx-if-name
                exit
            exit
        exit
        no shutdown
```




