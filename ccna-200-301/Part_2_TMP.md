# Intro


## 5:30 - :19:30 Sample switch forward
"Switches taking your traffic, then flood traffic out every ports, except the one that came in"



# Lab 1


## 21:00 - 43:15:

- Enable mode:
+ `show interface status`: displays a high-level summary table of all physical interfaces, detailing their connection state, VLAN assignment, speed, duplex settings, and hardware type
+ `show vlan`: displays the VLAN database on a Layer 2/Layer 3 switch
+ `show mac address-table dynamic`: display all mac addresses were learned by this switch 
+ You can see all the password set by `show run` in enable mode

- Config mode:
+ `enable password <your password>`: put a password for enable mode ( e.g: enable password 123 -> password to access enable mode is 123)
+ `line vty <start-range> <end-range>`: enters the configuration mode for the switch or router's Virtual Terminal (VTY) lines, which are the virtual ports used to manage the device remotely over a network using protocols like SSH or Telnet ( range is from 0 to 4 concurrent remote sessions )
+ enable secret <password>: Uses a salted cryptographic hash (by default MD5 / Type 5, or SHA-256 / Type 8 in modern IOS) to store the password in the running configuration.
+ `service password-encryption`: encrypts plain-text passwords stored in the configuration file so they cannot be read over someone's shoulder or viewed in a show running-config printout
*Overrides enable password: Cisco IOS has an older command called enable password <password>. If both are configured, enable secret takes precedence because it is secure.*
 `no enable secret`: to remove secret password
+ `interface vlan <VLAN>`: create and enters the configuration mode for the Switch Virtual Interface ( you can see the vlan you have by `show vlan brief` )
+ `no ip domain-lookup`: stop the device to translate human-readable domain names ( this is every time you miss type it will show `Translating...` )

- Config-line mode:
`password <your-password>`: To login to config line
`transport input all`: any incoming connections ( SSH or Telnet )

- Config-if mode:
+ `ip address dhcp`: tell a router or switch interface to request its IP configuration automatically from a DHCP server on the network, rather than using a static (manually assigned) IP address
*When does ip address that DHCP assigned change?
	+ The switch is turned off or disconnected for longer than the DHCP lease time (so the lease expires).
	+ Other devices on the network take that old IP while the switch is offline.
	+ There is no DHCP MAC reservation configured on the DHCP server
*




-> Create a switch -> Enter enable mode 

+ Create a password for enable mode: `enable password <your-password>` -> `exit` then type your password to enter the enable mode

+ Create an encrypted password for enable mode: `enable secret <password>` ( which will override the old password but it still show the raw old password and hashed new password from `show run` )

+ Create two vlan and set the ip address for them ( 192.168.1.1 255.255.255.0 and 192.168.2.1 255.255.255.0 ) and `no shut` for each vlan, then check them existed by `show ip interface brief`



# Questions


## 43:30 - 59:00: Q&A 1 from chats

Q1: Why do we still use Telnet as it is unsecured protocol, Why is it an option ? -> Just some company are still using Telnet, but in reality no one use it, just SSH

Q2: What are the different use-cases for VLAN vs Subnet ? When to use each ? -> Can't put more then 250 to 500 users in a signle subnet, otherwise the network falls apart. 

