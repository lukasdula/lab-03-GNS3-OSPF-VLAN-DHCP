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

In this third lab, I set up a DHCP server on Xubuntu to show how it connects with other clients. The setup was more complex than before and needed coordination between OSPF, VLANs, and the DHCP relay. When everything started working and all VLANs got their IP addresses correctly, it felt great to see the network running as expected.


---

© 2025 - Lukas Dula | Home Networking Lab & Portfolio
