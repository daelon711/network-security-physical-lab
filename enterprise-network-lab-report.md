# CyberOps - Lab 09: Physical Implementation

Group 29

Roles:
- Network Architect: member 1
- Firewall Engineer: member 2
- Switch Engineer: member 3
- Server Engineer: member 4
- Client/Test Engineer: member 5
- Documentation Lead: member 6

Group: 29

Date: 20/05/2026

## Table of Contents

Contents

- 1. Introduction ....................................................................................................................... 3
  - 1.1 Objectives .................................................................................................................... 3
- 2. Network Design.................................................................................................................. 3
  - 2.1 Network Requirements ................................................................................................. 3
  - 2.2 VLAN Design................................................................................................................. 3
  - 2.3 Network Diagram.......................................................................................................... 4
- 3. Hardware Setup ................................................................................................................. 4
  - 3.1 Devices Used................................................................................................................ 4
  - 3.2 Physical Cabling ........................................................................................................... 4
- 4. Firewall Configuration ........................................................................................................ 5
  - 4.1 Interface Configuration ................................................................................................. 5
    - 4.1.1 VLAN interface overview ......................................................................................... 5
    - 4.1.2 VLAN interface configuration .................................................................................. 5
  - 4.2 DHCP Configuration ..................................................................................................... 6
  - 4.3 NAT Configuration......................................................................................................... 6
    - 4.3.1. NAT and firewall policy configuration...................................................................... 7
  - 4.4 Firewall Rules ............................................................................................................... 7
    - 4.4.1 Firewall policies allowing Admin, Employee, Guest, and LAN internet access........... 8
    - 4.4.2 Firewall rules blocking DMZ access to Employee and Admin networks ..................... 8
    - 4.4.3 Firewall rules restricting Employee access to the Admin network and allowing access to the DMZ web server..................................................................................................... 9
    - 4.4.4 Firewall rule enforcing Guest network isolation while allowing internet access ......... 9
- 5. Switch Configuration........................................................................................................ 10
  - 5.1 VLAN Creation ............................................................................................................ 10
    - 5.1.1 VLANs created on the Cisco SF200-24 managed switch......................................... 10
  - 5.2 Port Assignment and Trunk Configuration .................................................................... 10
    - 5.2.1 Port VLAN membership and trunk configuration on the Cisco switch...................... 11
- 6. Server Configuration......................................................................................................... 11
  - 6.1 DMZ Web Server ......................................................................................................... 11
    - 6.1.1 DMZ web server reachable on port 80.................................................................... 11
- 7. Testing and Validation ...................................................................................................... 12
  - 7.1 Connectivity Tests ...................................................................................................... 12
    - 7.1.1 Test Procedures ................................................................................................... 12
  - 7.2 Evidence..................................................................................................................... 13
    - 7.2.1 Guest VLAN Restrictions....................................................................................... 13
    - 7.2.2 Employee Access to DMZ Web Server.................................................................... 13
    - 7.2.3 Admin VLAN Full Access....................................................................................... 14
- 8. Challenges and Lessons Learned...................................................................................... 14
- 9. Reflection ........................................................................................................................ 15

## 1. Introduction

### 1.1 Objectives

The objective of this lab was to design and implement a secure enterprise-style network using a 
Fortinet firewall and a Cisco managed switch. During the lab, we configured multiple VLANs to 
separate employees, administrators, guests, and DMZ servers into different network segments. 
We also configured firewall rules, NAT, and port forwarding to control communication between 
the networks and provide secure internet and web server access. The lab helped us understand 
how VLANs, firewall policies, and network segmentation are implemented on physical 
networking hardware in a real-world environment.

## 2. Network Design

### 2.1 Network Requirements

- Employees must be able to access the internet
- A public web server must be accessible from the internet
- Administrators must have full access to all systems and networks
- Guests must be able to access the internet, but not internal systems
- The network must be segmented into separate VLANs for security
- Firewall rules must enforce restricted communication between networks
- NAT and port forwarding must be configured for internet and web server access
- A default deny policy must be implemented to block unauthorized traffic

### 2.2 VLAN Design

| VLAN ID | VLAN NAME | Subnet | Gateway |
|---|---|---|---|
| 10 | Employee | 10.0.10.0/24 | 10.0.10.1 |
| 20 | DMZ | 10.0.20.0/24 | 10.0.20.1 |
| 30 | Admin | 10.0.30.0/24 | 10.0.30.1 |
| 40 | Guest | 10.0.40.0/24 | 10.0.40.1 |

### 2.3 Network Diagram

<!-- [Image: Network diagram] -->
<img width="786" height="496" alt="network diagram" src="https://github.com/user-attachments/assets/ccc59103-fbb9-4999-8c30-ce8d017788c5" />


## 3. Hardware Setup

### 3.1 Devices Used

