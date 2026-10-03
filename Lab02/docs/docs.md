##### Setting up the network
- Adding HWIC-4ESW to Router 2911 to create 4 extra switching ports (FastEthernet)

##### Setting up DHCP (to assign IP-address automatically)
- I tried to set up DHCP but realized I couldn't because all the switches are using FastEthernet port, and assigning IP-address in mainly in Layer 3. So I needed to create VLAN for each pool in order for it to work.

###### Setting up VLAN (Layer 2 access port) on Switch
- vlan 10 
- exit
- interface fa0/1
- switchport mode access
- switchport access vlan 10
- exit

##### On the router connect it to the corresponding VLAN (Interface)
We need to create VLAN that exists for each switch on router as well, and then: 
- *interface fa0/3/0*
- *switchport mode access*
- *switchport access vlan 10*
- *exit*
and so on

##### Configure each Switch to communicate with router
For default gateway on the router
- *interface vlan 10* (for the server room)
- *ip address 192.168.10.1 255.255.255.0*
- *no shutdown*
- *exit*
and so on for IT, Sales, and HR with vlan20 - vlan40

##### Setting up DHCP Server on the Router
- *ip dhcp pool VLAN10*
- *network 192.168.10.0 255.255.255.0*
- *default-router 192.168.10.1* (if you want to communicate with the outside world - go through me)
- *dns-server 192.168.10.2* (the server is configured statically, because we don't want server to be inconsistent where we are not sure of IP-address because of DHCP)
- exit 
- *ip dhcp excluded-address 192.168.10.1* (that's the router's gateway you want to avoid assigning to clients)
- *ip dhcp excluded-address 192.168.10.2* (we don't want other devices in VLAN10 to get the server's IP)
- *ip dhcp excluded-address 192.168.20.1*
- *ip dhcp excluded-address 192.168.30.1*
- *ip dhcp excluded-address 192.168.40.1*
- *write memory*
- *show ip dhcp binding*

##### Error 
- I didn't realized that switchport in default uses vlan1 
- I also realized that you need to add the vlan to the router as well so it understands how to talk to switches
- I also forgot that you need to configure every port to the vlan, not just from the switch to the router

- The purpose of the Server is to handle data internally, that is accessible by the company's computers. For e.g. providing pathway to Active Directory of specific worker/employee to log in or accessing internal website. 
