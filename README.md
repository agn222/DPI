# Deep Packet Inspection (DPI) Engine

A high-performance Deep Packet Inspection (DPI) system written in C++ that analyzes network traffic from PCAP files, identifies applications using protocol inspection and TLS SNI extraction, applies filtering policies, and generates traffic statistics.

## Features

* Parse Ethernet, IPv4, TCP, and UDP packets
* Extract and analyze TLS Server Name Indication (SNI)
* Identify applications such as YouTube, Facebook, Google, GitHub, etc.
* Flow-based connection tracking using Five-Tuple identification
* Rule-based traffic filtering

  * Block by IP address
  * Block by application
  * Block by domain name
* Multi-threaded packet processing pipeline
* PCAP input and output support
* Traffic classification and reporting

---

## Architecture

### Processing Pipeline

```text
Input PCAP
    │
    ▼
Packet Reader
    │
    ▼
Packet Parser
    │
    ▼
Flow Tracking
    │
    ▼
Application Classification
    │
    ▼
Policy Engine
    │
 ┌──┴──┐
 ▼     ▼
Allow  Block
 │
 ▼
Output PCAP
```

### Multi-Threaded Architecture

```text
Reader Thread
      │
      ▼
Load Balancers
      │
      ▼
Fast Path Workers
      │
      ▼
Output Writer
```

The multi-threaded version distributes packets across worker threads using Five-Tuple hashing, ensuring packets belonging to the same connection are processed by the same worker.

---

## Technologies Used

* C++17
* STL Containers
* Multi-threading (`std::thread`)
* Mutexes and Condition Variables
* PCAP File Processing
* Network Protocol Parsing

---

## Supported Protocols

### Layer 2

* Ethernet

### Layer 3

* IPv4

### Layer 4

* TCP
* UDP

### Layer 7

* TLS Client Hello (SNI Extraction)
* HTTP Host Header Inspection

---

## Application Detection

Applications are identified using domain-based signatures extracted from TLS SNI or HTTP Host headers.

Examples:

| Domain Pattern | Application |
| -------------- | ----------- |
| youtube.com    | YouTube     |
| facebook.com   | Facebook    |
| google.com     | Google      |
| github.com     | GitHub      |

---

## Flow Tracking

Each connection is uniquely identified using the Five-Tuple:

```text
Source IP
Destination IP
Source Port
Destination Port
Protocol
```

This enables:

* Stateful connection tracking
* Flow-level policy enforcement
* Efficient traffic classification

---

## Blocking Rules

The engine supports three filtering methods:

### IP Blocking

```bash
--block-ip 192.168.1.50
```

### Application Blocking

```bash
--block-app YouTube
```

### Domain Blocking

```bash
--block-domain facebook
```

Blocked flows are dropped and excluded from the output capture.

---

## Project Structure

```text
packet_analyzer/
│
├── include/
│   ├── pcap_reader.h
│   ├── packet_parser.h
│   ├── sni_extractor.h
│   ├── types.h
│   ├── rule_manager.h
│   ├── connection_tracker.h
│   ├── load_balancer.h
│   ├── fast_path.h
│   ├── thread_safe_queue.h
│   └── dpi_engine.h
│
├── src/
│   ├── pcap_reader.cpp
│   ├── packet_parser.cpp
│   ├── sni_extractor.cpp
│   ├── types.cpp
│   ├── main_working.cpp
│   └── dpi_mt.cpp
│
├── generate_test_pcap.py
├── test_dpi.pcap
└── README.md
```

---

## Building

### Single-Threaded Version

```bash
g++ -std=c++17 -O2 -I include -o dpi_simple \
src/main_working.cpp \
src/pcap_reader.cpp \
src/packet_parser.cpp \
src/sni_extractor.cpp \
src/types.cpp
```

### Multi-Threaded Version

```bash
g++ -std=c++17 -pthread -O2 -I include -o dpi_engine \
src/dpi_mt.cpp \
src/pcap_reader.cpp \
src/packet_parser.cpp \
src/sni_extractor.cpp \
src/types.cpp
```

---

## Running

### Basic Usage

```bash
./dpi_engine input.pcap output.pcap
```

### Block Applications

```bash
./dpi_engine input.pcap output.pcap \
--block-app YouTube \
--block-app TikTok
```

### Block IP Addresses

```bash
./dpi_engine input.pcap output.pcap \
--block-ip 192.168.1.50
```

### Block Domains

```bash
./dpi_engine input.pcap output.pcap \
--block-domain facebook
```

### Configure Worker Threads

```bash
./dpi_engine input.pcap output.pcap \
--lbs 4 \
--fps 4
```

---

## Sample Output

```text
Total Packets: 77
Forwarded: 69
Dropped: 8

Detected Applications:
- HTTPS
- YouTube
- Facebook
- Google
- DNS

Detected Domains:
- www.youtube.com
- www.facebook.com
- github.com
```

---

## Key Concepts Demonstrated

* Deep Packet Inspection (DPI)
* Network Protocol Parsing
* TLS SNI Extraction
* Flow-Based Traffic Analysis
* Rule-Based Traffic Filtering
* Multi-Threaded System Design
* Producer-Consumer Architecture
* Concurrent Queue Implementation

---

## Future Enhancements

* QUIC / HTTP3 support
* Real-time packet capture
* Persistent rule configuration
* Traffic shaping and rate limiting
* Web-based monitoring dashboard
* Extended application signature database

---

## Learning Outcomes

This project demonstrates practical knowledge of:

* Computer Networks
* Operating Systems
* Concurrent Programming
* Network Security
* Packet Processing Systems
* High-Performance System Design