"""
+ VLAN (Virtual Local Area Network) is a Layer 2 (Data Link) concept. It splits a single physical switch into multiple virtual switches to control broadcast domains at the Ethernet level.
+ Primary use cases:
	++ Limiting Broadcast Storms: Switches naturally flood broadcast frames (like ARP requests) out every port. VLANs restrict broadcast traffic to only the ports belonging to that specific VLAN:

	++ Separating Physical Infrastructure logically: Instead of buying separate switches for HR, Accounting, and Guests, you buy one large switch and isolate departments on specific physical ports.

	++ Carrying Multiple Networks Over One Cable (Trunking): Using 802.1Q VLAN tagging, a single physical link between two switches (or a switch and a router) can carry traffic for dozens of isolated networks.

	++ Hardware/Port Security: You can lock down physical ports so that plugging a rogue device into an "Admin VLAN" port fails unless authorized	

+ Subnet (IP Subnet) is a Layer 3 (Network) concept. It breaks a large IP network range into smaller logical IP address spaces to control IP routing and addressing:

	++ IP Address Allocation & Summarization: Grouping devices by network range makes routing efficient. Instead of routers tracking 500 individual device IPs, they track one summary route (e.g., 10.1.0.0/16).

	++ Enforcing Layer 3 Security (ACLs & Firewalls): Firewalls operate on IP addresses and ports. Subnetting allows you to write rules like "Allow Subnet 10.0.10.0/24 to access Subnet 10.0.20.0/24 only on port 443."

	++ Connecting Geographically Dispersed Sites: Subnets can span across WAN links, VPN tunnels, and cloud providers (like AWS VPCs or Azure VNets) where Layer 2 VLAN tagging cannot travel.

	++ Quality of Service (QoS): Prioritizing latency-sensitive IP traffic (like VoIP audio on 192.168.50.0/24) over bulk data traffic.
"""

Q3: How do you determine the address of the destination if you don't have the mac address ? -> ARP

Q4: Could you use Dynamic Host Protocol in this situation ? -> Do not recommend dynamic for anything

Q5: "Do I need to buy a switch config and all devices to connect his PC and 2 laptops at home ?" -> Depend, if you want to use access everything include pc and laptops, don't need to set up this much, but if your laptops used by someone, instance of your children and you want they can only access something, you can buy those.

Q6: "If I want to connect my neighbor's system, would I just need to configure my router to reach the IP address of her router ?" -> Yes

Q13: Is it possible to all switches route IP addresses during ARP ? -> No, 

Q14: What is max number of devices ( or IP ) that can be assigned to one VLAN ? -> It could be infinity, but it should have some specific number to limit

Q16: Where does ARP live in OSI model ( IP layer 3 but MAC is layer 2 ) ? -> Between layer 2 and 3.



# Lab 2

## 59:30 - 1:03:00: Set the configuration of switch 1

`interface <interface>`: access to that interface configuration
`no negotiation auto`: Stop negotiate to choose which speed is faster 
`duplex full`: Force gateway run at Full Duplex mode ( Allow send and receive data without confict )
`speed 100`: Force gateway runs default at 100 Mbps

*
CMD: `no negotiation auto` you should know:
+ This one is default enable in every switches
+ It only show in CMD `show run` when it is SFP/Fiber cable but RJ45 cable not, this CMD is not available

=> So there is no worry when you can use this CMD
*

## 1:30:15 - 1:59:30: Set the configuration of switch 2 and some status codes

- Create a new switch -> Connect SW1 to SW2 ( do not do opposite )
Then run `show ip int brief` in both side, you will see Vlan1 connected to SW2 has status is administratively down

- Set an interface in switch 2 has duplex full and speed 100 such as switch 1, so that both switch match configuration
 

Line Status		Protocol Status		Interface Status	Typical Root Cause
administratively 	downdown		disabled		The shutdown command is configured on the interface.
down			down			notconnect		No cable; bad cable; wrong cable pinouts; speed mismatch; neighboring device is (a) powered off, (b) shutdown, or (c) error disabled.
up			down			notconnect		Not expected on LAN switch physical interfaces.
down			down (err-disabled)	err-disabled		Port security has disabled the interface.
up			up			connected		The interface is working.



# Lab 3

## 1:19:16 - 1:28:00: Collision domain and broadcast domain 

Collision Domain: A physical network segment where data packets can collide when two or more devices transmit simultaneously over a shared medium. ( HUB )

Broadcast Domain: A logical network area where a broadcast frame sent by any single device is received by all other devices in that same segment. ( Switch + Router )



# Questions

Q1: What is difference between VLAN and subnets ? -> VLAN is a group of ports are the switch or switches, but each VLAN has its own subnet, each interface on a router needs to be on a different subnet

Q5: Do switched always broadcast or do they use tables? What if the route resides in table already do they use that route instead ? -> all switches broadcast the first packet out all ports until they actually learn the port with the MAC addresses and then send it directly to the port where MAC address resides

