# **2 - VLAN and Subinterface Configuration**

<br><br>

## **2.1 Introduction**  

This note configures VLANs, trunk links, and router subinterfaces to separate Layer 2 domains and prepare the network for DHCP in the next part. OSPF already provides Layer 3 backbone connectivity.


![TOPOLOGY-MAP-2](images/Pasted%20image%2020251007031313.png)

<br><br>

## <br><br> 2.2 Topology

| **Switch** | **VLAN**   | **Purpose / Device** | **Access Port →** | **Trunk Port → Router** | **Subinterface** |
| ------ | ------ | ---------------- | ------------- | ------------------- | ------------ |
| SW1    | VLAN10 | Xubuntu-client-1 | Gi0/1         | Gi0/2 → R2          | Gi0/1.10     |
| SW1    | VLAN30 | PC3              | Gi0/3         | Gi0/2 → R2          | Gi0/1.30     |
| SW2    | VLAN20 | Xubuntu-client-2 | Gi0/2         | Gi0/1 → R1          | Gi0/2.20     |
| SW2    | VLAN40 | PC4              | Gi0/3         | Gi0/1 → R1          | Gi0/2.40     |
| SW1    | VLAN50 | Xubuntu-server   | Gi0/0         | Gi0/2 → R2          | Gi0/1.50     |

<br><br>

## **2.3 Steps**

1. Create all required VLANs on both switches.
    
2. Assign access ports for end devices to their VLANs.
    
3. Configure trunk ports connecting switches and routers.
    
4. Verify VLAN and trunk status on both switches.
    
5. Create router subinterfaces with 802.1Q encapsulation.
    
6. Basic diagnostics of VLANs and interfaces.
    
<br><br>

## **2.4 VLAN Setup**  

VLANs are created to segment the network and assign devices to specific broadcast domains. Each VLAN is associated with its access port.


- Create VLANs with names.
    
- Assign access ports to the appropriate VLANs.
    
* Activate ports and save configuration.

### Switch (SW1)

```plaintext
enable
configure terminal

vlan 10
name VLAN10
exit
interface gi0/1
switchport mode access
switchport access vlan 10
no shutdown
exit

vlan 30
name VLAN30
exit
interface gi0/3
switchport mode access
switchport access vlan 30
no shutdown
exit

vlan 50
name VLAN50
exit
interface gi0/0
switchport mode access
switchport access vlan 50
no shutdown
end

write memory
```
![SW1-VLAN](images/Pasted%20image%2020251007021545.png)

### Switch (SW2)

```plaintext
enable
configure terminal

vlan 20
name VLAN20
exit
interface gi0/2
switchport mode access
switchport access vlan 20
no shutdown
exit

vlan 40
name VLAN40
exit
interface gi0/3
switchport mode access
switchport access vlan 40
no shutdown
end

write memory
```
![SW2-VLAN](images/Pasted%20image%2020251007022123.png)

<br><br>

## **2.5 Trunk Configuration**  

Trunk links allow multiple VLANs to travel over one physical connection between switches and routers.

- Configure trunk mode and allowed VLANs.
    
- Enable trunk interfaces and save configuration.
    

**Configuration in CLI (SW1):**

```plaintext
enable
configure terminal

interface gi0/2
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 10,30,50
no shutdown
exit

write memory
```
![TRUNK-SW1](images/Pasted%20image%2020251007022521.png)

**Configuration in CLI (SW2):**

```plaintext
enable
configure terminal

interface gi0/1
switchport trunk encapsulation dot1q
switchport mode trunk
switchport trunk allowed vlan 20,40
no shutdown
exit

write memory
```
![TRUNK-SW2](images/Pasted%20image%2020251007022326.png)

<br><br>

## **2.6 Subinterface Configuration**  

Router subinterfaces are created to logically separate traffic for each VLAN over a single physical interface. Encapsulation identifies VLAN tags.


- Create subinterfaces for each VLAN.
    
- Apply 802.1Q encapsulation with IP addresses(gateway).
    
- Enable interfaces and save configuration.
    

### Router (R2) –>> toward SW1 (Gi0/1)

```plaintext
enable
configure terminal

interface gi0/1.10
encapsulation dot1Q 10
ip address 10.10.10.1 255.255.255.0
no shutdown
exit

interface gi0/1.30
encapsulation dot1Q 30
ip address 10.30.30.1 255.255.255.0
no shutdown
exit

interface gi0/1.50
encapsulation dot1Q 50
ip address 10.50.0.1 255.255.255.0
no shutdown
exit

write memory
```
![ENCAPSULATION-R2](images/Pasted%20image%2020251007141348.png)
### Router (R1) –>> toward SW2 (Gi0/2)

```plaintext
enable
configure terminal

interface gi0/2.20
encapsulation dot1Q 20
ip address 10.20.20.1 255.255.255.0
no shutdown
exit

interface gi0/2.40
encapsulation dot1Q 40
ip address 10.40.40.1 255.255.255.0
no shutdown
exit

write memory
```
![encapsulation-R1](images/Pasted%20image%2020251007141704.png)

<br><br>

## **2.7 Diagnostics**  

Network verification confirms VLAN, trunk, and subinterface status. Since DHCP is not yet configured, ping tests are not included.

- Check VLAN and trunk configuration on switches.
    
- Verify router subinterfaces are created and active.
    

#### **Verification commands (switch SW1):**

```plaintext
show vlan brief
show interfaces trunk
show ip interface brief
```
![verify-SW1](images/Pasted%20image%2020251007023441.png)


#### **Verification commands (switch SW2):**

```plaintext
show vlan brief
show interfaces trunk
show ip interface brief
```
![verify-SW2](images/Pasted%20image%2020251007023547.png)


#### **Verification commands (router R1):**

```plaintext
show ip interface brief
```
![verify-R1](images/Pasted%20image%2020251007023044.png)


```
show running-config
```
![verify-encapsulation-R1](images/Pasted%20image%2020251007141742.png)
 
#### **Verification commands (router R2):**

```plaintext
show ip interface brief
```
![verify-R2](images/Pasted%20image%2020251007023226.png)

```
show running-config
```
![verify-encapsulation-R2](images/Pasted%20image%2020251007141504.png)

>**Note.:** End devices currently have no IP addresses because DHCP is not yet configured. Ping tests will be performed after DHCP setup in the next note.

<br><br>

## **2.8 Conclusion**  

VLANs and trunks are configured and operational on SW1 and SW2. Router subinterfaces are created with 802.1Q encapsulation and enabled. End devices will receive IP addresses via DHCP in the next part, where connectivity tests between VLANs will be executed.

---

**Next part:** [DHCP and Relay Configuration](03-dhcp-server-and-relay-integration.md)

