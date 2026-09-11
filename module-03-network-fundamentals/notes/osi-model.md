# OSI Model

## Overview

The **OSI (Open Systems Interconnection) Model** is a seven-layer framework for understanding how data moves between networked devices. It is especially useful for learning networking and troubleshooting connection problems.

When sending data, information moves from **Layer 7 down to Layer 1**. When receiving it, the process reverses from **Layer 1 up to Layer 7**.

## The Seven Layers

| Layer | Name | Plain-English purpose | Examples |
|---:|---|---|---|
| 7 | Application | Provides network services to applications. | HTTP/HTTPS, DNS, SSH, FTP, SMTP |
| 6 | Presentation | Formats, encrypts, compresses, or translates data. | TLS/SSL, UTF-8, JPEG |
| 5 | Session | Starts, manages, and ends communication sessions. | Session setup and recovery |
| 4 | Transport | Delivers data to the right application using ports. | TCP, UDP, ports 22/80/443 |
| 3 | Network | Uses IP addresses and routes data between networks. | IPv4/IPv6, routers, ICMP |
| 2 | Data Link | Moves data on the local network using hardware addresses. | Ethernet, Wi-Fi, MAC addresses, switches |
| 1 | Physical | Sends bits through electrical, light, or radio signals. | Cables, fibre, connectors, Wi-Fi signals |

## Sending-a-Document Analogy

Think of sending a document to another office:

1. **L7 — Application:** Create the document and choose a service to send it.
2. **L6 — Presentation:** Format or encrypt the document.
3. **L5 — Session:** Start and manage the conversation.
4. **L4 — Transport:** Break the document into pieces and ensure reliable delivery when using TCP.
5. **L3 — Network:** Add the destination office address and choose a route.
6. **L2 — Data Link:** Label each local delivery step with a hardware address.
7. **L1 — Physical:** Move it physically, like bits travelling through cable, fibre, or Wi-Fi.

The recipient processes the document in reverse order before reading it.

## Addressing by Layer

| Identifier | Layer | Purpose |
|---|---:|---|
| **MAC address** | L2 | Identifies a network interface on the local network |
| **IP address** | L3 | Identifies a device/network location across routed networks |
| **Port number** | L4 | Identifies a specific service on a device |
| **Application protocol** | L7 | Identifies the network service being used |

Quick memory guide:

- **MAC = L2**
- **IP = L3**
- **Port = L4**
- **Application protocol = L7**

## Encapsulation and Decapsulation

### Encapsulation: Sending (L7 → L1)

As data moves down the model, each layer adds information needed for delivery.

| Layer | Data unit | Information added |
|---:|---|---|
| L7–L5 | Data | Application, formatting, and session information |
| L4 | TCP segment / UDP datagram | Source and destination ports |
| L3 | Packet | Source and destination IP addresses |
| L2 | Frame | Source and destination MAC addresses |
| L1 | Bits | Signals sent through the network medium |

### Decapsulation: Receiving (L1 → L7)

The receiving device reads the bits and removes each layer’s information in reverse order until the original application data reaches the correct program.

## Practical Example: `192.168.10.4:22`

`192.168.10.4:22` means:

- `192.168.10.4` is the destination **IP address** at **Layer 3**.
- `22` is the destination **TCP port** at **Layer 4**.
- Port 22 commonly indicates **SSH**, an application-layer protocol at **Layer 7**.

For a remote SSH connection, routers use the IP address to forward traffic between networks. On each local network hop, Layer 2 frames use MAC addresses for local delivery.

## Troubleshooting Notes

| Symptom | Layer(s) to check | Examples |
|---|---|---|
| No connection, cable issue, Wi-Fi disabled | L1 | Cable, port lights, adapter, signal strength |
| Local devices cannot communicate | L2 | Switch port, VLAN, MAC learning, Wi-Fi association |
| Cannot reach another network | L3 | IP address, subnet mask, default gateway, routes |
| Device responds but service is unavailable | L4 | TCP/UDP port, firewall, listening service |
| Port is reachable but the app fails | L7 | DNS, SSH/HTTP configuration, credentials, logs |

Examples:

- If a website name fails but its IP address works, investigate **DNS at Layer 7**.
- If ping works but SSH to port 22 fails, basic IP connectivity works; check **TCP port 22, firewall rules, and the SSH service**.
- If a computer has no network connection at all, start with **Layers 1 and 2**.
- If a device reaches its local network but not the internet, check **Layer 3**, especially the default gateway and routing.

## Knowledge Check / Evidence of Learning

After completing the OSI Model lessons, I completed a harder 10-question quiz and scored **10/10**.

This demonstrated that I can:

- Match DNS, HTTP, and SSH to Layer 7.
- Identify TCP/UDP and port numbers as Layer 4 concepts.
- Identify IP addressing and routing as Layer 3 concepts.
- Identify MAC addresses and Ethernet frames as Layer 2 concepts.
- Recognise physical transmission as Layer 1.
- Explain encapsulation (L7 → L1) and decapsulation (L1 → L7).

This note covers OSI concepts only; the existing Module 03 lab-network configuration remains documented separately.
