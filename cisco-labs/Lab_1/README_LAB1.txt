Design a network in CISCO packet tracer to connect ACCOUNTS and DELIVERY departments through the following: [40 points]  

a. Each department should contain at least 2 PCs + 1 printer.  

b. Appropriate number of switches and routers should be used in the network.  

c. Using the given network address 192.168.40.0, all interfaces should be configured with appropriate IP addresses, subnet mask and gateways.  

d. All devices in the network should be connected using appropriate cables.  

e. Test the connectivity between ACCOUNTS and DELIVERY department - PCs in DELIVERY department should be able to ping the PCs in ACCOUNTS department.


## Tasks:

1. How many subnets do we need to use for this network ? Calculate them ( block size, range subnets, broadcast id)
2. Connect 2 switch to a router, 2 PCs and 1 printer to each switch
3. Configure router to assign IP dynamically to switches
4. Configure IP address, default gateway, subnet mask to each PC
5. Ping from PC to each other and make sure the data sent won't lost ( example: ping <IP address> )


## Solution:

		+-------------Router-------+
    		|			   |		
PC0 --------- Switch 1			Switch 2 ----------- PC2
               | |                        | |
PC1 -----------+ |			  | +---------------- PC3
Printer1 --------+                        +---------------- Printer 2


1. Take 1 router, 2 switches, 2 printers, and 4 PCS
2. Connect router to 2 switches by cable, each switch connect 2 different PCs, and 1 printer
3. Calculate subnet IP:

Network address = 192.168.40.0
The number of subnet IP  = the number of departments = 2

2^n = the number of subnets <=> n = 1 

n represents the number of borrowed bits calculating the subnet mask

=> Subnet mask = 255.255.255.128 ( 11111111.11111111.11111111.10000000 ) => /25
Network ID = 192.168.40.0 
Block size: 256 - 128 = 128

- 1st subnet:
Range of valid host = 192.168.40.1 - 192.168.40.126
Broadcast ID = 192.168.40.127

- 2nd subnet:
Range of valid host = 192.168.40.129 - 192.168.40.254
Broadcast ID = 192.168.40.255


## CMDS:

Router:
Connect router to 2 switch: `int range <interface/<fist-number>>-<last-number>` + no shut
example: `int range g0/0-1` + `no shut` -> change all status of int g0/0 and g0/1 into up
Configure router int:
int g0/0
ip address 192.168.40.1 255.255.255.128
int g0/1
ip address 192.168.40.129 255.255.255.128


PC0:
IPv4: 192.168.40.2
Subnet mask: 255.255.255.128
Default gateway: 192.168.40.1 ( IP of switch )

PC1:
IPv4: 192.168.40.3
Subnet mask: 255.255.255.128
Default gateway: 192.168.40.1 ( IP of switch )

Printer 1:
IPv4: 192.168.40.4
Subnet mask: 255.255.255.128
Default gateway: 192.168.40.1 ( IP of switch )

==================================================

PC2:
IPv4: 192.168.40.130
Subnet mask: 255.255.255.128
Default gateway: 192.168.40.129 ( IP of switch )

PC3:
IPv4: 192.168.40.131
Subnet mask: 255.255.255.128
Default gateway: 192.168.40.129 ( IP of switch )

Printer 2:
IPv4: 192.168.40.132
Subnet mask: 255.255.255.128
Default gateway: 192.168.40.129 ( IP of switch )
