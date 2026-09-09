## 5:30 - 20:30: TCP/IP, UDP , and flow control


## 21:00 - 31:00: Questions 1 from chat


## 32:00 - 50:00: Access control lists

What is access control lists ? -> keep the data from going to places that don't want to


## 53:00 - 1:16:30: Lab


R1:
enable password cisco
line vty 0 4
no login
password cisco 
transport input all
int lo0
ip address 1.1.1.1 255.255.255.255
no shut
int g0/0
ip address 192.168.1.1 255.255.255.252
no shut
router ospf 1
network 1.1.1.1 0.0.0.0 area 0
network 192.168.1.0 0.0.0.3 area 0

R2:
enable password cisco
service password-encryption
int lo0
ip address 2.2.2.2 255.255.255.255
no shut
int g0/0
ip address 192.168.1.2 255.255.255.252
no shut
enable password cisco
line vty 0 4
no login
password cisco 
transport input all
router ospf 1
network 2.2.2.2 0.0.0.0 area 0
network 192.168.1.0 0.0.0.3 area 0


Config access list:
R1:
access-list 1 permit 1.1.1.1
access-list 1 permit 192.168.1.1
access-list 1 permit 2.2.2.2
Check by: `show ip access-lists <number>`: 


## 1:16:45 - 1:26:30: Questions 2 from chat

Q1: Is wild Card Mask is also configured to loopback address ? -> wild mask could be allow multiple addresses or all addresses

Q4: Can ACL's ( Access Control List ) be stateful ? -> Routers aren't really a firewall. ACL as a rule is not stateful

Q5: Does it run on VM ? -> It literally all routers, include in a whole cloud when you installed


## 1:29:50 - 1:33:00: Continue lab


R1:
no ip access-list standard 1 in
access-list 1 remark allows Phuc ospf to reach nonie
access-list 1 permit 1.1.1.1
access-list 1 permit 2.2.2.2
access-list 1 permit 192.168.1.1
access-list 1 permit 192.168.1.2
access-list 1 permit 224.0.0.5
access-list 1 permit 224.0.0.6
int g0/0
ip access-group 1 in


## 1:36:25 -  2:08:00: Extend access list

R1:

syntax: access-list <number> permit <...> <source> <destination>

access-list 101 permit ospf any any
access-list 101 permit icmp 1.1.1.1 0.0.0.0 any
access-list 101 permit icmp 192.168.1.1 0.0.0.0 any
access-list 101 permit icmp 192.168.1.2 0.0.0.0 any
access-list 101 permit ip any any
ip access-group 101 in

=> `show access-lists`  View the access list, can ping to 2.2.2.2

-----------------------------

syntax: `access-list <number> remark <comment-text>`: remark do not block, permit, or alter traffic flow

access-list 101 remark allowretreat
access-list 101 permit ospf any any
access-list 101 deny tcp 172.16.3.11 0.0.0.255 eq www 2.2.2.2 0.0.0.0
show access-list
no ip access-list extended 101


-----------------------------

Create access control list:

ip access-list extended iamthehuman
permit ospf any any
permit ip 1.1.1.1 0.0.0.0 2.2.2.2 0.0.0.0 ( this will permit anything from 1.1.1.1 talk to 2.2.2.2 )
deny ip 172.16.1.0 0.0.0.255 any ( block anything when 172.16.1.0 talk to any IP address )
deny ip 172.16.4.0 0.0.0.255 any
deny ip 172.16.8.0 0.0.0.255 any
deny ip 172.16.12.0 0.0.0.255 any
permit ip any any

int g0/0
ip access-group iamthehuman in


## 2:18:30 - 2:42:00: DDOS attack