| Device | Purpose |
|---|---|
| Fortinet Firewall | Inter-VLAN routing, firewall security, NAT and internet access |
| Cisco SF200-24 Switch | VLAN segmentation and switch port management |
| Laptops / Virtual Machines | Employee, guest, admin and server systems |

### 3.2 Physical Cabling

The Fortinet firewall was connected to the Cisco switch using a trunk connection to allow traffic 
from multiple VLANs to pass between the firewall and switch. Port FE2 on the switch was 
configured as a trunk port and connected to port2 on the Fortinet firewall.

The remaining switch ports were configured as access ports and assigned to their respective 
VLANs:

- VLAN 10 for employee devices
- VLAN 20 for DMZ servers
- VLAN 30 for administrator devices
- VLAN 40 for guest devices

The WAN interface of the Fortinet firewall was connected to the internet network to provide 
internet connectivity and allow NAT port forwarding to the DMZ web server.

## 4. Firewall Configuration

### 4.1 Interface Configuration

The Fortinet firewall was configured with separate VLAN interfaces for each network segment. 
Each interface was assigned a VLAN ID, IP address, and security zone. The firewall acts as the 
default gateway for all VLANs and performs inter-VLAN routing between the different networks.

| Interface | VLAN ID | IP Address | Security Zone |
|---|---|---|---|
| Port2.10 | 10 | 10.0.10.1/24 | Employee |
| Port2.20 | 20 | 10.0.20.1/24 | DMZ |
| Port2.30 | 30 | 10.0.30.1/24 | Admin |
| Port2.40 | 40 | 10.0.40.1/24 | Guest |

#### 4.1.1 VLAN interface overview

<!-- [Image: VLAN interface overview screenshot] -->
<img width="938" height="143" alt="VLAN interface overview" src="https://github.com/user-attachments/assets/d78ec902-39e1-4960-9fa3-89198c07fbb2" />


#### 4.1.2 VLAN interface configuration

<!-- [Image: VLAN interface configuration screenshots (Employee, DMZ, Admin, Guest)] -->
<img width="399" height="372" alt="VLAN interface configuration" src="https://github.com/user-attachments/assets/93e49cb8-5620-4207-93d5-246c62f1661b" />
<img width="452" height="309" alt="VLAN interface configuration" src="https://github.com/user-attachments/assets/b854153f-5252-4cb6-9893-26d9edec3bcd" />
<img width="359" height="420" alt="VLAN interface configuration" src="https://github.com/user-attachments/assets/b815c2b1-9df2-4a2b-9abf-338fe33344b1" />
<img width="429" height="451" alt="VLAN interface configuration" src="https://github.com/user-attachments/assets/94399fce-7841-4217-96d6-ff3a9d3aebb7" />




### 4.2 DHCP Configuration

DHCP services were configured on the Fortinet firewall to automatically assign IP addresses to 
devices within the Employee, Admin, and Guest VLANs. Each VLAN used its own DHCP range, 
subnet mask, default gateway, and DNS configuration. Static IP addressing was used for 
systems in the DMZ network.

| VLAN | DHCP Range | Gateway | DNS |
|---|---|---|---|
| Employee | 10.0.10.2 - 10.0.10.254 | 10.0.10.1 | System DNS |
| DMZ | Static IPs used | 10.0.20.1 | System DNS |
| Admin | 10.0.30.2 - 10.0.30.254 | 10.0.30.1 | System DNS |
| Guest | 10.0.40.2 - 10.0.40.254 | 10.0.40.1 | System DNS |

<!-- [Image: DHCP lease table screenshot] -->
<img width="873" height="240" alt="DHCP lease" src="https://github.com/user-attachments/assets/860e2129-2f33-44c4-b57e-cc186cb45453" />


### 4.3 NAT Configuration

**Outbound NAT**

Outbound NAT was configured on the Fortinet firewall to allow devices in the Employee, Admin
and Guest VLANs to access the internet through the WAN interface. NAT was enabled on the 
firewall policies connecting the internal VLANs to the WAN interface.

**Destination NAT / Port Forwarding**

Destination NAT (port forwarding) was configured to allow external access to the DMZ web 
server. HTTP and HTTPS traffic arriving on the WAN interface were forwarded to the internal 
DMZ web server using a Virtual IP.

#### 4.3.1. NAT and firewall policy configuration

<!-- [Image: FortiGate policy list screenshot] -->
<img width="679" height="482" alt="FortiGate policy list" src="https://github.com/user-attachments/assets/4193cd6d-d5ea-43ef-8bbb-5f590fd21aa3" />


### 4.4 Firewall Rules

Firewall policies were configured on the Fortinet firewall to control communication between 
VLANs and enforce network security. Allow and deny rules were implemented to restrict 
unauthorized access while permitting required services such as internet access and web server 
connectivity.

