# Capstone Project – A Small School

## Physical Topology
![image](https://github.com/user-attachments/assets/ee903931-986d-4851-b0d0-fc2628f31e24)

### Design Objectives

- **High Availability**: Redundant core devices and links ensure no single point of failure disrupts the network.
- **Segregated Management**: VLANs separate network traffic from different zones, enhancing security and efficiency.
- **Centralized Services**: Servers are centrally located in the server room to provide DHCP, storage, and virtualization services.
- **Security**: Traffic segregation and control are implemented using VLANs and ACLs.

### Physical Topology and Room Allocation

#### Core Devices

- **Core Routers (R1 and R2):**
  - Located in the Central Networking Room.
  - Core routers provide backbone routing for the campus network, connecting to the Internet and running dynamic routing protocol, such as OSPF.
  - The two core routers are interconnected via cross-links to ensure high availability, allowing network continuity even in the event of a single router failure.

- **Multilayer Switches (MSW1 and MSW2):**
  - Located in the Central Networking Room.
  - These switches offer multi-layer switching capabilities, enabling routing communication between classrooms and departments.
  - L3 switches are interconnected via high-bandwidth links and form a redundant network with the core routers to ensure network stability.

#### Access Layer Devices

- **L2 Switches:**
  - Deployed in classrooms, staff offices, and server rooms to provide network access for end-user devices.
  - Devices connected to these switches include student computers, faculty devices, and servers.

- **Network Segmentation:**
  - **NSA Classrooms (Room 207)** and **SD Classrooms (Room 209):** Provide network access for student and instructor computers and support communication with local servers.
  - **Faculty Offices (Room 301 and Room 302):** Offer dedicated network access for teachers and staff to support their administrative and educational needs.
  - **Server Room (Servers Room):** Provides virtualized services, centralized storage, and network management capabilities.

#### Servers and Virtualization

- **Virtualization Platform:**
  - Deployed on Proxmox virtualization platforms (PROX HYP1 and PROX HYP2), forming a high-availability cluster.
  - Offers essential network services, including DHCP, FTP, and TFTP, to support smooth campus network operations.

- **Storage Devices:**
  - Centralized storage devices are deployed to store teaching materials and shared files, with efficient integration into the virtualization platform.

#### Key Network Features

- **Redundant Design:**
  - Core devices (routers and switches) are interconnected via cross-links to prevent single points of failure and enhance network availability.

- **Network Segmentation and Isolation:**
  - VLAN technology is used to separate students, faculty, and server networks, ensuring the independence and security of each area.

- **Centralized Resource Management:**
  - The server room provides high-performance computing and storage resources, enabling centralized management and allocation to meet multi-user demands.

### Wi-Fi Network Features

- **Campus Dedicated SSID (CITE):**
![image](https://github.com/user-attachments/assets/b0d80e02-8819-4a74-b9cb-20729e0f1330)
  - **Target Users:** Faculty and students at the school.
  - **Functionality:** Provides internet access for internal users of the school. Currently managed with a high-strength password to ensure basic network security. Additionally, the SSID is hidden, allowing only internal personnel with necessary credentials to connect.
  - **Future Plans:** A Wireless LAN Controller (WLC) will be introduced for centralized management, and a RADIUS server will be configured to enhance authentication efficiency and security, achieving more intelligent and precise network control.

- **Guest SSID (CITE-GUEST):**
![image](https://github.com/user-attachments/assets/7e7fef62-bf40-4c00-ac81-e4eac5b59e44)
  - **Target Users:** External visitors.
  - **Functionality:** Provides internet access for visitors, isolated from the internal campus network to ensure the security of the internal network.
  - **Authentication Method:** Web-based authentication is used, requiring visitors to log in via a web portal before accessing the network. This approach enhances flexibility and security in visitor management.

### Physical Topology Table

| Device         | Interface   | Destination              |
|----------------|-------------|--------------------------|
| R1             | G0/0/0      | ISP                      |
|                | G0/0/1      | MSW1                     |
| R2             | G0/0/0      | ISP                      |
|                | G0/0/1      | MSW2                     |
| MSW1           | F0/10       | R1                       |
|                | F0/1        | S1-trunk                 |
|                | F0/3        | S2-trunk                 |
|                | F0/5        | S3-trunk                 |
|                | F0/23-24    | MSW2-ethchannel          |
| MSW2           | F0/10       | R2                       |
|                | F0/2        | S1-trunk                 |
|                | F0/4        | S2-trunk                 |
|                | F0/6        | S3-trunk                 |
|                | F0/23       | MSW2-ethchannel          |
|                | F0/24       |                          |
| S1             | F0/1        | MSW1-Primary             |
|                | F0/2        | MSW2-Standby             |
| S2             | F0/1        | MSW1-Primary             |
|                | F0/2        | MSW2-Standby             |
| S3             | F0/1        | MSW1-Primary             |
|                | F0/2        | MSW2-Standby             |
| PROX HYP 1     | F0/24       | SW3                      |
| PROX HYP 2     | F0/16       |                          |
| Turenas        | G0/2        |                          |
| Windows 2022   | G0/1        |                          |
| Wi-Fi Router   | LAN1        |                          |

## Logical Topology
![image](https://github.com/user-attachments/assets/717db76f-7a91-4fa3-8984-b7cdd8b9a085)


### Core Layer

#### Logical Relationships

- The core layer consists of two core routers (R1 and R2) and two multilayer switches (MSW1 and MSW2), responsible for campus network routing and gateway functions:
  - Core routers run OSPF dynamic routing protocol (Process ID 1) for dynamic route learning and fault failover.
  - Multilayer switches are interconnected with high-bandwidth links and provide redundant gateways using HSRP (Hot Standby Router Protocol).
  - The Wi-Fi device is connected to MSW2 and provides internet access to students, teachers, and staff. A guest network is configured on the Wi-Fi device for visitors, using a separate SSID.

#### IP Addressing

| Device        | Interface       | IP Address        | Subnet               | Function           |
|---------------|-----------------|-------------------|----------------------|--------------------|
| R1            | G0/0/0          | 10.128.250.241    | 10.128.250.240/30    | Connect to ISP     |
|               | G0/0/1          | 192.168.100.1     | 192.168.100.0/30     | Connect to MSW1    |
| R2            | G0/0/0          | 10.128.250.242    | 10.128.250.240/30    | Connect to ISP     |
|               | G0/0/1          | 192.168.100.5     | 192.168.100.4/30     | Connect to MSW2    |

#### Multilayer Switches

| VLAN ID | Virtual Gateway | Primary Switch IP | Secondary Switch IP | Subnet          |
|---------|-----------------|-------------------|---------------------|-----------------|
| VLAN 10 | 192.168.10.254  | 192.168.10.252    | 192.168.10.253      | 192.168.10.0/24 |
| VLAN 20 | 192.168.20.254  | 192.168.20.252    | 192.168.20.253      | 192.168.20.0/24 |
| VLAN 30 | 192.168.30.254  | 192.168.30.252    | 192.168.30.253      | 192.168.30.0/24 |
| VLAN 40 | 192.168.40.254  | 192.168.40.252    | 192.168.40.253      | 192.168.40.0/24 |

### Access Layer

#### Logical Relationships

- The access layer connects Layer 2 switches (S1, S2, and S3) to end-user devices in various departments and functional areas:
  - Access switches (S1, S2, and S3) connect to the core switches (MSW1 and MSW2) via Fast Ethernet interfaces to provide redundancy.
  - Each switch serves devices in a specific VLAN, ensuring traffic isolation and security.

#### VLAN Tabling

| Device | Interface | VLAN ID | Description         |
|--------|-----------|---------|---------------------|
| S1     | F0/1      | VLAN 10 | Provides access for student devices |
| S2     | F0/1      | VLAN 20 | Provides access for Instructors and Staff devices |
| S3     | F0/1      | VLAN 40 | Provides access for server equipment |

### Devices Layer

#### Logical Relationships

- The devices layer consists of servers and endpoint devices, each located in its respective VLAN and managed through the routing devices in the core layer:
  - Student, faculty, and staff devices are assigned to their respective VLANs.
  - Servers are centralized in VLAN 40, providing global services such as DHCP, FTP, and storage.

#### Server IP Configuration

| Device Name  | Type   | IP Address       | Function                  |
|--------------|--------|------------------|---------------------------|
| PROX HYP 1   | Physic | 192.168.40.95    | Virtualization platform   |
| PROX HYP 2   |        | 192.168.40.96    | Virtualization platform   |
| Turenas      |        | 192.168.40.98    | Centralized storage       |
| Windows 2022 |        | 192.168.40.100   | DHCP, FTP, TFTP services  |
| DC-1         | VMs    | 192.168.40.89    | Active Directory services |
| DC-2         |        | 192.168.40.90    | DNS services              |
| Backup       |        | 192.168.40.101   | Backup all servers        |
| Fedora       |        | 192.168.40.88    | Webservice, Database services |

#### Endpoint Device IP Configuration

| Device Type     | Subnet           | Description                     |
|-----------------|------------------|---------------------------------|
| Student Devices | 192.168.10.0/24 | Devices used in classrooms      |
| Faculty Devices | 192.168.20.0/24 | Devices used for teaching       |
| Staff Devices   | 192.168.30.0/24 | Devices used for administration |
| IT Devices      | 192.168.40.0/24 | Devices used for IT             |

## Running Configurations
# Routers Configuration Files

## Core Router R1 Configuration

```plaintext
Current configuration : 3468 bytes
!
! Last configuration change at 01:40:14 UTC Wed Dec 11 2024
! NVRAM config last updated at 01:40:44 UTC Wed Dec 11 2024
!
version 16.6
service timestamps debug datetime msec
service timestamps log datetime
service password-encryption
platform qfp utilization monitor load 80
no platform punt-keepalive disable-kernel-core
!
hostname R1
!
boot-start-marker
boot-end-marker
!
vrf definition Mgmt-intf
 !
 address-family ipv4
 exit-address-family
 !
 address-family ipv6
 exit-address-family
!
security passwords min-length 7
enable secret 5 $1$9EeG$hwqdcObXwlapFBT/o0ngD1
!
no aaa new-model
!
ip domain name thecite.ca
!
login block-for 120 attempts 3 within 60
!
subscriber templating
!
multilink bundle-name authenticated
!
license udi pid ISR4321/K9 sn FDO22142GAX
license boot suite AdvUCSuiteK9
license boot level securityk9
diagnostic bootup level minimal
!
spanning-tree mode pvst
spanning-tree extend system-id
!
username R1admin secret 5 $1$UDF7$CXn4Vqzgxtie9hfc/jHvM1
!
redundancy
 mode none
!
no cdp run
!
interface GigabitEthernet0/0/0
 description Link to ISP
 ip address 10.128.250.241 255.255.255.0
 ip nat outside
 negotiation auto
!
interface GigabitEthernet0/0/1
 description Link to MSW1
 ip address 192.168.100.1 255.255.255.252
 ip nat inside
 negotiation auto
!
router ospf 1
 router-id 1.1.1.1
 network 192.168.100.0 0.0.0.3 area 0
 default-information originate
!
ip nat inside source list 100 interface GigabitEthernet0/0/0 overload
ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/0/0
ip route 192.168.0.0 255.255.0.0 GigabitEthernet0/0/1
!
ip ssh version 2
ip access-list standard SSH
 permit 192.168.40.25
 deny   any
access-list 100 permit ip 192.168.0.0 0.0.255.255 any
!
banner motd ^C
 * Welcome to the Network Management System *
 * Authorized Access Only. All Activities are Monitored and Logged. *
 * Contact IT: info@cite.ca. Last Updated: [08/12/2024]. *
^C
!
line con 0
 password 7 0307521F035E731F1A
 logging synchronous
 login
 transport input none
!
line vty 0 4
 access-class SSH in
 exec-timeout 5 0
 login local
 transport input ssh
line vty 5 15
 access-class SSH in
 exec-timeout 5 0
 login local
 transport input ssh
!
ntp master 2
end
```

## Core Router R2 Configuration

```plaintext
Current configuration : 6038 bytes
!
! Last configuration change at 01:38:41 UTC Wed Dec 11 2024
! NVRAM config last updated at 01:38:59 UTC Wed Dec 11 2024
!
version 16.12
service timestamps debug datetime msec
service timestamps log datetime
service call-home
platform qfp utilization monitor load 80
hostname R2
!
boot-start-marker
boot-end-marker
!
vrf definition Mgmt-intf
 !
 address-family ipv4
 exit-address-family
 !
 address-family ipv6
 exit-address-family
!
security passwords min-length 7
enable secret 9 $9$F/Jx.kEedH4Mlk$35PRT0xOS.dnBRyacLQGQeyzKGoH1Ztu5qR.HXbqoiI
!
no aaa new-model
call-home
 contact-email-addr sch-smart-licensing@cisco.com
 profile "CiscoTAC-1"
  active
  destination transport-method http
!
ip domain name thecite.ca
!
login block-for 120 attempts 3 within 60
login on-success log
!
crypto pki trustpoint SLA-TrustPoint
 enrollment pkcs12
 revocation-check crl
!
router ospf 1
 router-id 2.2.2.2
 network 192.168.100.4 0.0.0.3 area 0
 default-information originate
!
ip forward-protocol nd
no ip http server
ip http secure-server
ip route 0.0.0.0 0.0.0.0 GigabitEthernet0/0/0
ip route 192.168.0.0 255.255.0.0 GigabitEthernet0/0/1
!
ip ssh version 2
ip access-list standard SSH
 permit 192.168.40.25
 deny   any
ip access-list extended 100
 permit ip 192.168.0.0 0.0.255.255 any
!
banner motd ^C
 * Welcome to the Network Management System *
 * Authorized Access Only. All Activities are Monitored and Logged. *
 * Contact IT: info@cite.ca. Last Updated: [08/12/2024]. *
^C
!
line con 0
 password cite1234
 logging synchronous
 login
!
line vty 0 4
 access-class SSH in
 exec-timeout 5 0
 login local
 transport input ssh
line vty 5 15
 access-class SSH in
 exec-timeout 5 0
 login local
 transport input ssh
!
ntp server 192.168.100.1
end
```

# Multilayer Switches Configuration Files

## MSW1 Configuration

```plaintext
Current configuration : 5421 bytes
!
! Last configuration change at 03:22:27 UTC Wed Dec 11 2024
! NVRAM config last updated at 01:51:58 UTC Wed Dec 11 2024
!
version 12.2
no service pad
service timestamps debug uptime
service timestamps log datetime
no service password-encryption
!
hostname MSW1
!
enable secret 5 $1$L1U4$mg1iwFChC8XSJFajvhRLV.
!
username MSW1admin secret 5 $1$S2B0$8VI8TN59AXB.MNyQQn2pO.
no aaa new-model
system mtu routing 1500
ip subnet-zero
ip routing
no ip domain-lookup
ip domain-name thecite.ca
!
ip ssh version 2
login block-for 120 attempts 3 within 60
!
spanning-tree mode pvst
spanning-tree extend system-id
spanning-tree vlan 10,20 priority 24576
spanning-tree vlan 30,40 priority 28672
!
vlan internal allocation policy ascending
!
interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface FastEthernet0/1
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
 switchport mode trunk
!
interface FastEthernet0/10
 no switchport
 ip address 192.168.100.2 255.255.255.252
!
interface Vlan10
 ip address 192.168.10.252 255.255.255.0
 ip access-group VLAN10 in
 ip helper-address 192.168.40.100
 standby 10 ip 192.168.10.254
 standby 10 priority 120
 standby 10 preempt
!
interface Vlan20
 ip address 192.168.20.252 255.255.255.0
 ip access-group VLAN20 in
 ip helper-address 192.168.40.100
 standby 20 ip 192.168.20.254
 standby 20 priority 120
 standby 20 preempt
!
interface Vlan30
 ip address 192.168.30.252 255.255.255.0
 ip helper-address 192.168.40.100
 standby 30 ip 192.168.30.254
 standby 30 priority 110
 standby 30 preempt
!
interface Vlan40
 ip address 192.168.40.252 255.255.255.0
 ip access-group VLAN40 in
 standby 40 ip 192.168.40.254
 standby 40 priority 110
 standby 40 preempt
!
router ospf 1
 router-id 3.3.3.3
 log-adjacency-changes
 network 192.168.0.0 0.0.255.255 area 0
 default-information originate
!
ip classless
ip route 0.0.0.0 0.0.0.0 192.168.100.1
ip http server
ip http secure-server
!
ip access-list standard SSH
 permit 192.168.40.25
 deny   any
!
no cdp run
!
control-plane
!
banner motd ^C
 *                 Welcome to the Network Management System                  *
 *  -----------------------------------------------------------------------  *
 *      Authorized Access Only. All Activities are Monitored and Logged.     *
 *    Please ensure compliance with the organization's policies and best     *
 *                         security practices.                               *
 *     For support or queries, contact the IT department at (info@cite.ca).  *
 *         System Last Updated: [08/12/2024] | Version: [1.6.8]              *
^C
!
line con 0
 password cite1234
 logging synchronous
 login
line vty 0 4
 access-class SSH in
 exec-timeout 5 0
 login local
 transport input ssh
line vty 5 15
 access-class SSH in
 exec-timeout 5 0
 login local
 transport input ssh
!
ntp clock-period 36031231
ntp server 192.168.100.1
end
```

## MSW2 Configuration

```plaintext
Current configuration : 4351 bytes
!
! Last configuration change at 05:07:08 UTC Wed Dec 11 2024
! NVRAM config last updated at 01:57:39 UTC Wed Dec 11 2024
!
version 12.2
no service pad
service timestamps debug datetime msec
service timestamps log datetime
no service password-encryption
!
hostname MSW2
!
enable secret 5 $1$/U6w$P.2ISPgHT0H6.jW8oeA9V/
!
username MSW2admin secret 5 $1$30mR$srtEULQZp3DGzd2nJaI3h0
no aaa new-model
system mtu routing 1500
ip subnet-zero
ip routing
no ip domain-lookup
ip domain-name thecite.ca
!
login block-for 120 attempts 3 within 60
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
ip ssh version 2
!
interface Port-channel1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!
interface FastEthernet0/2
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
 switchport mode trunk
!
interface FastEthernet0/10
 no switchport
 ip address 192.168.100.6 255.255.255.252
!
interface Vlan10
 ip address 192.168.10.253 255.255.255.0
 ip access-group VLAN10 in
 ip helper-address 192.168.40.100
 standby 10 ip 192.168.10.254
 standby 10 priority 110
 standby 10 preempt
!
interface Vlan20
 ip address 192.168.20.253 255.255.255.0
 ip access-group VLAN20 in
 ip helper-address 192.168.40.100
 standby 20 ip 192.168.20.254
 standby 20 priority 110
 standby 20 preempt
!
interface Vlan30
 ip address 192.168.30.253 255.255.255.0
 ip helper-address 192.168.40.100
 standby 30 ip 192.168.30.254
 standby 30 priority 120
 standby 30 preempt
!
interface Vlan40
 ip address 192.168.40.253 255.255.255.0
 ip access-group VLAN40 in
 standby 40 ip 192.168.40.254
 standby 40 priority 120
 standby 40 preempt
!
router ospf 1
 router-id 4.4.4.4
 log-adjacency-changes
 network 192.168.0.0 0.0.255.255 area 0
 default-information originate
!
ip classless
ip route 0.0.0.0 0.0.0.0 192.168.100.5
ip http server
ip http secure-server
!
ip access-list standard SSH
 permit 192.168.40.25
 deny   any
!
no cdp run
!
control-plane
!
banner motd ^C
 *                 Welcome to the Network Management System                  *
 *  -----------------------------------------------------------------------  *
 *      Authorized Access Only. All Activities are Monitored and Logged.     *
 *    Please ensure compliance with the organization's policies and best     *
 *                         security practices.                               *
 *     For support or queries, contact the IT department at (info@cite.ca).  *
 *         System Last Updated: [08/12/2024] | Version: [1.6.8]*
^C
!
line con 0
 password cite1234
 logging synchronous
 login
line vty 0 4
 access-class SSH in
 exec-timeout 5 0
 login local
 transport input ssh
line vty 5 15
 access-class SSH in
 exec-timeout 5 0
 login local
 transport input ssh
!
ntp clock-period 36031145
ntp server 192.168.100.1
end
```
# Layer 2 Switches Configuration Files

## L2SW1 Configuration

```plaintext
Current configuration : 9643 bytes
!
! Last configuration change at 23:29:56 UTC Mon Mar 1 1993
! NVRAM config last updated at 05:16:45 UTC Wed Dec 11 2024
!
version 15.0
no service pad
service timestamps debug datetime msec
service timestamps log datetime
service password-encryption
!
hostname L2SW1
!
enable secret 5 $1$MkHB$s7fPXis2LnGvi.ze93T4M0
!
no aaa new-model
system mtu routing 1500
!
no ip domain-lookup
login block-for 120 attempts 3 within 60
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
interface FastEthernet0/1
 description Trunk port connected with MLSW1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
 switchport mode trunk
 switchport nonegotiate
!
interface FastEthernet0/2
 description Trunk port connected with MLSW2
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
 switchport mode trunk
 switchport nonegotiate
!
interface FastEthernet0/3
 description Access Ports
 switchport access vlan 10
 switchport mode access
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 switchport port-security
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/4
 description Access Ports
 switchport access vlan 10
 switchport mode access
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 switchport port-security
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/10
 description Access Ports
 switchport access vlan 10
 switchport mode access
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 switchport port-security
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface Vlan1
 no ip address
 shutdown
!
no ip http server
no ip http secure-server
!
banner motd ^C
 * Welcome to the Network Management System *
 * Authorized Access Only. All Activities are Monitored and Logged. *
 * Last Updated: [06/12/2024] | Version: [1.6.8] *
^C
!
line con 0
 exec-timeout 5 0
 password 7 15110218017B79777C
 logging synchronous
 login
line vty 0 4
 login
line vty 5 15
 login
!
ntp server 192.168.100.1
end
```

## L2SW2 Configuration

```plaintext
Current configuration : 9206 bytes
!
! Last configuration change at 23:21:54 UTC Mon Mar 1 1993
! NVRAM config last updated at 05:15:44 UTC Wed Dec 11 2024
!
version 15.0
no service pad
service timestamps debug datetime msec
service timestamps log datetime
service password-encryption
!
hostname L2SW2
!
enable secret 5 $1$f3.P$vUh39jkmGYe8MhodUFFj1/
!
no aaa new-model
system mtu routing 1500
!
no ip domain-lookup
login block-for 120 attempts 3 within 60
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
interface FastEthernet0/1
 description Trunk to MLSW1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
 switchport mode trunk
 switchport nonegotiate
!
interface FastEthernet0/2
 description Trunk to MLS2
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
 switchport mode trunk
 switchport nonegotiate
!
interface FastEthernet0/10
 description Access Ports
 switchport access vlan 20
 switchport mode access
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 switchport port-security
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface Vlan1
 no ip address
 shutdown
!
no ip http server
no ip http secure-server
!
banner motd ^C
 * Welcome to the Network Management System *
 * Authorized Access Only. All Activities are Monitored and Logged. *
 * Last Updated: [06/12/2024] | Version: [1.6.8] *
^C
!
line con 0
 exec-timeout 5 0
 password 7 104D000D0046405858
 logging synchronous
 login
line vty 0 4
 login
line vty 5 15
 login
!
end
```

## L2SW3 Configuration

```plaintext
Current configuration : 3844 bytes
!
! Last configuration change at 05:13:47 UTC Wed Dec 11 2024
! NVRAM config last updated at 05:13:55 UTC Wed Dec 11 2024
!
version 15.0
no service pad
service timestamps debug datetime msec
service timestamps log datetime
service password-encryption
!
hostname L2SW3
!
enable secret 5 $1$4DK2$pbrJk70kwP9Ggd2gmNd9/1
!
no aaa new-model
system mtu routing 1500
!
no ip domain-lookup
login block-for 120 attempts 3 within 60
!
spanning-tree mode pvst
spanning-tree extend system-id
!
vlan internal allocation policy ascending
!
interface FastEthernet0/1
 description Trunk to MLSW1
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
 switchport mode trunk
 switchport nonegotiate
!
interface FastEthernet0/2
 description Trunk to MLSW2
 switchport trunk native vlan 99
 switchport trunk allowed vlan 10,20,30,40,99
 switchport mode trunk
 switchport nonegotiate
!
interface FastEthernet0/3
 description Access Ports
 switchport access vlan 40
 switchport mode access
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 switchport port-security
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface FastEthernet0/10
 description Proxmox Server
 switchport access vlan 40
 switchport mode access
 switchport port-security maximum 3
 switchport port-security violation restrict
 switchport port-security mac-address sticky
 switchport port-security
 spanning-tree portfast
 spanning-tree bpduguard enable
!
interface Vlan1
 no ip address
 shutdown
!
ip http server
ip http secure-server
!
banner motd ^C
 * Welcome to the Network Management System *
 * Authorized Access Only. All Activities are Monitored and Logged. *
 * Last Updated: [06/12/2024] | Version: [1.6.8] *
^C
!
line con 0
 exec-timeout 5 0
 password 7 15110218017B79777C
 logging synchronous
 login
line vty 0 4
 login
line vty 5 15
 login
!
end
```
