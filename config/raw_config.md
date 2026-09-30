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
- *interface gi0/0.10*
- *encapsulation dot1Q 10* (802.1Q is a IEEE standard)
- *ip address 192.168.10.1 255.255.255.0*
- *no shutdown*
- *exit*

##### Create point-to-point communication (router-router)
Router 1
- *interface gi0/1* 
- *ip address 10.0.0.1 255.255.255.252* 
- *no shutdown* 
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
- *network 192.168.10.0 255.255.255.0* 
- *default-router 192.168.10.1* 
- *dns-server 8.8.8.8* 
- exit 
- *ip dhcp excluded-address 192.168.10.1*
- *ip dhcp excluded-address 192.168.20.1*
- *ip dhcp excluded-address 192.168.30.1*
- *ip dhcp excluded-address 192.168.40.1*
- *write memory*
- *show ip dhcp binding*
Then on Router 2
- *interface gi0/0.1*
- *encapsulation dot1Q 10* 
- *ip address 192.168.30.1 255.255.255.0*
- *ip helper-address 10.0.0.1* 
- *interface gi0/0.2*
- *encapsulation dot1Q 20*
- *ip address 192.168.40.1 255.255.255.0*
- *ip helper-address 10.0.0.1*