| Source | Destination | Service | Action |
|---|---|---|---|
| Employee | WAN | DNS, HTTP, HTTPS | Allow |
| Guest | WAN | DNS, HTTP, HTTPS | Allow |
| Admin | All Networks | ALL | Allow |
| Employee | DMZ Web Server | HTTP, HTTPS | Allow |
| Guest | Internal Networks | ALL | Deny |
| Guest | DMZ | ALL | Deny |
| Guest | Admin | ALL | Deny |
| Employee | Admin | ALL | Deny |
| WAN | DMZ Web Server | HTTP, HTTPS | Allow |

A default deny policy was enforced on the firewall to block all unauthorized traffic not explicitly 
permitted by existing firewall rules.

#### 4.4.1 Firewall policies allowing Admin, Employee, Guest, and LAN internet access

<!-- [Image: Admin-to-Internet, Employee-to-Internet and Guest-to-Internet policy screenshots] -->
<img width="744" height="810" alt="admin-employee-guest" src="https://github.com/user-attachments/assets/2a21acc7-327a-4a43-bf2a-0e24caad5785" />


#### 4.4.2 Firewall rules blocking DMZ access to Employee and Admin networks

<!-- [Image: Block-Guest-to-Internal and Block-Guest-to-DMZ policy screenshots] -->
<img width="862" height="287" alt="dmz-guest" src="https://github.com/user-attachments/assets/4ec79bed-875c-42c2-b53f-4510e9add0cf" />
<img width="438" height="284" alt="dmz-guest" src="https://github.com/user-attachments/assets/3a88c038-c76e-47e2-a959-b5738f17f679" />


#### 4.4.3 Firewall rules restricting Employee access to the Admin network and allowing access to 
the DMZ web server

<!-- [Image: Block-Guest-to-Admin policy screenshot] -->
<img width="420" height="303" alt="Block-Guest-to-Admin" src="https://github.com/user-attachments/assets/de32c610-0403-4002-a616-77fff4623703" />



#### 4.4.4 Firewall rule enforcing Guest network isolation while allowing internet access

<!-- [Image: Block-Employee-to-Admin, Block-DMZ-to-Employee and Internet-to-WebServer policy screenshots] -->
<img width="333" height="466" alt="policies" src="https://github.com/user-attachments/assets/56c5fa1b-fea6-4b91-96db-c0fe61a44331" />


## 5. Switch Configuration

### 5.1 VLAN Creation

VLANs were created on the Cisco SF200-24 managed switch to separate the network into 
different logical segments. Each VLAN was assigned a unique VLAN ID and name to represent a 
specific department or security zone within the network.

| VLAN ID | VLAN NAME |
|---|---|
| 10 | Employee |
| 20 | DMZ |
| 30 | Admin |
| 40 | Guest |

#### 5.1.1 VLANs created on the Cisco SF200-24 managed switch

<!-- [Image: Cisco SF200-24 Create VLAN screenshot] -->
<img width="609" height="332" alt="csico" src="https://github.com/user-attachments/assets/9a2c61a9-a6e3-4873-8ab4-390d34e8563b" />


### 5.2 Port Assignment and Trunk Configuration

Switch ports were configured as either access ports or trunk ports depending on their purpose 
within the network. Access ports were assigned to specific VLANs for end devices, while trunk 
ports were configured to carry multiple VLANs between the switch and the Fortinet firewall.

| Port | Mode | VLAN |
|---|---|---|
| FE1 | Access | VLAN 20 (DMZ) |
| FE4 | Access | VLAN 10 (Employee) |
| FE5 | Access | VLAN 30 (Admin) |
| FE6 | Access | VLAN 40 (Guest) |
| FE2 | Trunk | VLANs 10,20,30,40 |

#### 5.2.1 Port VLAN membership and trunk configuration on the Cisco switch

<!-- [Image: Port VLAN Membership table screenshot] -->
<img width="730" height="364" alt="vlan ports" src="https://github.com/user-attachments/assets/1ea47f8f-dc91-49af-b695-79f4451886ee" />


## 6. Server Configuration

### 6.1 DMZ Web Server

A web server was deployed in the DMZ network using the IP address 10.0.20.10. The server was 
configured to host a basic webpage on HTTP port 80. The webpage was successfully accessed 
from another VLAN, confirming that the DMZ web server was reachable through the firewall 
rules.

The test client used for this verification was connected to the Admin VLAN with IP address 
10.0.30.5 and default gateway 10.0.30.1.

| Item | Configuration |
|---|---|
| Web server IP | 10.0.20.10 |
| Network | DMZ |
| Service | HTTP |
| Port | 80 |
| Test client IP | 10.0.30.5 |

#### 6.1.1 DMZ web server reachable on port 80

