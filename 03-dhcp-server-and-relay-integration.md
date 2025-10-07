
# **03 - DHCP and Relay Configuration**

## **3.1 Introduction**  

This section configures a central DHCP server on Xubuntu within VLAN 50 and enables dynamic IP assignment for clients located in VLANs 10, 20, 30, and 40. Router R1 and R2 act as DHCP relay agents, forwarding client requests between their VLANs and the DHCP server. VLAN 50 remains a server segment with a static IP address assigned to the Xubuntu server.

![](Pasted%20image%2020251007161847.png)



## **3.2 Steps**

1. Install and enable the ISC DHCP server on Xubuntu.
    
2. Assign a static IP address to the server interface ens3.
    
3. Configure the DHCP service to listen on ens3.
    
4. Create a DHCP configuration file defining address pools for VLANs 10–40.
    
5. Enable and verify the DHCP service.
    
6. Configure relay addresses on routers R1 and R2.
    
7. OSPF Network Configuration for VLANs and DHCP Connectivity

8. Static Route on DHCP Server
    
9. Verify DHCP operation and perform diagnostic ping tests between VLANs.
    



## **3.3 Configuration (Xubuntu DHCP Server)**

#### Install the DHCP package

```
sudo apt update
sudo apt install isc-dhcp-server -y
```
![instal-DHCP](images/Pasted%20image%2020251007131844.png)
#### Assign static IP address to ens3

```
sudo ip addr add 10.50.0.10/24 dev ens3
sudo ip link set ens3 up
```
![Assing](images/Pasted%20image%2020251007132032.png)

#### Define DHCP interface

```
sudo nano /etc/default/isc-dhcp-server
```
![define-DHCP](images/Pasted%20image%2020251007132207.png)

* Set the interface line as follows:

```
INTERFACESv4="ens3"
INTERFACESv6=""
```
![EDIT-DHCP](images/Pasted%20image%2020251007124510.png)

* save and exit*

#### Enable DHCP service

```
sudo systemctl enable isc-dhcp-server
```
![enable-DHCP](images/Pasted%20image%2020251007132301.png)

#### Restart and check status

```
sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server
```
![restart-check-DHCP](images/Pasted%20image%2020251007132614.png)

#### Configure DHCP pools

```
sudo nano /etc/dhcp/dhcpd.conf
```
![configure-DHCP](images/Pasted%20image%2020251007133101.png)

#### Insert the following configuration:

```
default-lease-time 600;
max-lease-time 7200;
authoritative;

# VLAN 10
subnet 10.10.10.0 netmask 255.255.255.0 {
  range 10.10.10.100 10.10.10.150;
  option routers 10.10.10.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;
}

# VLAN 20
subnet 10.20.20.0 netmask 255.255.255.0 {
  range 10.20.20.100 10.20.20.150;
  option routers 10.20.20.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;
}

# VLAN 30
subnet 10.30.30.0 netmask 255.255.255.0 {
  range 10.30.30.100 10.30.30.150;
  option routers 10.30.30.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;
}

# VLAN 40
subnet 10.40.40.0 netmask 255.255.255.0 {
  range 10.40.40.100 10.40.40.150;
  option routers 10.40.40.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;
}

# VLAN 50 (server segment, no DHCP range)
subnet 10.50.0.0 netmask 255.255.255.0 {
  option routers 10.50.0.1;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 8.8.8.8, 1.1.1.1;
}
```
![edit-DHCP-1](images/Pasted%20image%2020251007142010.png)

![edit-DHCP-2](images/Pasted%20image%2020251007142049.png)


### Restart DHCP service and verify

```
sudo systemctl restart isc-dhcp-server
sudo systemctl status isc-dhcp-server
```
![restart-DHCP](images/Pasted%20image%2020251007142344.png)

Expected output:

```
Active: active (running)
```



## **3.4 Static Route on DHCP Server**

The DHCP server (10.50.0.10) needs a default route to reach clients in other VLANs. Without it, the server can only see its own subnet (10.50.0.0/24) and cannot reply to DHCP requests from other networks. The default route sends all traffic through the router R2 (10.50.0.1).

```
sudo ip route add default via 10.50.0.1
```
![static-route-dhcp](images/Pasted%20image%2020251007144823.png)

This allows the server to send DHCP responses to all VLANs through R2.




## **3.5 Configuration (Router Relay Setup)**

#### **Router R1:**

```
configure terminal
interface gi0/2.20
ip helper-address 10.50.0.10
exit
interface gi0/2.40
ip helper-address 10.50.0.10
exit

write memory
```
![R1-RELAY](images/Pasted%20image%2020251007142520.png)

#### **Router R2:**

```
configure terminal
interface gi0/1.10
ip helper-address 10.50.0.10
exit
interface gi0/1.30
ip helper-address 10.50.0.10
exit
interface gi0/1.50
ip address 10.50.0.1 255.255.255.0
no shutdown
exit

write memory
```
![R2-RELAY](images/Pasted%20image%2020251007143208.png)


