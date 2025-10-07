
# **1 - OSPF Routing Configuration**

## **1.1 Introduction**  

This section creates Layer 3 connectivity between two routers, R1 and R2, using the OSPF routing protocol. The goal is to enable dynamic route exchange within a single OSPF area (Area 0). Both routers are part of the same point-to-point network, which allows them to automatically form adjacency and exchange routing information.  
This setup will be the base for VLAN and DHCP configuration in the next parts of the lab.

![TOPOLOGY-map-1](images/Pasted%20image%2020251007014838.png)

## **1.2 Topology**  

The following devices are used in this part of the lab. Both routers are connected through switch SW3, which acts as a simple Layer 2 connection for OSPF adjacency.

| Device | Interface | Connected to → | Peer Interface | IP Address | Subnet Mask     | Gateway |
| ------ | --------- | -------------- | -------------- | ---------- | --------------- | ------- |
| R1     | Gi0/1     | SW3            | Gi0/1 (R2)     | 10.10.1.1  | 255.255.255.252 | -       |
| R2     | Gi0/2     | SW3            | Gi0/2 (R1)     | 10.10.1.2  | 255.255.255.252 | -       |


## **1.3 Steps**

1. Configure IP addresses on router interfaces.
    
2. Enable OSPF routing process.
    
3. Set router IDs for R1 and R2.
    
4. Advertise directly connected networks.
    
5. Verify OSPF neighbor relationship and routing table.
    

## **1.4 Configuration**

### **Router (R1)**

```
configure terminal
hostname R1
interface gi0/1
ip address 10.10.1.1 255.255.255.252
no shutdown
exit
router ospf 1
router-id 1.1.1.1
network 10.10.1.0 0.0.0.3 area 0
exit
write memory
```
![OSPF-R1](images/Pasted%20image%2020251006215506.png)

### **Router (R2)**

```
configure terminal
hostname R2
interface gi0/2
ip address 10.10.1.2 255.255.255.252
no shutdown
exit
router ospf 1
router-id 2.2.2.2
network 10.10.1.0 0.0.0.3 area 0
exit
write memory
```
![OSPF-R2](images/Pasted%20image%2020251006215625.png)

## **1.5 Verification**  

After both routers are configured, check that OSPF neighbors are formed and routes are visible in the routing table.

```
R1# show ip ospf neighbor
R1# show ip route ospf
R1# ping 10.10.1.2
R1# show ip interface brief
```
![VERIFY](images/Pasted%20image%2020251006220215.png)

## **1.6 Conclusion** 

The routers are now successfully configured with OSPF within Area 0.  
Both devices recognize each other and have exchanged routing information over the shared network.  
This configuration establishes a stable backbone connection that will serve as the base for the next part,  
where VLANs will be added to extend and segment the network.

---

**Next part:**[OSPF Routing Configuration](01-ospf-routing-configuration.md)
