# **README**



## **DHCP Server and Relay Integration**

## Introduction and Objectives

This lab focuses on integrating a DHCP server with the existing OSPF and VLAN infrastructure. The goal is to deploy a centralized DHCP server on Xubuntu (VLAN 50) and configure routers R1 and R2 as relay agents. The configuration enables dynamic IP address allocation across multiple VLANs and ensures connectivity through OSPF routing.

![TOPOLOGY-map](images/Pasted%20image%2020251007165153.png)



The objectives are:

- Configure OSPF routing between routers R1 and R2
    
- Implement VLAN segmentation and subinterfaces on routers
    
- Install and configure a DHCP server on Xubuntu (VLAN 50)
    
- Set up DHCP relay using ip helper-address commands
    
- Extend OSPF to include VLAN and server networks for full connectivity
    

---

## **Lab Structure**

1. [OSPF Routing Configuration](01-ospf-routing-configuration.md)
    
2. [VLAN and Subinterface Configuration](02-vlan-and-subinterface-configuration.md)
    
3. [DHCP and Relay Configuration](03-dhcp-server-and-relay-integration.md)


---

## **Key Features**

- Integration of OSPF, VLANs, and DHCP in one topology
    
- Centralized DHCP server using ISC DHCP on Xubuntu
    
- Relay configuration with ip helper-address
    
- VLANs dynamically receiving addresses from the server
    
- Added VLAN networks into OSPF routing for communication with the DHCP server
    

---

## **Author's Note**

This was my first attempt at creating a DHCP server on Xubuntu. The setup was more advanced than in previous labs and required careful coordination between OSPF, VLANs, and the DHCP relay. When the network finally came online and all VLANs started receiving their IP addresses correctly, it was a great feeling to see everything working as it should.


---

© 2025 - **Lukas Dula** | _Home Network Lab & Portfolio_
