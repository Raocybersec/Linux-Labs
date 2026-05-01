Lab 1: Linux Network Stack Analysis & Baseline

Date: 01 May 2026

Environment: Ubuntu Workstation

Objective: To audit the local network stack and document the relationship between the physical (Layer 2) and logical (Layer 3) layers.
1. Network Interface Discovery (ip links

The first step in network defense is identifying the "Attack Surface""the physical and virtual doors that allow data to enter the system.
Command:

ip link show

Analyze:

    Interface wlp3s0: Identified as the primary wireless interface.

    Status:  UP(Indicates the hardware is active and the kernel is ready for traffic).

    MAC Address:  :::::(Sanitized). This is the permanent hardware ID used at the Network Access Layer.

    Loopback (lo): Confirmed MTU 65536Uh, . This high value is possible because the traffic never leaves the system RAM, avoiding physical wire limitations.

Cloud Security Application: In a cloud environment (AWS/Azure), this command is verify used to that Elastic Network Interfaces (ENIs) are correctly attached to the virtual instance and that the MTU is optimized for high-speed backbone communication.

2. Logical Routing Audit (ip routes

Once the hardware is confirmed, we must map how the system decides where to send data packets.
Command:

ip route show

Sanitized OUtput: 

default via 192.168.**.1 dev wlp3s0 proto dhcp src 192.168.**.*** metric 600 
192.168.**.0/24 dev wlp3s0 proto kernel scope link src 192.168.**.*** metric 600

Key Findings:

    Default Gateway: 192.168..1Uh, . This is the "Exit" for all traffic destined for the internet.

    Local Subnet: 192.168..0/24Uh, . This defines the trusted local network where the system can communicate directly with other devices without a router.

    Source IP: 192.168..***Uh, . This is the logical identity assigned to this workstation.

Cloud Security Application: This audit is essential for confirming VPC Route Tables. It ensures that sensitive traffic is routed through a Secure Appliance or NAT Gateway rather than leaking directly to the public internet.

3. Hardware-to-IP Mapping (ip neighbors

This command bridges the gap between the IP address (software) and the MAC address (hardware) using the ARP Protocol.

Command: 

ip neighbor show

Sanitized OUtput: 

192.168.**.1 dev wlp3s0 lladdr **:**:**:**:**:** REACHABLE

Security Observation:

The Gateway IP  192.168..1is correctly mapped to the hardware ID of the router.

Incident Response (Blue Teaming): I monitor this table to detect ARP Spoofing. If the  lladdr(MAC) for my gateway changes unexpectedly, it indicates a potential Man-in-the-Middle (MitM) attack where a malicious actor is attempting to my traffic.

Conclusion

By mapping these three layers, I have established a security baseline for this workstation. This understanding of the Linux network stacking is foundational for secure managing, scalable virtual networks in a Cloud Infrastructure.