Q7: What is payload actually ? -> The data in a frame is a payload

Q8: Can we connect VLAN to router or VLAN switch -> yes



# VLANs

## 1:42:30 - 1:53:40: VLANs concept



# Lab 4: 

`vlan <range>`: access to the vlan ( range from 1 to 4094 ) 
`show vlan`:  

`switchport mode dynamic desirable`: configures a Cisco switch port to actively negotiate with the connected device to form a trunk link using Cisco's proprietary Dynamic Trunking Protocol

- A trunk (or trunk link) is a single physical network link between two switches or between a switch and a router—configured to carry traffic for multiple VLANs simultaneously. 

+ Go to configuration mode -> select from range of possible interfaces ( int range <interface/start> - end ) ( e.g: g0/1 - 2 mean you select g0/1 and g0/2 ) -> `switchport mode access`: set port operational mode to Access mode -> `switchport access vlan <range>`: explicitly assigns that you selected to vlan you want ( range is from 1 to 4094 )

`switchport mode dynamic desirable` in a interface configuration -> You can see Switchport: Enabled and Administrative Mode: dynamic desirable in `show interface <interface> switchport`



# Questions

Q1: Can we have same VLAN in 2 switched ? -> absolutely yes

Q2: Clarify about layer 2 and layer 3 security ? -> At layer two, we can add QoS that we can send critical data over non critical, layer 3 we have firewall, instruction detection system, layer three access lists, …

Q3: Is 802.1Q TRUNKING the same as VLAN TAGGING ? -> Trunk enables you to put multiple VLANs on that single wire, while VLAN TAGGING is what enables you to put multiple VLANs on a single wire and keep them logically separated. ( Trunk: the ability to connect to switched, tagging is what enables to keep your traffic separated on them )

Q6: What VTP ( VLAN TRUNKING protocol ) has to do with VLANs ? -> It enables you to connect to switched and determine which VLANs are going to pop on that wire in between them

Q8: How to get hosts on different VLANs to communicate using L2 switches ? -> VLAN is separated, the only way you can communicate to another VLAN is with a router.

Q9: In normal env, which is a better choice between routers or multiple protocol switches ? -> It depends on how many devices connected 

Q11: Is there any alternative to trunking in real environment or in cloud ? -> No

Q12: Can DNS and DHCP be run on a domain controller ? ->  Yes



# STP and RSTP ( Spanning tree protocol & Rapid spanning tree protocol )

Spanning Tree Protocol (STP) and Rapid Spanning Tree Protocol (RSTP) are Layer 2 network protocols that prevent loops in networks connected with redundant Ethernet links.

The problem they solved: Switching loops

- In network design, engineers connect switches with redundant cables so that if one link breaks, another takes over. However, Ethernet frames do not have a Time to Live (TTL) field like IP packets do. Without STP, redundant physical connections cause:

+ Broadcast Storms: An ARP or broadcast frame circulates continuously around physical loops, multiplying infinitely until switch CPUs hit 100% and network interfaces collapse

+ MAC Address Table Instability: Switches continually see the same host MAC address coming from different physical ports, causing "MAC flapping" and packet loss

=> STP solves this by monitoring physical topology and logically blocking redundant ports—creating a loop-free tree structure while keeping backup paths warm


How do they work

+ Elect the Root Bridge: The switch with the lowest Bridge ID (Bridge Priority + MAC Address) becomes the central boss ("Root Bridge").

+ Determine Root Ports (RP): Every non-root switch finds its single port that has the lowest root path cost (based on link bandwidth) back to the Root Bridge.

+ Determine Designated Ports (DP): For each network segment between switches, the port that offers the lowest path cost back to the Root Bridge becomes the forwarding port for that link.

Block Remaining Ports (Alternate/Backup): Any port that is neither a Root Port nor a Designated Port is put into a Blocking state. Traffic cannot pass through it, breaking the physical loop



# Questions

Q1: Why do you need STP if we have TTL ? -> TTL is layer 3 and STP is layer 2
