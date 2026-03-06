# Deep Packet Inspection (DPI) Engine

A **high-performance Deep Packet Inspection (DPI) system implemented in C++** that analyzes network packet captures (PCAP files), reconstructs connections using five-tuple flow identification, extracts application metadata such as TLS Server Name Indication (SNI), and applies rule-based filtering to simulate real-world network traffic control.

This project demonstrates core networking and systems concepts including **protocol parsing, connection tracking, multithreading, and rule-based packet filtering**.

---

# Overview

Deep Packet Inspection (DPI) is a technique used by network devices to inspect packet contents as they pass through a network checkpoint. Unlike traditional firewalls that rely only on IP addresses and ports, DPI analyzes packet payloads to identify applications and enforce policies.

This engine processes captured network traffic and performs the following tasks:

- Parse packets from PCAP files
- Reconstruct network flows using five-tuple identification
- Extract domain names from TLS and HTTP traffic
- Classify traffic into application categories
- Apply blocking rules based on IP, domain, or application
- Generate a detailed processing report

The system simulates how **enterprise firewalls, ISPs, and network monitoring tools inspect and control network traffic**.

---

# Features

### Packet Parsing
Reads raw packet data from PCAP files and extracts protocol headers including:

- Ethernet
- IPv4
- TCP / UDP

### Flow Reconstruction
Packets are grouped into connections using the **five-tuple flow identifier**:

- Source IP
- Destination IP
- Source Port
- Destination Port
- Protocol

This allows the system to track communication sessions across multiple packets.

### TLS SNI Inspection
Even though HTTPS traffic is encrypted, the **Server Name Indication (SNI)** field in the TLS Client Hello message reveals the domain being accessed.

The DPI engine extracts this value to identify applications such as:

- YouTube
- Facebook
- Google
- GitHub
- Netflix

### Rule-Based Traffic Control

The system supports several blocking mechanisms:

| Rule Type | Example | Effect |
|----------|--------|--------|
| IP Blocking | `192.168.1.50` | Blocks all traffic from source |
| Application Blocking | `YouTube` | Blocks detected application |
| Domain Blocking | `facebook` | Blocks domains containing keyword |

### Multi-threaded Processing

The advanced version includes a **high-performance packet processing pipeline** with:

- Reader thread
- Load balancer threads
- Fast path worker threads
- Output writer thread

This architecture enables efficient processing of large PCAP files.

---

# Networking Background

Understanding the project requires basic knowledge of the **network protocol stack**.

| Layer | Protocol Examples |
|------|------------------|
| Application | HTTP, TLS, DNS |
| Transport | TCP, UDP |
| Network | IP |
| Data Link | Ethernet |

Each network packet contains nested protocol headers:

---

# The Five-Tuple Concept

A network connection is uniquely identified by five fields:

| Field | Example |
|------|---------|
| Source IP | 192.168.1.100 |
| Destination IP | 142.250.185.206 |
| Source Port | 54321 |
| Destination Port | 443 |
| Protocol | TCP |

All packets sharing this tuple belong to the same communication session.  
The DPI engine uses this identifier to maintain **stateful flow tracking**.

---

# TLS SNI Extraction

When a client initiates an HTTPS connection, it sends a **TLS Client Hello** message before encryption begins.

This message includes the **Server Name Indication (SNI)** extension containing the domain name.

Example TLS structure:

The DPI engine parses this handshake message to extract the domain name and classify application traffic.

---

# Project Architecture

The system processes packets through multiple stages.

### Simple Version

A **single-threaded implementation** processes packets sequentially and is useful for learning and debugging.

### Multi-threaded Version

A **parallel pipeline architecture** improves performance for large traffic captures.

Packets are distributed using **consistent hashing** so that packets from the same connection are processed by the same worker thread.

---

# Repository Structure
packet_analyzer/

include/
pcap_reader.h
packet_parser.h
sni_extractor.h
rule_manager.h
connection_tracker.h
thread_safe_queue.h
dpi_engine.h

src/
main_working.cpp
dpi_mt.cpp
pcap_reader.cpp
packet_parser.cpp
sni_extractor.cpp
types.cpp

generate_test_pcap.py
test_dpi.pcap
README.md

---

# Building the Project

The project requires a **C++17 compatible compiler**.

### Build the simple version
g++ -std=c++17 -O2 -I include -o dpi_simple
src/main_working.cpp
src/pcap_reader.cpp
src/packet_parser.cpp
src/sni_extractor.cpp
src/types.cpp
### Build the multi-threaded engine


g++ -std=c++17 -pthread -O2 -I include -o dpi_engine
src/dpi_mt.cpp
src/pcap_reader.cpp
src/packet_parser.cpp
src/sni_extractor.cpp
src/types.cpp

The generated PCAP includes:

- TLS connections with SNI
- HTTP requests
- DNS queries
- traffic from blocked IPs

---

# Example Output
Total Packets: 77
TCP Packets: 73
UDP Packets: 4

Forwarded: 69
Dropped: 8

Application Breakdown
HTTPS 50.6%
DNS 5.2%
YouTube 5.2% (Blocked)
Facebook 3.9%


Detected domains:


www.youtube.com

www.facebook.com

github.com
www.google.com


---

# Real World Applications

Deep Packet Inspection is widely used in:

- Enterprise firewalls
- ISP traffic management
- Parental control systems
- Network monitoring platforms
- Intrusion detection systems

The techniques used in this project are similar to those implemented in tools such as **Snort, Suricata, and enterprise network security appliances**.

---

# Future Improvements

Potential extensions include:

- QUIC / HTTP3 traffic inspection
- Bandwidth throttling instead of packet dropping
- Real-time monitoring dashboard
- Additional application signatures
- Persistent rule configuration

---

# Technologies Used

- C++
- Multithreading
- Network protocol parsing
- PCAP file processing
- Producer-consumer concurrency pattern

---

# Summary

This project demonstrates how a **Deep Packet Inspection engine** can analyze network traffic, identify applications, and enforce network policies using packet-level inspection techniques.

Key concepts implemented include:

- Network protocol parsing
- Flow tracking using five-tuple identification
- TLS handshake inspection
- Multi-threaded packet processing
- Rule-based traffic filtering

The system provides a practical implementation of DPI concepts commonly used in **network security and traffic analysis systems**.
