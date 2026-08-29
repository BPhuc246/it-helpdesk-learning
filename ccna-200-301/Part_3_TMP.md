## 6:00 - 15:00: IP address and some examples how to not wasting IP address

## 15:10 - 34:30: Subnet concepts

IP address: Network ( 16 bit ) + Subnet ( 8 bit ) + Host ( 8 bit )
           < ---------------------- 32 bit ----------------------> 


Private IP Networks 			Class of Networks 	Number of Networks
10.0.0.0				A			1
172.16.0.0 through 172.31.0.0		B			16
192.168.0.0 through 192.168.255.0	C			256


Format of Unsubnetted Class A, B, and C Networks

A N = 8 - H =24 , B N = 16 - H = 16 , C N =24 - H = 8
N: Networks, H: Hosts bit

Class A: 224 - 2 = 16 777 214
Class B: 216 - 2 = 65 534
Class C: 28 - 2 = 254

Borrowing Enough Subnet and Host Bits:
Need X Subnets: 2^S >= X
Need Y Hosts/Subnet: 2^H - 2 >= Y

Example 1: N = 16, S = 8, H = 8. Subnets need 200 and Hosts/Subnet need 200

2^S >= 200 <=> 256 > 200 and 2^H - 2 >= 200 <=> 254 > 200 => /24

## 35:00 - 40:30: Questions 1 from chats

Q1: When to use class B vs class C address ? -> In reality, people do not really care about what kind of class is that IP address, usually /24 for LAN links and /30 for WAN links.

Q7: How is subnetting same or different for IPv6 ? -> Almost the same one is 32 bit binary and one is 128 bit hexadecimal address

## 43:40 - 1:16:00: Find the next subnet math

## 1:16:30 - 1:29:00: Questions 2 from chats

Q3: When referring to CIDR we only mean Private Address Space, is assumption correct ? ->  We do not use class B, class C, it has nothing to do with if it's public or private. It means we're not adhering to the /8, /16, /24 

Q7: When is /8 ever useful ? They are such big domains ? ->  There'd be no reason to use anything like that, but if you like to give yourself a lot of room to grow.

Q11: What's difference between the cloud and a datacenter ? I thought that Datacenters hosted the cloud ? -> the cloud is just somebody else's datacenter                                                

Q12: Would you ever create a wan connection on the cloud ? -> ( example: If I have multiple servers and how can I do access all of them without wire it to the cloud ), Also need a backup connection for cloud.


## 1:30:00 - 1:42:00: Summary address

Example: 192.168.0.0/23 can cover these IP addresses ( 192.168.0.0/24, 192.168.1.0/24, ... )

Why we need address plan ?  

Example 1: 

Datacenter		Cloud
10.0.0.0/24		10.0.1.0
10.0.2.0/24
10.0.3.0/24

+ Avoid overlappong & conflict IP ranges -> 10.0.1.0 is between 10.0.0.0 and 10.0.2.0. If someone randomly assigned 10.0.0.0/24 to both the cloud and the datacenter, hybrid traffic over Site-to-Site VPN or DirectConnect/ExpressRoute would fail completely due to routing collisions

+ Route Aggregation & Summarization: Because all these subnets fall inside the contiguous supernet, upstream edge routers or security firewalls can advertise a single route (10.0.0.0/22) instead of four separate /24 routes. This keeps routing tables small and reduces CPU overhead


Example 2:

Datacenter			Cloud
	- 10.0.0.0/22 -->
		<-- 172.16.0.0/22 -
10.0.0.0/24			172.16.0.0/24
10.0.1.0/24			172.16.1.0/24
10.0.2.0/24			172.16.2.0/24
10.0.3.0/24			172.16.3.0/24


## 1:42:45 - 1:45:30: Questions 3 from chat

Q1: Difference between Access switch vs Distribution switch vs core switch ? -> Core is the fastest router,  distribution switch is it bring stuff into our core, then we plug in switches called access switches where user plugin

Q2: So we use supernetting for our wan connections to the cloud ? -> There's no choice, because cloud only take 100 routers. 

## 2:01:35 - 2:33:00: How to configure router

+ Select two routers then connect them by staright through cable
+ Set ip address 192.168.0.2 255.255.255.252 to int g0/0 for Router 2:
`en` -> `config t` -> `int g0/0` -> `ip address 192.168.0.2 255.255.255.252` -> `no shut` -> exit ( check by `show ip int brief` )

Possible problems:
+ After set or create a new ip address in a router, only that router can ping but other can't ( explain, take an example that I want to go to USA but i do not have an airplane or a boat, so how do i get there ? -> There's no route )

Solution: 

Method 1:

config router 2
`ip route 1.0.0.0 255.0.0.0 192.168.0.1` -> 
           
1.0.0.0: target destination network you want to reach
255.0.0.0: subnet mask for target network ( convert every single IP address starting with .1 from 1.0.0.0 to 1.255.255.255 )
192.168.0.1: The IP address of the neighboring router interface ( the interface on R1 connected to R2 )
  
config router 1
`ip route 2.0.0.0 255.0.0.0 192.168.0.2`

Or remove by: `no ip route ... ... ...`

--> Then now 2 routers connected

+ Set network for 2 router
`router ospf 1` -> `network 0.0.0.0 255.255.255.255 area 0`
-> Show neighbor relationship by: `show ip ospf neighbor`

Now both of them can ping to each other

Method 2:

Config router 1:

`router eigrp <number range from 1 to 65 535>`: used to start and enter the configuration mode for the EIGRP (Enhanced Interior Gateway Routing Protocol) process which allows you to specify network segments to advertise.
 
`router eigrp 1` -> `network 2.2.2.2` -> `network 192.168.0.0` -> `no auto-summary`

config router 2:

`router eigrp 1` -> `network 1.1.1.1` -> `network 192.168.0.0` -> `no auto-summary`

## 2:33:30 - 2:43:00: Learning debug 

`debug ip packet`: a cmd show logs
