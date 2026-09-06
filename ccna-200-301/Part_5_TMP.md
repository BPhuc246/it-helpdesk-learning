## 5:00 - 42:00 : How to troubleshooting


## 43:00 - 52:00: Questions 1 from chat  


## 1:02:30 - 1:10:00: OSPF debug


## 1:11:00 - 1:13:30: Questions 2 from chat

Q3: How does the router choose between an OSPF route and Other routes ( example EIGRP ) -> Depend on their trust rank ( example: IP route will be more trust than EIGRP )


## 1:15:00 - 1:52:00: OSPF Area Border Router and its design


## 1:57:00 - 2:50:00: Lab 

+ Create 3 routers connected:

R1 -------- R2 
 \          /
  \        /
   \      /
    \    /
      R3


Configuration:
R1:
int lo0
ip address 1.1.1.1 255.255.255.255 
no shut
int g0/0
ip address 192.16.0.1 255.255.255.252
no shut
int g0/1
ip address 172.16.0.5 255.255.255.252
no shut
router ospf 1
network 1.1.1.1 0.0.0.0 area 0             ! Enable OSPF on Loopback0
network 192.16.0.0 0.0.0.3 area 0          ! Enable OSPF on g0/0 (talks to R2)
network 172.16.0.4 0.0.0.3 area 0          ! Enable OSPF on g0/1 (talks to R3)
line vty 0 4
no login
password cisco
transport input all

R2:
int lo0
ip address 2.2.2.2 255.255.255.255 
no shut
int g0/0
ip address 192.16.0.2 255.255.255.252
no shut
int g0/1
ip address 172.16.0.9 255.255.255.252
no shut
router ospf 1
network 2.2.2.2 0.0.0.0 area 0             ! Enable OSPF on Loopback0
network 192.16.0.0 0.0.0.3 area 0          ! Enable OSPF on g0/0 (talks to R1)
network 172.16.0.8 0.0.0.3 area 0          ! Enable OSPF on g0/1 (talks to R3)
line vty 0 4
no login
password cisco
transport input all

R3:
int lo0
ip address 3.3.3.3 255.255.255.255 
no shut
int g0/0
ip address 172.16.0.6 255.255.255.252
no shut
int g0/1
ip address 172.16.0.10 255.255.255.252
no shut
router ospf 1
network 3.3.3.3 0.0.0.0 area 0             ! Enable OSPF on Loopback0
network 172.16.0.4 0.0.0.3 area 0          ! Enable OSPF on g0/0 (talks to R1)
network 172.16.0.8 0.0.0.3 area 0          ! Enable OSPF on g0/1 (talks to R2)
line vty 0 4
no login
password cisco
transport input all

Explanation:

What is different between IP route, EIGRP, and OSPF ?

Feature / Protocol	Static Routing (ip route)	EIGRP (router eigrp)	OSPF (router ospf)
Protocol Type	Manual / Static	Distance-Vector (Advanced/Hybrid)	Link-State
How Routes Are Discovered	Hardcoded manually by the admin.	Neighbor routers pass distance & vector updates ("routing by rumor").	Routers flood LSAs to build an identical map of the entire topology.
Best Path Calculation	Fixed static next-hop.	DUAL Algorithm (Bandwidth & Delay).	Dijkstra SPF Algorithm (Interface Cost / Bandwidth).
Administrative Distance (AD)	1 (Highest Priority)	90 (Preferred over OSPF)	110
Failover / Redundancy	Manual update required if a link breaks.	Fast; uses precomputed Feasible Successors.	Fast; recalculates SPF tree when link states change.
Vendor Compatibility	Universal across all brands.	Cisco proprietary (though basic specs were published).	Open Standard (works on Cisco, Juniper, Mikrotik, etc.).


- Here are the 3 distinct "highways" (subnets) connecting your 3 routers:

+ Subnet 192.16.0.0/30 (Link between R1 and R2):
	Network ID: 192.16.0.0
	Usable IPs: 192.16.0.1 (assigned to R1) and 192.16.0.2 (assigned to R2)

+ Subnet 172.16.0.4/30 (Link between R1 and R3):
	Network ID: 172.16.0.4
	Usable IPs: 172.16.0.5 (assigned to R1) and 172.16.0.6 (assigned to R3)