## **3.6 OSPF Network Configuration for VLANs and DHCP Connectivity**

This section explains how OSPF was extended on both routers to include all VLAN networks (10, 20, 30, 40, and 50) in the routing process. By adding these networks, full connectivity was established between all VLANs and the DHCP server in VLAN 50, allowing DHCP relay to function across the topology.



### **Router R1 Configuration**

```
conf t
router ospf 1
network 10.20.20.0 0.0.0.255 area 0
network 10.40.40.0 0.0.0.255 area 0
passive-interface GigabitEthernet0/2.20
passive-interface GigabitEthernet0/2.40
end

write memory
```
![R1-OSPF](images/Pasted%20image%2020251007153247.png)

**Explanation:**  

Router R1 advertises VLAN 20 and VLAN 40 networks in OSPF. Both interfaces are marked as passive to prevent OSPF hello packets from being sent to end devices. The OSPF adjacency with R2 is maintained via the trunk or transit interface.

---

### Router R2 Configuration

```
conf t
router ospf 1
network 10.10.10.0 0.0.0.255 area 0
network 10.30.30.0 0.0.0.255 area 0
network 10.50.0.0 0.0.0.255 area 0
passive-interface GigabitEthernet0/1.10
passive-interface GigabitEthernet0/1.30
passive-interface GigabitEthernet0/1.50
end

write memory
```
![OSPF-R2](images/Pasted%20image%2020251007153601.png)

**Explanation:**  

Router R2 advertises VLAN 10, VLAN 30, and VLAN 50 (server segment) into OSPF. This step ensures that the DHCP server network (10.50.0.0/24) is reachable by all routers. VLAN interfaces are configured as passive to avoid unnecessary OSPF hellos.

---

### Verification Router (R2)

```
show ip ospf neighbor
show ip route ospf
ping 10.50.0.10
```
![verify-R2](images/Pasted%20image%2020251007153818.png)

**Expected Results:**

- OSPF adjacency between R1 and R2 is in FULL state.
    
- Route to 10.50.0.0/24 -> this test is successful
    
- Ping from R1 to the DHCP server (10.50.0.10) is successful
    

---



> **Notes.:** Adding VLAN networks to OSPF on both routers ensures full Layer 3 visibility across the topology.  
> Including VLAN 50 (the DHCP server segment) in OSPF is essential, as it allows DHCP requests relayed by R1 to reach the server and the responses to be routed back correctly.




## **3.7 Verification (DHCP Operation)**  

Verify that the DHCP service is active and that routers forward client requests correctly.

#### **On Xubuntu:**

```
sudo systemctl status isc-dhcp-server
```
![verify-xubuntu](images/Pasted%20image%2020251007144947.png)

```
sudo journalctl -u isc-dhcp-server -b --no-pager | tail -n 20
```
![verify-2-xubuntu](images/Pasted%20image%2020251007145117.png)


#### **On Router (R1):**

```
show ip interface brief
show ip helper-address
```
![verify-R1](images/Pasted%20image%2020251007154321.png)

### **On Clients (VPCS / Xubuntu):**


#### VPC3

```
ip dhcp
show ip
```
![VPC3](images/Pasted%20image%2020251007154351.png)

#### VPC4

```
ip dhcp
show ip
```
![VPC4](images/Pasted%20image%2020251007154517.png)

#### Xubuntu-client-1

```
nmcli device status
ip a
```
![Xubuntu-1](images/Pasted%20image%2020251007154837.png)


#### xubuntu-client-2

```
nmcli device status
ip a
```
![xubuntu-2](images/Pasted%20image%2020251007154722.png)


Expected result: Each client successfully obtains an IP address from the DHCP (xubuntu-server).



## **3.8 Diagnostics (Connectivity Tests)**  

Test Layer 3 communication across VLANs and confirm reachability to the DHCP server.

### **Ping tests:**

#### ROUTER (R1)

```
ping 10.50.0.10
ping 10.10.10.100
ping 10.20.20.100
ping 10.30.30.100
ping 10.40.40.100
```
![router-1-ping](images/Pasted%20image%2020251007155640.png)

#### Xubuntu-client2 (VLAN 20)

```
ping 10.50.0.10
ping 10.10.10.100
ping 10.30.30.100
ping 10.40.40.100
```
![xubuntu-2-ping](images/Pasted%20image%2020251007155330.png)


#### **Results:**

- All clients receive IP addresses from their VLAN pools.
    
- Clients can reach the Xubuntu DHCP server (10.50.0.10).
    
- Inter-VLAN communication operates correctly via routers R1 and R2.
    



## **3.9 Conclusion** 

The DHCP server is successfully installed and configured on Xubuntu. VLAN 50 remains a static server network, and clients from VLANs 10–40 receive addresses dynamically through DHCP relay agents. The configuration provides full connectivity and centralized IP management across all VLANs.

---

**Back to project overview:** README