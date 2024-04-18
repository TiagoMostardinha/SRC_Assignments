---
parent: "[[SRC]]"
date: 2024-04-09
time: 17:01
tags: 
- in-progress
- project
topics:
- network
- firewalls
- "load-balancer"
---
# High-Availability Firewall Project - Assignment for SRC
- 
---
# Team
1. Tiago Mostardinha, 103944
2. Guilherme Claro, 98432
---
# Requirements
- Build and configure a network with firewall and load-balancers
	- [ ] VPCS address config
	- [ ] Router config
		- [ ] address config
		- [ ] static routes config
	- [ ] Firewall
		- [ ] interfaces addres config
		- [ ] NAT/PAT config
		- [ ] Zone configuration
		- [ ] Firewall chains and rules
			- [ ] Firewall chains
			- [ ] TCP,UDP,SSH packet flow config
			- [ ] UDP port blockage
			- [ ] public access control config
			- [ ] Block external users during a DDoS
	- [ ] Load-Balancer configuration
		- [ ] interfaces address config
		- [ ] static route config
		- [ ] VRRP config
		- [ ] connection synchronization states config

- **Questions**
	1. Explain why the synchronization of the load-balancers allows the nonexistence of firewall synchronization.
	2. Which load balancing algorithm may also allow the nonexistence of load-balancers synchronization?
	3. Explain why device/connection states synchronization may be detrimental during a DDoS attack.
	4. Define a set of flow control policies that 
		1. Incorporate the good practices of network defense
		2. Allow the internal users a limited access to internal/DMZ and external service
		3. allow strict access to the public services available on the DMZ server(s)
		4. Block external users during a possible DDoS attack
	5. Deploy the appropriate firewall rules in the firewalls. You may assume that exists an external monitoring system that will identify the external IP address of the DDoS participants and dynamically provide an updated list
	6. (Extra) Create a script (running in a internal server directly connected to the firewalls) that automatically creates the blocking rules in the firewalls during a DDoS attack upon the identification of the attackers IP addresses. Suggestion: consider a remote connection via SSH to the firewalls
---
# Policies Definition
1. 
---
# Map of Content
1. 
---
# Network
## PC

| PC      | Subnet    | Broadcast   | Address     | Mask |
| ------- | --------- | ----------- | ----------- | ---- |
| Inside  | 10.2.2.0  | 10.2.2.255  | 10.2.2.100  | 24   |
| Outside | 200.2.2.0 | 200.2.2.255 | 200.2.2.100 | 24   |
# Router
## RouterInside

| Interface | Destination | Subnet   | Broadcast  | Address   | Mask |
| --------- | ----------- | -------- | ---------- | --------- | ---- |
| f0/0      | LB1A        | 10.1.1.0 | 10.1.1.255 | 10.1.1.10 | 24   |
| f0/0      | LB1B        | 10.1.1.0 | 10.1.1.255 | 10.1.1.10 | 24   |
| f0/1      | Inside      | 10.2.2.0 | 10.2.2.255 | 10.2.2.10 | 24   |
## RouterOutside

| Interface | Destination | Subnet    | Broadcast   | Address    | Mask |
| --------- | ----------- | --------- | ----------- | ---------- | ---- |
| f0/0      | LB1A        | 200.1.1.0 | 200.1.1.255 | 200.1.1.10 | 24   |
| f0/0      | LB1B        | 200.1.1.0 | 200.1.1.255 | 200.1.1.10 | 24   |
| f0/1      | Inside      | 200.2.2.0 | 200.2.2.255 | 200.2.2.10 | 24   |
# LB
## LB1A

| Interface | Destination  | Subnet   | Broadcast  | Address  | Mask |
| --------- | ------------ | -------- | ---------- | -------- | ---- |
| eth0      | RouterInside | 10.1.1.0 | 10.1.1.255 | 10.1.1.1 | 24   |
| eth1      | FW2          | 10.0.1.0 | 10.0.1.255 | 10.0.1.1 | 24   |
| eth2      | FW1          | 10.0.2.0 | 10.0.2.255 | 10.0.2.1 | 24   |
| eth5      | LB1B         | 10.0.5.0 | 10.0.5.255 | 10.0.5.1 | 24   |

