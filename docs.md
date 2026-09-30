##### Configuring the VLAN on the Switch (2 VLANS)
- *enable*
- *config terminal*
###### Adding VLAN areas
- *vlan 10*
- *name Data*
- *exit*
- *vlan 20*
- *name Voice*
- *exit*
###### Configure client belonging to corresponding VLAN with corresponding port (interfaces)
- *interface fa0/1*
- *switchport mode access*
- *switchport access vlan 10*
- *exit*
- *interface fa0/2*
- *switchport mode access*
- *switchport access vlan 20*
- *exit*
###### Configure switch to the router
- *interface gi0/1*
- *switchport mode trunk* (to act as a trunk link) - since the switch is carrying data for 2 different VLAN
- *exit*
- *end* 
Save the config
- *write memory*
Verify the configuration
- *show vlan brief* 

##### Configuring the router gateway
On the router 1
- Create sub-interface (path way)
	- *interface gi0/0.10*
- Connect that path way to VLAN 10 
	- *encapsulation dot1Q 10* (802.1Q is a IEEE standard)
		- Every data that sends through gi0/0.10 will automatically get a dot1Q tag 
- VLAN tags only works at Layer 1 (network)
	- To allow different VLAN to communicate with each other (we need default gateway)
		- *ip address 192.168.10.1 255.255.255.0*
- By default router and switch interface are disabled, this turns it on for usage
	- *no shutdown*
- *exit*

##### Create point-to-point communication (router-router)
Router 1
- *interface gi0/1* 
- *ip address 10.0.0.1 255.255.255.252* (allowing only 4 ip address for: network, broadcast, 2 host)
	- (Both 10.0.0.0 and 192.162.0.0 are private IP, but 10 is usually used for enterprise networks, 192 for home)
- *no shutdown* (because routers on default is turned off)
Statically creating the route (for subnet 30, 40 to reach through 10.0.0.2 - Router 2)
- *ip route 192.168.30.0 255.255.255.0 10.0.0.2*
- *ip route 192.168.40.0 255.255.255.0 10.0.0.2*
- *exit*
- *write memory*
- *show ip route*
- *show ip interface brief*
Router 2
- Repeat from above but instead
	- *interface gi0/1*
	- *ip address 10.0.0.2 255.255.255.252*
	- *no shutdown*
	- *ip route 192.168.10.0 255.255.255.0 10.0.0.1*
	- *ip route 192.168.20.0 255.255.255.0 10.0.0.1*

##### Configure DHCP server on Router 1 (Central DHCP Server)
- *ip dhcp pool VLAN10*
- *network 192.168.10.0 255.255.255.0* (if a client connects to this wifi, assign it from 192.168.10.1 to 192.168.10.254 - 0 represents the network as whole)
- *default-router 192.168.10.1* (if you want to communicate with the outside world - go through me)
- *dns-server 8.8.8.8* (if a client wants to visit a website ask 8.8.8.8 - google server)
- exit 
- *ip dhcp excluded-address 192.168.10.1* (that's the router's gateway you want to avoid assigning to clients)
- *ip dhcp excluded-address 192.168.20.1*
- *ip dhcp excluded-address 192.168.30.1*
- *ip dhcp excluded-address 192.168.40.1* (exclude all routers since Router 1 is the central DHCP server)
- *write memory*
- *show ip dhcp binding*
Then on Router 2
- *interface gi0/0.1*
- *encapsulation dot1Q 10* (connect the path way to VLAN10 through 192.168.30.1)
- *ip address 192.168.30.1 255.255.255.0*
- *ip helper-address 10.0.0.1* (let 10.0.0.1 - Router, DHCP request relays)
- *interface gi0/0.2*
- *encapsulation dot1Q 20*
- *ip address 192.168.40.1 255.255.255.0*
- *ip helper-address 10.0.0.1*

#### Error 
- When you do similar on Router 2 assigning 192.168.10.1 and 192.168.20.1 you encounter something called **IP Address Conflict**. Both routers are trying to act as a default gateway for VLAN10 and VLAN20 causing confusion, both are claiming to be the boss gateway for the same subnets. 
	- When Router 2 gets DHCP requests, router 2 forward this to router 1, but router 1 is confused because it thinks that it should be its local network
	- Instead we need to use **IP helper** for Router 2 to lead the transport to Router 1 for assigning the IP address. 

#### Learned
- VLAN
- Trunk
- dot1Q
- IP helper 
- IP address conflict
