# Intro

## 9:30 - 16:48: Slightly introducing TCP transport layer, OSI model,

## 17:00 - 21:00: Example for routing concept

## 21:10 - 23:30: Data encapsulation explanation

Five Steps of Data Encapsulation: TCP/IP

Layer 5    Application   Data
Layer 4    Transport     Data | TCP                                    Segment
Layer 3    Network       Data | TCP | IP                               Packet
Layer 2    Data Link     Data Link | Data | TCP ( Data Link --> |      Frame ( LH | Data | LT )
Layer 1    Physical      Transmit Bits    <-------------------- |

LH and LT stands for link header and link trailer, respectively.

## 24:00 - 27:00: OSI Model Networking

Layers Explanation of OSI Model

Application: Using the user's features
Presentation: Present data to the application layer, ASCII text binary, JPEGs encryption
Session: Define how to start controlling end to end conversations and includes control management of bidirectional communication, messages that are being sent
Transport: Provides control of the data being sent from point A to point B, so TCP send and resend or UDP just send once.

## 27:30 - 37:00: Q&A 1 from chats

Q1: Can an IP address be spoofed ( fake ) in a TCP request ? -> Absolutely yes -> That Why generation Firewall appear

Q2: BGP is Application layer though, right ? -> Yes

Q3: At layer 2 ( Data Link ), what protections are in place to protect against IP spoofing ? -> There are no protections against IP spoofing at layer 2. ( Layer 2 isn't a VLAN, switch or an Ethernet )

Q4: What is frame rely and ATM ? -> Frame rely: They connect you to a cloud and create a virtual link in multiple locations. It was a very old school networking technique. Although it has a good cloud concepts, it was slow. ATM  - synchronous transfer mode appear.

Q5: Why do you focus on only 4 layers ? -> Because that's all networking is.

Q6: "Hey Mike, Application layer is somewhat confusing to me. Why is BGP considered Application layer. Is it just because it uses a port (TCP 179) to do its work ?" -> BGP itself is an application. ( it performs high-level routing policy decisions and data exchanges between independent networks, rather than just moving raw data ).

Q7: What is difference between frames and packets ? -> Packets from layer 3, Frames from layer 2

Q8: Does a layer 3 switch have the same capabilities as a router ? -> "No, A Layer 3 switch only supports partial routing functions, mainly to enable fast communication between devices across VLANs within a LAN.A router, on the other hand, provides comprehensive and advanced routing features to enable flexible and stable communication across LAN–WAN and LAN–LAN networks." [2]

Q9: What is difference between TCP and UDP frame ? -> There is no such thing as a TCP or UDP frame

Q10: What is difference between TCP and UDP ? -> TCP is a reliable transport ( 1 sent 2, 2 got something from 1, 2 send back something to 1 to make sure that 2 got it ), UDP is just sending data as fast as possible and hoping you can get it ( really fast but may lost some data ).

Q11: How does the sender knows the MAC addresses of the receiver ? -> There is a protocol called ARP ( address resolution protocol ).

Q12: What do you really mean when you say the layers talk to each other? Explain it in a simple language? -> -_(@_@)_/-


## 38:40 - 42:50: Local area network ( LAN )

## 43:00 - 54:00: Freezing problem ( just skip it )

## 54:10 - 1:05:00: Q&A 2 from chats

Q1: So a switch is a device that allows computers to connect to a network ? -> Switch is where you plug all your devices in to network.

Q2: Are there any particular prerequisites we should aware of for these lecture or can someone completely new learn ? -> No ( My opinion is Yes, you should learn these before start this long course: How internet work ?, OSI model, Search for each layers ( Application, Transport, Network, Data Link, Physical ) how do they work )

Q3: How TCP protocol is responsible for errors correction ? -> ( Example for it does )

Q4: Explain about session layer -> -_(@_@)_/-

Q5: In ethernet cable, we are using only 1,2,3,6 so in this which number is for tx and rx particular ? -> Talk about it soon

Q6: Does a router have loop bridge that can be break with STP ? ->Router do not use spanning tree, it doesn't have that problem

Q7: Is there any difference between throughput and data rate and bit rate ? ->   Troughput is the amount of stuff you can  show through there, the bit rate and data rate is the amount of stuff you are selling. ( E.g: You have a 100 GB / second througput, but application may only be streaming 10 GB / second so you have 90 Gb left over -> that's bit rate )

Q8: Can we apply STP on a router / -> No

Q9: As a Cloud networking engineer, do I need to know layer 1 and 2 ? -> yes

Q10: How many times TCP retires and where do we set -> Based on how you code the application

Q11: Where do the lost package go ? Do they just disappear into nothing ? -> They reach the time to live and they just die out

Q12: Why should no significant stuff be on the WLAN ? -> WLAN is unreliable, may lose some small data or send late.

## 1:05:10 - 1:21:30: Type of Cables, and Concepts



# First Lab


## 1:25:30  - 1:38:50: Some necessary CMD

Packet tracer version

Install: https://www.netacad.com/cisco-packet-tracer
Guide: https://www.youtube.com/watch?v=7sWn9MAuJFM

### Testing: 

Select any router -> Click CLI -> wait till it done initialized -> choose no then enter -> Enter Enable mode by `enable`


In Enable mode:

`show run`: list version, type of switching it's using, IP address
`config t`: enter global configuration mode 
`hostname <name>`: sets or changes the device name ( e.g: hostname R1 -> Now Router become R1 )
`int lo0`: creates or enters the configuration mode for a virtual ( stands for interface loopback 0 ) -> set the ip address `ip addr <ip> <subnet-mask>`: assigns a static IP address and subnet mask to a specific interface on a Cisco device
`no shutdown`: removes the administrative disable command
`exit`: exit from previous mode currently
`show ip interface brief`: show all ip address in ( R1 )


- Status is all administratively down -> Why ?
Check by: `show run` -> look at interface, you will see "no ip address, shutdown, duplex auto, speed auto ,..."
Enable by: `config t` -> choose interface you want: `int <interface>` (Giga..0/0 -> g0/0 same with Fast...0/0 ) -> set ip address `ip addr 172.16.1.1 255.255.255.252`

- Disable health check of a switch or router interface: `no keepalive` 
-> Benefits: stop keepalive traffic, forces line protocol up
-> Common use cases: connecting to non-cisco equipment, lab & test environment, GRE tunnels & sub interfaces

Select any switch -> Click CLI -> wait till it done initialized -> -> choose no then enter
Click connections ( lighting symbol ) -> choose copper straight-through cable to connect router and switch 
Complete when checking the interface from router you chosen connect to switch has been up in status


## 1:39:30 - 1:44:50: Q&A 3 from chats

Q1: What is difference between link up/down and line protocol up/down ? -> link up/down is like layer one ( physical layer ) where a cable plugged into it, line protocol is like layer two ( data link layer ) is the delsey up and running, frame relay.

Q2: What do you think about 802.11ax: 802.11ax is wireless ( Wifi 6 ), unreliable that it is used for things are NOT CRITICAL such as playing video games, trying check social media, it should not plug into wireless because of collision nightmare  ( current latest wifi is 802.11be wifi 7 until 2026  )

Q3: Can configuration be imported rather than manually set up ? -> "We usually take a router config, complete it, make it in notepad, paste it in router. But we could set up a TFTP server and pull our startup config and save it to memory"

Q4: When passing these commands how do you know the extract style to type so you do not get errors ? -> Always checking document

## 1:44:00 - 1:45:00: Reason why can't make mistake configuration

## 1:45:15 - 1:48:00: Internet access at work and its concept

## 1:50:30 - 2:03:00: How router and encapsulation works

## 2:03:15 - 2:13:30: Q&A 4 from chats

Q1: Where does PC1 get MAC addresses ? -> `ipconfig /all` then you gonna see your MAC address ( IP address of your device ) ( first part is OUI . second part is unique address your card )

Q2: Since ARP is a layer2 and router is layer3, would routers still communicate via ARP ? -> ARP is a CR hybrid layer 2/3 protocol that map a layer three IP address to a layer 2 MAC address, so router can communicate to other routers, or via IP.

Q3: Where does PC1 get MAC addresses of all the other PC's it want to walk to ? -> If they are on a subnet, ARP will ask who has this MAC address, then somebody responds

Q4: Why does R2 to R3 have an ethernet header/trailer instead of HDLC ? -> R2 and R3 are connected via Ethernet ports, not Serial ports. 
+ Ethernet ports always use Ethernet framing (which uses Source/Destination MAC addresses).
+ Serial ports use serial protocols like HDLC or PPP.
+ Note: HDLC is a bit-oriented Data Link Layer protocol standardized by the ISO (ISO 13239). It provides reliable, error-checked delivery of data frames across point-to-point and multipoint communication links

Q5: Is the time to live the same thing that trace routers use to debug routing issues ? -> No

Q6: What method do you use to configure switched/routers ? -> "We tend to use SSH - most secure protocol"

Q7: Does multicast addressing use broadcasts ? -> Broadcasts aren't forwarded out, but multicast can create something called reverse path trees to the source

Q8: Does ARP works with CDP ? -> Never use acronyms, because CDP can be a lot of things. It could be a coupon code, Cisco Discovery Protocol,...
+ CDP (Cisco Discovery Protocol) is a proprietary Data Link Layer (Layer 2) protocol helps routers identify other routers

Q9: When a packet is then sent from R1 to R2. What is the packets destination ? Would it be a broadcast MAC ? -> It is directly the IP address of next router and the MAC address of the next router. The ARP has to figure out who has sent the MAC address then send it

## 2:14:00 - 2:17:00: What setting terminal look like



# Second Lab


## 2:18:00 - 2:24:00: Some basic CMD

Router:
`reload`: shuts down the operating system and reloads the image from flash memory, clearing RAM in the process ( it need Privileged EXEC mode )
`?`: show all possible CMD in any mode ( enable mode, config ,... )

## 2:27:00 - 2:51:00: Connect two routers and share IP address from two routers

Disconnect Router and Switch

Create a new router -> connect 2 router -> open cli router 2 -> hostname in config mode to R2  -> `int lo0` -> set new ip address `ip addr 2.2.2.2 255.255.255.255` -> then `no shutdown` -> `int f0/0 or g0/0` -> set ip address 172.16.1.2 255.255.255.252 -> `no shut` -> complete you will see loop back ip 2.2.2.2 and f0/0 or g0/0 ip address is 172.16.1.2 and status is up

Troubleshooting challenge: If this is 172.16.1.1 is on the one side of the link and 172.16.1.2 is on the other side, do you guys think it can ping across the link ?

Hint: Same subnet, there's a wire between them
Hint: They can talk to each other

 Test by `ping 172.16.1.1` from router 2 ( if it not work just access to that int has 172.16.1.1 and use `no shut` ) -> `show ip int brief` you will see protocol turn from down into up

==> Finish

`show ip route`: Displays the router's IP Routing Table
`router ospf  <process ID>`: Initializes an OSPF (Open Shortest Path First) dynamic routing process on the router and enters the OSPF router configuration context (config-router#). ( process ID is range from 1 to 65535 )
`traceroute <IP address>`: show the router it passed to get the IP address

*Make sure: Run cmd `router ospf 1` in R2 before R1 finished network configuration 

=> Or else after finished configuration, one of two router may not show IP from of other router
*

Router 1 setup:
`config t`
`router ospf 1`
`network 1.1.1.1 0.0.0.0 area 0`: 
`network 172.16.1.0 0.0.0.3 area 0`
exit to enable mode
`show ip route` -> If you see the IP address from router 2 with 1.1.1.1, it is success

Router 2 setup:
`config t`
`router ospf 1`
`network 2.2.2.2 0.0.0.0 area 0`: 
`network 172.16.1.0 0.0.0.3 area 0`
exit to enable mode
`show ip route` -> If you see the IP address from router 1 with 2.2.2.2, it is success

Test: ping to other router IP address ( R1: `ping 2.2.2.2`, R2: `ping 1.1.1.1` )

What if router 2 add one more ip address, will it also show IP address added in router 2 from R1 ? -> No, because you haven't told OSPF to share it yet ( we have to add network in ospf 1 from router 2 )

## 2:51:10 - 2:53:30: Cisco switch memory types

RAM ( working Mamry of Running Configuration )
Flash ( Cisco IOS Software )
ROM ( Bootstrap Program )
NVRAM ( Start-up Configuration )

## 2:54:00 - 3:33:00: Q&A 5 from chats

Q1: What is difference between loopback and a regular ethernet interface ? -> A regular ethernet interface is basically a physical port, a loopback address is a logical address that you put on the routers, it can be assigned any kind of interface IP address 

Q2: What is area mean ? -> OSPF divides network into sections and they are called areas. Inside of an area, you need to know everything inside that area, but in between areas, you do not need to know as much

Q3: So if you want to have an interface to use to identify a system a loopback is nicer -> A loopback is an address that will never go down, it's a management interface ( e.g: after the cable is disable, the loopback address is still remaining up )

Q4: Do routers have a cache for all ARP requests ? If so - How long do they stay in memory ? ->  Yes, And it depends on can configure how long it stays in memory

Q5: What does the command: `copy system:running-config` do ? ->  copies the router's currently active configuration (stored in RAM) to another location, such as saved memory (NVRAM), flash storage, or a remote network server



References:


[2]: https://www.come-star.com/blog/layer-3-switch-vs-router