+ Subnet 172.16.0.8/30 (Link between R2 and R3):
	Network ID: 172.16.0.8
	Usable IPs: 172.16.0.9 (assigned to R2) and 172.16.0.10 (assigned to R3)


R2: 
int g0/2
ip address 192.168.1.1 255.255.255.252
no shut
router ospf 1
network 192.168.1.0 0.0.0.3 area 1

R4:
line vty 0 4
no login
password cisco
transport input all
exit
enable password cisco
int lo0
ip address 4.4.4.4 255.255.255.255
no shut
int g0/0
ip address 192.168.1.2 255.255.255.252
no shut
router ospf 1
network 4.4.4.4 0.0.0.0 area 1
network 192.168.1.0 0.0.0.3 area 1



R1:
int g0/2
ip address 172.16.0.13 255.255.255.252
router ospf 1
network 172.16.0.12 0.0.0.3 area 3

R5:
int lo0
ip address 5.5.5.5 255.255.255.255
no shut
int g0/0
ip address 172.16.0.14 255.255.255.252
no shut
router ospf 1
network 172.16.0.12 0.0.0.3 area 3
network 5.5.5.5 0.0.0.0 area 3
line vty 0 4
no login
password cisco
transport input all


*
Tip: If you want a specific ip is a neighbor IP from other router, you can do this:
`router ospf < number >`
`router-id < IP_ADDRESS >`
`clear ip ospf process`

Remember Which IP created first will be the router-id
*

What does `area <number> stub no-summary` do ? -> It will convert OSPF Area <number> into a Totally Stubby Area.
It is configured on an Area Border Router (ABR)—in your topology, R1—to block unnecessary routing updates and save memory/CPU on downstream routers inside Area 3 (like R5).

What if you do for both R5 and R1:
`router ospf <number>`
`area 3 stub summary`

=> R1 stops forwarding individual Type 3 Summary LSAs (like 2.2.2.2, 3.3.3.3, 4.4.4.4) into Area 3.
=> To prevent R5 from losing access to the rest of the network, R1 automatically injects a default route (0.0.0.0/0) via its interface g0/2 (172.16.0.13).

This "0.0.0.0" acts as a "catch-all" gateway:
+ When R5 wants to send traffic to 4.4.4.4 (or any IP outside Area 3), it looks at its routing table.
+ Since R5 no longer has a specific route for 4.4.4.4, the packet matches the Default Route (0.0.0.0/0).
+ R5 forwards the packet directly out of g0/0 to R1 (172.16.0.13), trusting R1 (the ABR) to route it to the correct destination.

`show ip ospf database`
Example + explanation: 

                Router Link States (Area 3)

Link ID         ADV Router      Age         Seq#       Checksum Link count
172.16.0.14     172.16.0.14     2833        0x80000013 0x00b43e 2
5.5.5.5         5.5.5.5         469         0x80000017 0x002834 2
1.1.1.1         1.1.1.1         469         0x80000012 0x00297d 1

- Meaning: Type 1 LSAs generated by every router inside Area 3 describing their directly connected links:
+ 1.1.1.1 = R1's LSA describing its g0/2 connection into Area 3.
+ 5.5.5.5 = R5's new LSA using its updated Router ID.
+ 172.16.0.14 = An old, stale LSA from before R5's Router ID was cleared (it will age out automatically when Age reaches 3600 seconds).

                Net Link States (Area 3)
Link ID         ADV Router      Age         Seq#       Checksum
172.16.0.14     5.5.5.5         308         0x80000008 0x00a5b8

- Meaning: Represents the multi-access broadcast network segment on the 172.16.0.12/30 link.
- ADV Router 5.5.5.5 = R5 was elected as the Designated Router (DR) on this broadcast link and advertises the connected routers.

                Summary Net Link States (Area 3)
Link ID         ADV Router      Age         Seq#       Checksum
0.0.0.0         1.1.1.1         464         0x80000029 0x00250d

- Meaning: This is the most important section. Because of no-summary, all individual inter-area LSAs have been filtered out.
- Link ID 0.0.0.0: ABR R1 (1.1.1.1) advertises a single Type 3 Summary LSA for 0.0.0.0, representing the default route injected into Area 3.


