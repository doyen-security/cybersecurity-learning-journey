# Module 03 – Network Fundamentals

## Objectives

- Understand networking basics
- Learn the OSI model
- Learn the TCP/IP model
- Understand IP addressing
- Learn subnetting basics
- Understand ports and protocols
- Practice basic network troubleshooting


---

# Kali Linux & Metasploitable 2 Lab Network

This section documents the practical network configuration and troubleshooting
performed using Kali Linux and Metasploitable 2 in Oracle VirtualBox.

The goal was to create a lab environment where Kali could:

- Access the Internet for normal browsing and security tools
- Communicate with Metasploitable 2 through an isolated network
- Use a static lab IP address
- Maintain connectivity after configuration changes and reboots

## Lab Topology

```text
                         INTERNET
                            |
                       VirtualBox NAT
                            |
                     Kali Linux eth0
                       10.0.2.15/24
                            |
                       KALI LINUX
                            |
                     Kali Linux eth1
                       192.168.10.3
                            |
                    Internal Network
                         intnet
                            |
                            |
                    Metasploitable 2
                       192.168.10.4

| Device           | Interface | Network | IP Address      | Purpose              |
| ---------------- | --------- | ------- | --------------- | -------------------- |
| Kali Linux       | eth0      | NAT     | 10.0.2.15/24    | Internet access      |
| Kali Linux       | eth1      | intnet  | 192.168.10.3/24 | Isolated lab network |
| Metasploitable 2 | eth0      | intnet  | 192.168.10.4/24 | Vulnerable target    |

Lab Network
Network: 192.168.10.0/24
Kali Linux: 192.168.10.3/24
Metasploitable 2: 192.168.10.4/24
VirtualBox Network Configuration
Kali Linux

Kali Linux was configured with two network adapters.

Adapter 1 — Internet

Attached to: NAT

Purpose:

Internet access
Web browsing
Package updates
Adapter 2 — Lab Network

Attached to: Internal Network
Name: intnet

Purpose:

Isolated cybersecurity lab
Communication with Metasploitable 2
Final Kali Network Layout
eth0 → NAT → 10.0.2.15/24 → Internet

eth1 → intnet → 192.168.10.3/24
                 |
                 |
          Metasploitable 2
             192.168.10.4
Both machines share the isolated intnet lab network through their respective interfaces.

Configuring Kali Linux
After enabling the second VirtualBox adapter, Kali detected:
eth0
eth1
The second interface was configured with:
sudo ip addr add 192.168.10.3/24 dev eth1
sudo ip link set eth1 up
The configuration was verified  with:
ip addr show eth1
Expected address  192.168.10.3/24
Initial Connectivity Problem

The first connectivity test failed:
ping -c 4 192.168.10.4
Kali returned  Destination Host Unreachable
Cause

Kali had two network interfaces:
eth0 → NAT
eth1 → Internal Network (intnet)
Metasploitable 2 was initially connected to: NAT Network → NATNetwork

Because Metasploitable 2 was not connected to the same isolated Internal Network as Kali's eth1 interface, Kali could not reach the target at 192.168.10.4.

Solution

Metasploitable 2 was changed to:
Internal Network → intnet

Configuring Metasploitable 2

After connecting Metasploitable 2 to the Internal Network, it did not automatically receive an IPv4 address because the Internal Network was not providing DHCP.

A temporary address was assigned:
sudo ifconfig eth0 192.168.10.4 netmask 255.255.255.0 up

The address was verified using:
ifconfig eth0
The result showed
inet addr:192.168.10.4
Mask:255.255.255.0

Testing Kali ↔ Metasploitable Connectivity

From Kali:  ping -c 4 192.168.10.4
The test was successful
4 packets transmitted
4 packets received
0% packet loss
This confirmed communication between Kali and the Metasploitable target.

Restoring Kali Internet Connectivity

After adding the second network interface, Kali's eth0 interface was initially disconnected.

The status was checked using:  nmcli device status
he existing NetworkManager connection was identified as:  Wired connection 1
The connection was configured to use DHCP on eth0:
sudo nmcli connection modify "Wired connection 1" \
connection.interface-name eth0 \
ipv4.method auto \
ipv4.addresses "" \
ipv4.gateway "" \
ipv4.dns ""

The connection was activated:
sudo nmcli connection up "Wired connection 1"
Then Kali received  10.0.2.15/24
and regained its default route:  default via 10.0.2.2 dev eth0
Internet Connectivity Tests

Internet connectivity was tested using: ping -c 4 8.8.8.8
result
4 packets transmitted
4 received
0% packet loss
DNS resolution was then tested
ping -c 4 google.com
result
4 packets transmitted
4 received
0% packet loss

This confirmed:

Internet connectivity
Default routing
DNS resolution
Making Metasploitable's IP Persistent

The initial IP configuration using ifconfig was temporary and could disappear after reboot.

The existing network configuration was inspected with:
cat /etc/network/interfaces
The original configuration used DHCP:
auto eth0
iface eth0 inet dhcp

It was changed to a static configuration:
 auto eth0
iface eth0 inet static
    address 192.168.10.4
No Internet gateway was configured because Metasploitable 2 is intended to remain isolated from the Internet.

Persistence Verification

Metasploitable 2 was rebooted after the static configuration was applied.

After reboot, the interface was checked: ifconfig eth0
The address remained  192.168.10.4
Kali then tested the connection again
ping -c 4 192.168.10.4
Result
4 packets transmitted
4 received
0% packet loss
This confirmed that the Metasploitable IP configuration survived the reboot.
## Final Verification
The final lab configuration was tested from Kali.
ping -c 4 192.168.10.4
Result
0% packet loss
Kali → Internet
ping -c 4 8.8.8.8
result   0% packet loss
DNS Resolution
ping -c 4 google.com
Result   0% packet loss
Final Working Configuration
eth0
Network: NAT
IP: 10.0.2.15/24
Purpose: Internet access

eth1
Network: Internal Network (intnet)
IP: 192.168.10.3/24
Purpose: Cybersecurity lab

Metasploitable 2
eth0
Network: Internal Network (intnet)
IP: 192.168.10.4/24
Purpose: Vulnerable target
Troubleshooting Lessons
Problem 1 — Destination Host Unreachable

Cause: Kali and Metasploitable 2 were connected to different VirtualBox networks.

Solution: Both machines were connected to:

Internal Network → intnet

Problem 2 — Metasploitable Lost Its IPv4 Address

Cause: The Internal Network did not provide DHCP.

Solution: A static IPv4 address was configured:

192.168.10.4/24
Problem 3 — Kali Lost Internet Connectivity

Cause: eth0 did not have an active NetworkManager connection.
Solution: The existing Wired connection 1 profile was restored on eth0 using DHCP.

Problem 4 — Metasploitable's IP Disappeared After Reboot

Cause: The address had initially been assigned temporarily using ifconfig.

Solution: A persistent static configuration was added to: /etc/network/interfaces

Snapshots

Snapshots were created for both virtual machines after reaching the known-good network configuration.

Kali Linux Snapshot
eth0 → NAT → Internet
eth1 → intnet → 192.168.10.3
Metasploitable 2 Snapshot  eth0 → intnet → 192.168.10.4

These snapshots provide recovery points before continuing with future cybersecurity exercises.

Evidence / Screenshots

Screenshots will be added to document the configuration and testing process.

Planned evidence includes:

VirtualBox Kali network configuration
VirtualBox Metasploitable network configuration
Kali IP address configuration
Metasploitable IP address configuration
Kali routing table
Kali-to-Metasploitable connectivity test
Kali Internet connectivity test
DNS connectivity test
Key Concepts Practiced

This lab provided practical experience with:

VirtualBox NAT networking
VirtualBox Internal Networking
Multiple network interfaces
IPv4 addressing
CIDR notation
/24 subnet masks
DHCP
Static IP addressing
Default gateways
Routing tables
NetworkManager
Network troubleshooting
Connectivity testing
DNS verification
Isolated cybersecurity lab design
Conclusion

A functional cybersecurity lab network was successfully established using Kali Linux and Metasploitable 2.

Kali Linux can access the Internet through NAT while maintaining an isolated connection to Metasploitable 2 through the intnet Internal Network.

Final Lab Addresses
Kali Linux:       192.168.10.3
Metasploitable 2: 192.168.10.4

Connectivity was successfully verified with 0% packet loss, and snapshots were created for both virtual machines at the known-good configuration.

The lab is now ready for further controlled cybersecurity exercises such as network enumeration, service discovery, vulnerability assessment, and exploitation within the isolated environment.

## Notes

- [OSI Model](notes/osi-model.md)



    


 