## LB1B

| Interface | Destination  | Subnet   | Broadcast  | Address  | Mask |
| --------- | ------------ | -------- | ---------- | -------- | ---- |
| eth0      | RouterInside | 10.1.1.0 | 10.1.1.255 | 10.1.1.2 | 24   |
| eth1      | FW1          | 10.0.3.0 | 10.0.3.255 | 10.0.3.2 | 24   |
| eth2      | FW2          | 10.0.4.0 | 10.0.4.255 | 10.0.4.2 | 24   |
| eth5      | LB1A         | 10.0.5.0 | 10.0.5.255 | 10.0.5.2 | 24   |

## LB2A

| Interface | Destination   | Subnet    | Broadcast   | Address   | Mask |
| --------- | ------------- | --------- | ----------- | --------- | ---- |
| eth0      | RouterOutisde | 200.1.1.0 | 200.1.1.255 | 200.1.1.1 | 24   |
| eth1      | FW2           | 10.0.10.0 | 10.0.10.255 | 10.0.10.1 | 24   |
| eth2      | FW1           | 10.0.20.0 | 10.0.20.255 | 10.0.20.1 | 24   |
| eth5      | LB2B          | 10.0.50.0 | 10.0.50.255 | 10.0.50.1 | 24   |

## LB2B

| Interface | Destination   | Subnet    | Broadcast   | Address   | Mask |
| --------- | ------------- | --------- | ----------- | --------- | ---- |
| eth0      | RouterOutisde | 200.1.1.0 | 200.1.1.255 | 200.1.1.2 | 24   |
| eth1      | FW1           | 10.0.30.0 | 10.0.30.255 | 10.0.30.2 | 24   |
| eth2      | FW2           | 10.0.40.0 | 10.0.40.255 | 10.0.40.2 | 24   |
| eth5      | LB2B          | 10.0.50.0 | 10.0.50.255 | 10.0.50.2 | 24   |

# FW
## FW1

| Interface | Destination | Subnet    | Broadcast   | Address   | Mask |
| --------- | ----------- | --------- | ----------- | --------- | ---- |
| eth0      | LB1A        | 10.0.2.0  | 10.0.2.255  | 10.0.2.3  | 24   |
| eth1      | LB1B        | 10.0.3.0  | 10.0.3.255  | 10.0.3.3  | 24   |
| eth3      | LB2B        | 10.0.30.0 | 10.0.30.255 | 10.0.30.3 | 24   |
| eth4      | LB2A        | 10.0.20.0 | 10.0.20.255 | 10.0.20.3 | 24   |

## FW2

| Interface | Destination | Subnet    | Broadcast   | Address   | Mask |
| --------- | ----------- | --------- | ----------- | --------- | ---- |
| eth0      | LB1B        | 10.0.4.0  | 10.0.4.255  | 10.0.4.4  | 24   |
| eth1      | LB1A        | 10.0.1.0  | 10.0.1.255  | 10.0.1.4  | 24   |
| eth3      | LB2A        | 10.0.10.0 | 10.0.10.255 | 10.0.10.4 | 24   |
| eth4      | LB2B        | 10.0.40.0 | 10.0.40.255 | 10.0.40.4 | 24   |



---
# Configuration

## Inside - config
```
#Inside
ip 10.2.2.100/24 10.2.2.10
save
```
## Outside - config
```
#Outside
ip 200.2.2.100/24 200.2.2.10
save
```

## RouterInside - config
```
!RouterInside
conf t

int f0/0
ip address 10.1.1.10 255.255.255.0
no shutdown
int f0/1
ip address 10.2.2.10 255.255.255.0
no shutdown

end
write

! static ip route
conf t

ip route 0.0.0.0 0.0.0.0 10.1.1.1
ip route 0.0.0.0 0.0.0.0 10.1.1.2

end
write
```

## RouterOutside - config
```
!RouterOutside
conf t

int f0/0
ip address 200.1.1.10 255.255.255.0
no shutdown
int f0/1
ip address 200.2.2.10 255.255.255.0
no shutdown

end
write

! static ip route
conf t

ip route 192.1.0.0 255.255.254.0 200.1.1.1
ip route 192.1.0.0 255.255.254.0 200.1.1.2

end
write
```

