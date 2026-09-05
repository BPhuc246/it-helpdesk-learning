## 6:30 - 12:30: Routing concept


## 30:10 - 43:30: Some CMD Configurations of router


## 44:30 - 1:05:30:  Questions 1 from chat

Q2: Which use cases choosing switching with spanning trees vs routing ? -> Spanning tree can make many problems and hard to troubleshooting, so that's why not many people use it. Routing is faster and easier to avoid a routing loop with good configuration and switch loops.

Q3: What is the advantage of using default router ? -> If you do not have a default router, you have to add router manually to everything

Q4: Do you configure your routine table or its done automatically ? -> if we run a routing protocol such as OSPF, it's automatic, or else we have to configure static routes.

Q8: As we configure the IP static route, is the address of the next hop the default router of the LAN ? -> It's not, the next hop is the next router of the link


## 1:05:50 - 1:46:00: Routing between Switching


## 1:46:30 - 2:17:00:Lab

+ Create 3 routers, one switch and connect all of them by a cable

R1

int lo0
ip address 1.1.1.1 255.255.255.255
no shut
int g0/0
ip address 192.168.1.1 255.255.255.252
no shut


R2

int lo0
ip address 2.2.2.2 255.255.255.255
no shut
int g0/0
ip address 192.168.1.2 255.255.255.252
no shut


R3

int lo0
ip address 3.3.3.3 255.255.255.255
no shut
int g0/0
ip address 192.168.1.5 255.255.255.252
no shut

Mission: How do you make all routers can ping to each other IP addresses.


Method 1: IP route

R1 and R2 can connect, but R3 can's connect to R2 -> R3 is on the other subnet ( sub1: 1 -> 2, sub2: 5 -> 6 )


R2 can connect to R3 by:
R2:
int g0/1
ip address 192.168.1.6 255.255.255.252

=> Now R2 can ping to 192.168.1.5, and R3 can ping to 192.168.1.2 

But R3 and R1 can't connect, 

syntax ip route: ip route <DESTINATION-NETWORK> <SUBNET-MASK> <NEXT-HOP-IP | EXIT-INTERFACE>

R1: Add route to reach R3's subnet through R2
ip route 192.168.1.4 255.255.255.252 192.168.1.2

R3: Add route to reach R1's subnet through R2:
ip route 192.168.1.0 255.255.255.252 192.168.1.6

Now R1 and R3 can ping to each other

Now set the ip address 1.1.1.1, 2.2.2.2, and 3.3.3.3

=> 
R1:
ip route 2.2.2.2 255.255.255.255 192.168.1.2
ip route 3.3.3.3 255.255.255.255 192.168.1.2

R2:
ip route 1.1.1.1 255.255.255.255 192.168.1.1
ip route 3.3.3.3 255.255.255.255 192.168.1.5

R3: 
ip route 1.1.1.1 255.255.255.255 192.168.1.6
ip route 2.2.2.2 255.255.255.255 192.168.1.6

Make sure all these cmd Success Rate: 5/5

R1: 
ping 2.2.2.2, ping 3.3.3.3
ping 192.168.1.2, ping 192.168.1.5, ping 192.168.1.6

R2:
ping 1.1.1.1, ping 3.3.3.3
ping 192.168.1.1, ping 192.168.1.5

R3:
ping 1.1.1.1, ping 2.2.2.2
ping 192.168.1.1, ping 192.168.1.2, ping 192.168.1.6


Method 2: EIGRP 

syntax network: network <IP_ADDRESS> [WILDCARD_MASK]
<IP_ADDRESS>: The base IP address or network prefix you are matching.
[WILDCARD_MASK]: An inverted subnet mask used to control how precisely the router checks interface IPs.

A wildcard mask uses binary to tell the router which parts of the IP address matter:
+ 0 = Exact match required. (The number in this position must match).
+ 255 = Ignore / Anything goes. (Wildcard position).

E.g 1: network 192.168.1.0 0.0.0.3 -> Meaning: "Look for any interface on this router whose IP starts with 192.168.1. and ends with 0, 1, 2, or 3 (the /30 range). Turn on EIGRP on those interfaces."

E.g 2: network 1.1.1.1 0.0.0.0 -> Meaning: "Look for an interface on this router with the exact IP address 1.1.1.1. Turn on EIGRP on that specific interface and advertise 1.1.1.1/32 to neighbors."

E.g 3: network 192.168.1.0 0.0.0.255 -> Meaning: "Look for any interface starting with 192.168.1. (e.g., 192.168.1.1, 192.168.1.50, 192.168.1.254). Activate EIGRP on all of them."

R1, R2, R3: router eigrp <NUMBER_RANGE_1_56634> + no auto-summary

R1:
network 192.168.1.0 0.0.0.3
network 1.1.1.1 0.0.0.0

R2:
network 192.168.1.0 0.0.0.3
network 192.168.1.4 0.0.0.3
network 2.2.2.2 0.0.0.0

R3:
network 192.168.1.4 0.0.0.3
network 3.3.3.3 0.0.0.0