*To enable area 3 again just paste this in two routers (config-router): `no area <number> no-summary`*


R4:
int lo10
ip address 64.64.64.64 255.255.255.255
no shut
router ospf 1
redistribute connected metric 5 metric-type 1 subnets

syntax: redistribute connected metric <number> metric-type <1|2> subnets
+ redistribute connected: Tells the OSPF process to grab all networks from directly connected interfaces that lack an active network statement under router ospf.
+ subnets (Crucial for Cisco IOS): Without the keyword subnets, OSPF will only redistribute classful networks (e.g., /8, /16, /24). Adding subnets forces OSPF to redistribute variable-length subnetted masks (VLSM), such as your /30 point-to-point links or /28 LANs.
+ metric <cost>: Assigns a starting seed metric (cost) to the imported routes.
	> If you do not specify a metric, OSPF defaults to a seed metric of 20 for most 	redistributed protocols. Example: metric 50 assigns a starting cost of 50 to these 	external routes.
+ metric-type <1 | 2>: Determines how the cost calculates as traffic travels through the rest of your network:
	-> Metric-Type 2 (E2 - Default): The route cost remains fixed at the seed metric 	everywhere in the network, ignoring interior hop costs. Example: If seed metric is 	20, every router in the network sees the cost to this route as 20, no matter how 	many routers away it is.
	-> Metric-Type 1 (E1): The total cost accumulates (Internal Path Cost + Seed 	Metric).Example: If seed metric is 20 and the path to the ASBR costs 10, the router 	calculates the total cost as 30 ($20 + 10$).

- After this cmd: check `show ip ospf database` in R1 or any router -> You must see this at the end:

                Summary ASB Link States (Area 3)
Link ID         ADV Router      Age         Seq#       Checksum
4.4.4.4         1.1.1.1         87          0x8000000a 0x009ca3

+ Type 4 LSA (ASBR Summary): Generated by ABRs (in this case, R1 advertising into Area 3, or R2 into Area 0) to tell other routers where an ASBR is located.
+ Link ID 4.4.4.4: Identifies the Router ID of the ASBR (Router 4).
+ ADV Router 1.1.1.1: Indicates that R1 (as the ABR for Area 3) is forwarding this announcement into Area 3 so R5 knows how to reach ASBR R4.

                Type-5 AS External Link States
Link ID         ADV Router      Age         Seq#       Checksum Tag
64.64.64.64     4.4.4.4         92          0x80000001 0x00ab93 0

+ Type 5 LSA (Autonomous System External): Generated directly by the ASBR (R4) to advertise routes imported into OSPF from outside sources.
+ Link ID 64.64.64.64: The actual external network prefix being advertised (Loopback 10 on R4).
+ ADV Router 4.4.4.4: Shows that R4 is the original advertising router that redistributed this network.
+ Tag 0: An optional 32-bit field used for route filtering or administrative tracking (default is 0).

Create a switch

R5:
int g0/1
ip address 192.168.10.1 255.255.255.0
no shut
router ospf 1
network 192.168.10.0 0.0.0.255 area 3


## 3:03:45 - 3:10:00: Concept of a network type with regards to OSPF

Bonus Summary:

* Model OSPF: *

                    OSPF DOMAIN
                         |
             +-----------+-----------+
             |                       |
          Area 0                  Area 1
        (Backbone)              (non-backbone)
             |                       |
             |                       |
            R1                      R4
             |
             |
          Area 3
       (non-backbone)
             |
            R5

OSPF = routing protocol
Area = area in OSPF.
Area 0 = backbone.
ABR = router stands between Areas.
ASBR = router take route from outside OSPF into inside OSPF.
LSA = information that OSPF use to describe topology/routes.
LSDB = database contain LSAs.
SPF = algorithm using LSDB to find the best path.
Routing table = final result used by router to forward packet.

Important chain: 
Interface -> OSPF Neighbor -> LSA -> LSDB -> SPF -> Best Path -> Routing Table -> Forward packet


* What does OSPF actually do ? *

Example:

( 1.1.1.1 )
R1 -------- R2 ( 2.2.2.2 )
 \          /
  \        /
   \      /
      R3 ( 3.3.3.3 )