<!-- [Image: Browser showing http://10.0.20.10 and PowerShell ipconfig output] -->
<img width="810" height="312" alt="browser" src="https://github.com/user-attachments/assets/cf23c2c5-c008-4b97-9ea9-e922a04b6b95" />


## 7. Testing and Validation

### 7.1 Connectivity Tests

| Test | Expected Result | Actual Result |
|---|---|---|
| Employee -> Internet | Allowed | Success |
| Guest -> Internet | Allowed | Success |
| Guest -> Admin | Blocked | Success |
| Employee -> DMZ Web Server | Allowed | Success |
| WAN -> Web Server | Allowed | Success |
| Admin -> All VLANs and Internet | Allowed | Success |

Connectivity testing was performed between VLANs, the DMZ web server, and the internet to 
verify that the firewall policies and VLAN segmentation were functioning correctly.

#### 7.1.1 Test Procedures

##### 7.1.1.1 Employee can reach internet

<!-- [Image: PowerShell curl.exe https://google.com output] -->

<img width="749" height="137" alt="internet-employee" src="https://github.com/user-attachments/assets/2903b69d-43a6-4ee7-b9f8-8ae39a1e7250" />

##### 7.1.1.2 Guest can reach internet

<!-- [Image: PowerShell curl.exe https://example.com output] -->
<img width="762" height="112" alt="internet-guest" src="https://github.com/user-attachments/assets/220eaf95-4203-4d0c-a772-43d8bd82d8e8" />


##### 7.1.1.3 Guest cannot reach internal networks

<!-- [Image: PowerShell ping output to 10.0.10.4, 10.0.30.4 and 10.0.20.10] -->
<img width="552" height="451" alt="ping" src="https://github.com/user-attachments/assets/c064c4d0-16e9-4aa1-b402-5349918bf99b" />


### 7.2 Evidence

#### 7.2.1 Guest VLAN Restrictions

<!-- [Image: Guest client network settings, Google loaded, and Admin VLAN site unreachable] -->
<img width="715" height="429" alt="guest-client" src="https://github.com/user-attachments/assets/cbb8c1ea-decf-4749-9da4-ca617eaeba71" />


The Guest VLAN client received an IP address in the 10.0.40.0/24 subnet and was able to 
access the internet successfully. Access attempts to internal resources such as the Admin 
VLAN were blocked by firewall rules.

#### 7.2.2 Employee Access to DMZ Web Server

<!-- [Image: Employee client network settings and DMZ web server page] -->
<img width="637" height="380" alt="employee-dmz" src="https://github.com/user-attachments/assets/7cd78eca-f91e-4a19-a56a-2505af559632" />


The Employee VLAN client successfully accessed the DMZ web server hosted at 10.0.20.10 
over HTTP. This confirms that the firewall policy allowing Employee-to-DMZ web traffic was 
functioning correctly.

#### 7.2.3 Admin VLAN Full Access

<!-- [Image: Admin client accessing the switch and FortiGate interfaces, with PowerShell ipconfig output] -->
<img width="672" height="378" alt="admin-vlan" src="https://github.com/user-attachments/assets/85737e28-3ceb-477c-a12d-a10ab7afa82a" />


The Admin VLAN device was able to access all configured VLANs and services. Firewall policies 
granted unrestricted access from the Admin network for management and testing purposes.

## 8. Challenges and Lessons Learned

- The first switch that was tested did not function correctly with the VLAN configuration 
requirements. To resolve this issue, the network was rebuilt using a Cisco SF200-24 
smart switch.
- The Zyxel switch reset process did not always work correctly, which caused delays 
during the initial setup and troubleshooting stages.
- DHCP troubleshooting was difficult at times because it was necessary to verify that 
devices were receiving IP addresses from the correct VLAN DHCP scope.
- Multiple lockouts occurred while configuring the switch management interface due to 
incorrect VLAN or IP management settings.
- Firewall policy order initially caused connectivity problems because some deny and 
allow rules were processed incorrectly.
- NAT and port forwarding configuration required additional troubleshooting before the 
DMZ web server became accessible correctly.
- Through resolving these issues, a better understanding was gained of VLAN 
configuration, DHCP operation, trunk ports, firewall policies, and NAT troubleshooting.
- Despite the challenges encountered during implementation, all major project 
objectives were completed successfully.

## 9. Reflection

This project demonstrated the importance of correct VLAN segmentation, firewall policy 
configuration, and NAT implementation in a small enterprise network environment. The 
practical configuration of physical devices provided valuable hands-on experience with 
troubleshooting network connectivity, DHCP assignment, switch management, and firewall 
security rules.

The project also highlighted how small configuration mistakes, such as incorrect VLAN 
assignments or firewall rule order, can prevent communication between networks. 
Troubleshooting these issues improved understanding of network security concepts and device 
management.

Overall, the project successfully achieved its objectives and provided useful experience 
working with real networking hardware and enterprise-style network design.