## FW & LB - init config
```
# init
sudo cp /opt/vyatta/etc/config.boot.default /config/config.boot
reboot
```

## LB1A - config
```
# interfaces config
configure

set system host-name LB1A
set interfaces ethernet eth0 address 10.1.1.1/24
set interfaces ethernet eth1 address 10.0.1.1/24
set interfaces ethernet eth2 address 10.0.2.1/24
set interfaces ethernet eth5 address 10.0.5.1/24

commit
save
exit

# static ip route
configure

set protocols static route 10.2.2.0/24 next-hop 10.1.1.10

commit
save
exit
```

## LB1B - config
```
# interfaces config
configure

set system host-name LB1B
set interfaces ethernet eth0 address 10.1.1.2/24
set interfaces ethernet eth1 address 10.0.3.2/24
set interfaces ethernet eth2 address 10.0.4.2/24
set interfaces ethernet eth5 address 10.0.5.2/24

commit
save
exit

# static ip route
configure

set protocols static route 10.2.2.0/24 next-hop 10.1.1.10

commit
save
exit
```


## LB2A - config
```
# interfaces config
configure

set system host-name LB2A
set interfaces ethernet eth0 address 200.1.1.1/24
set interfaces ethernet eth1 address 10.0.10.1/24
set interfaces ethernet eth2 address 10.0.20.1/24
set interfaces ethernet eth5 address 10.0.50.1/24

commit
save
exit

# static ip route
configure

set protocols static route 200.2.2.0/24 next-hop 200.1.1.10

commit
save
exit
```

## LB2B - config
```
# interfaces config
configure

set system host-name LB2B
set interfaces ethernet eth0 address 200.1.1.2/24
set interfaces ethernet eth1 address 10.0.30.2/24
set interfaces ethernet eth2 address 10.0.40.2/24
set interfaces ethernet eth5 address 10.0.50.2/24

commit
save
exit

# static ip route
configure

set protocols static route 200.2.2.0/24 next-hop 200.1.1.10

commit
save
exit
```

## FW1 - config
```
# interfaces config
configure

set system host-name FW1
set interfaces ethernet eth0 address 10.0.2.3/24
set interfaces ethernet eth1 address 10.0.3.3/24
set interfaces ethernet eth3 address 10.0.30.3/24
set interfaces ethernet eth4 address 10.0.20.3/24

commit
save
exit


# static ip route
configure

set protocols static route 0.0.0.0/0 next-hop 10.0.20.1
set protocols static route 0.0.0.0/0 next-hop 10.0.30.2

set protocols static route 10.2.2.0/24 next-hop 10.0.2.1
set protocols static route 10.2.2.0/24 next-hop 10.0.3.2

commit
save
exit

# nat pat config
configure

set nat source rule 10 outbound-interface eth3
set nat source rule 10 outbound-interface eth4
set nat source rule 10 source address 10.0.0.0/8
set nat source rule 10 translation address 192.1.0.1-192.1.0.10

commit
#save
exit

```

## FW2 - config
```
# interfaces config
configure

set system host-name FW2
set interfaces ethernet eth0 address 10.0.4.4/24
set interfaces ethernet eth1 address 10.0.1.4/24
set interfaces ethernet eth3 address 10.0.10.4/24
set interfaces ethernet eth4 address 10.0.40.4/24

commit
save
exit

# static ip route
configure

set protocols static route 0.0.0.0/0 next-hop 10.0.10.1
set protocols static route 0.0.0.0/0 next-hop 10.0.40.2

set protocols static route 10.2.2.0/24 next-hop 10.0.1.1
set protocols static route 10.2.2.0/24 next-hop 10.0.4.2

commit
save
exit

# nat pat config
configure

set nat source rule 10 outbound-interface eth3
set nat source rule 10 outbound-interface eth4
set nat source rule 10 source address 10.0.0.0/8
set nat source rule 10 translation address 192.1.0.1-192.1.0.10

commit
#save
exit

```