When R1 connected to R2 and R3, R2 connected to R1 and R3, R3 connected to R1 and R2
After that these routers can calculate the destination to other routers.


* What is LSA ? *

LSA = Link-State Advertisement ( this is a piece of information that OSPF use to give notification about topology or route.  )
Example: R1 has a link to R2 and R3 -> this information will be packaged into LSA

-> LSA is not a routing table, chain: LSA -> LSDB -> SPF -> Routing Table


* What is LSDB ? *

LSDB = Link-State Database ( it is a database containing LSAs that router knows  )
Example:
                 LSDB
                  |
       +----------+----------+
       |          |          |
      R1         R2         R3
      LSA        LSA        LSA
       |          |          |
       +----------+----------+
                  |
                 SPF
                  |
             Routing Table

`show ip ospf database`: show LSDB
`show ip route`: show routing table

Types of LSA:
+ Router LSA ( I am a router and there are my links )
+ Network LSA ( this is multi-access network and DR describe it )
+ Summary LSA ( This area has network of other area )
+ ASBR Summary LSA ( Where is the path to reach ASBR ? )
+ AS External LSA ( There is a router outside OSPF )

Type 1 → "I am router."
Type 2 → "This is network segment."
Type 3 → "Other area has this network."
Type 4 → "Go this way to reach ASBR"
Type 5 → "There is a router outside the OSPF."


- Type 1: Router LSA 
Each routers OSPF create type 1 LSA for each areas that they ( routers ) participant in

R1 will create information like: ( link to R2 + link to R3 + loopback 1.1.1.1 ), R2 create type 1 of R2, R3 create type 1 of R3


- Type 2: Network LSA
Type usually relate to DR

              Switch
          /     |     \
        R1      R2     R3

This is multi-access network. Instead of OSPF let every routers create adjacency fully, the number of adjacency can increse sharply. OSPF solve this by: 

DR
 |
 +---- R1
 +---- R2
 +---- R3

=> DR create type 2 network LSA to describe that network segment.


- Type 3: Summary LSA

Example:

           Area 0
        R1 ------- R2
         |          |
         |          |
       Area 3     Area 1
         |          |
        R5         R4 ( 4.4.4.4/32 )

=> R5 is not in Area 1, how does R5 know 4.4.4.4 exist ? -> R1 and R2 act as ABRs and propagate information between areas via Type 3 LSAs.


* What is ABR ( Area Border Router ) ? *

- ABR is a router connecting OSPF areas together
Example: Area 0 - R1 - Area 3 ( R1 has interface in area 0, area 3 so R1 is ABR )
Example: Area 1 - R4 ( R4 is a internal router )


* What is AS ( Autonomous System ) ? *

- A network system under a single organization or administration.

Example:

Company A            Company B               ISP
    |                    |                    |
 AS65001              AS65003              AS65002

 OSPF usually use inside an autonomous system, so OSPF is a IGP - Interior Gateway Protocol


- Type 4: What is ASBR ? ( Autonomous System Boundary Router )

Boundary between OSPF and routing information outside OSPF
Example: 
          OSPF
           
R1 ----- R2 ----- R3
                  |
                  |
              External network

If R3 take route from static, EIGRP, BGP, connected and then give that route into OSPF: 
External route - R3 - OSPF  => R3 is ASBR


* How to let a router become ASBR and what is Redistribution ? *

`Redistribution connected metric <number> metric-type <1|2> subnets` -> Include my connected routes in OSPF. -> It becomes ASBR.

Redistribution: mean take route from this routing source to another routing protocol.

- Type 5: External LSA

When R4 redistribute: OSPF need to say: "This network come from outside OSPF". 
That information broadcasted by type 5 LSA

Example LSA:

                      OSPF
                       |
        +--------------+--------------+
        |                             |
      Area 0                        Area 1
        |                             |
    R1 ---- R2                       R4
     \      /                         |
      \    /                        ASBR
       \  /                           |
        R3                       64.64.64.64
         |
       Area 3
         |
        R5

* What is stubby area ? *

It block external Type 5 LSAs instead it inject 0.0.0.0/0 ( default route )

Why they need to stub from both two side routers ? -> Because OSPF neighbors agree on Area attributes.