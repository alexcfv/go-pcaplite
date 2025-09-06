# 🕵️‍♂️ go-pcaplite – Lightweight Network Sniffer Go lib

[![Go Reference](https://pkg.go.dev/badge/github.com/alexcfv/go-pcaplite.svg)](https://pkg.go.dev/github.com/alexcfv/go-pcaplite)
[![Go Report Card](https://goreportcard.com/badge/github.com/alexcfv/go-pcaplite)](https://goreportcard.com/report/github.com/alexcfv/go-pcaplite)
[![codecov](https://codecov.io/github/alexcfv/go-pcaplite/graph/badge.svg?token=ZHZMTJI4D7)](https://codecov.io/github/alexcfv/go-pcaplite)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![Platform](https://img.shields.io/badge/platform-linux%20%7C%20macos%20%7C%20windows-blue)
![Go Version](https://img.shields.io/github/go-mod/go-version/alexcfv/go-pcaplite)

---

## 🚀 Overview

`go-pcaplite` is a **lightweight Go library** for capturing and inspecting network traffic in real time.  
It wraps `gopacket` and simplifies packet sniffing with an easy-to-use API.  

---

## 🔥 Features

- 📡 **Live packet capture** from any interface  
- 🔍 Supports **BPF filters** (tcp, udp, icmp, arp, etc.)  
- 📝 Extracts **protocol metadata** (DNS, ARP, etc.)  
- 🖥️ Cross-platform: Linux, macOS, Windows  
- ⚡ Designed for simplicity and integration into other tools  

---

## 🛠️ Installation

```bash
go get github.com/alexcfv/go-pcaplite
```

---

## 🔑 Running on Different Operating Systems

| OS          | How to run                                                            |
| ----------- | --------------------------------------------------------------------- |
| **Linux**   | `sudo go run main.go`                                                 |
| **macOS**   | `sudo go run main.go` (or allow permissions in Security settings)     |
| **Windows** | Run as Administrator                                                  |

---

## 🌐 Common Network Interfaces

| OS          | Typical Interfaces                               |
| ----------- | ------------------------------------------------ |
| **Linux**   | `eth0`, `wlan0`, `lo`, `enp3s0`, `docker0`       |
| **macOS**   | `en0`, `en1`, `lo0`, `bridge0`, `utun0`          |
| **Windows** | `Ethernet`, `Wi-Fi`, `Loopback Pseudo-Interface` |

---

## 🔍 Example Filters (BPF Syntax)

| Filter                | Description                    |
| --------------------- | ------------------------------ |
| `tcp`                 | Capture only TCP packets       |
| `udp`                 | Capture only UDP packets       |
| `icmp`                | Capture ICMP (ping) traffic    |
| `arp`                 | Capture ARP requests/responses |
| `tcp port 443`        | Capture HTTPS traffic          |
| `udp or icmp`         | Capture UDP + ICMP packets     |
| `tcp and dst port 22` | Capture packets going to SSH   |

---

## 📦 Example

```golang
package main

import (
    "fmt"
    "log"
    "github.com/alexcfv/go-pcaplite"
)

func main() {
    opts := pcaplite.CaptureOptions{
        Filter:  "tcp port 443 or udp or arp or icmp", // HTTPS + other protocols
        Promisc: true,
    }

    packets, err := pcaplite.Capture("en0", opts) //en0 macOS interface
    if err != nil {
        log.Fatal(err)
    }

    for p := range packets {
        fmt.Printf("[%s] %s:%s -> %s:%s | %s | %d bytes\n",
            p.Timestamp.Format("15:04:05"),
            p.SrcIP, p.SrcPort,
            p.DstIP, p.DstPort,
            p.Protocol, p.Length,
        )

        // Print additional metadata (DNS, ARP, etc.)
        for k, v := range p.Extra {
            fmt.Printf("  %s: %s\n", k, v)
        }
    }
}
```

---

## 📦 Output:

```bash
[16:05:29] 192.168.0.30:57621 -> 192.168.0.255:57621 | UDP | 86 bytes
[16:05:29] 2a06:63c1:110a:6c00:e433:15e:935f:6291:52189 -> 2603:1061:10::16:443 | TCP | 74 bytes
[16:05:29] 2603:1061:10::16:443 -> 2a06:63c1:110a:6c00:e433:15e:935f:6291:52189 | TCP | 74 bytes
[16:05:29] 2a06:63c1:110a:6c00:e433:15e:935f:6291:53309 -> 2a00:e90:0:3:3:3:3:3:53 | DNS | 115 bytes
  DNS_Query: smoot-searchv2-aeun1a.v.aaplimg.com
[16:05:29] 2a06:63c1:110a:6c00:e433:15e:935f:6291:60810 -> 2a00:e90:0:3:3:3:3:3:53 | DNS | 115 bytes
  DNS_Query: smoot-searchv2-aeun1a.v.aaplimg.com
[16:05:29] 2a06:63c1:110a:6c00:e433:15e:935f:6291:61161 -> 2a00:e90:0:3:3:3:3:3:53 | DNS | 115 bytes
  DNS_Query: smoot-searchv2-aeun1a.v.aaplimg.com
[16:05:29] 2a00:e90:0:3:3:3:3:3:53 -> 2a06:63c1:110a:6c00:e433:15e:935f:6291:53309 | DNS | 189 bytes
  DNS_Query: smoot-searchv2-aeun1a.v.aaplimg.com
[16:05:29] 2a00:e90:0:3:3:3:3:3:53 -> 2a06:63c1:110a:6c00:e433:15e:935f:6291:60810 | DNS | 189 bytes
  DNS_Query: smoot-searchv2-aeun1a.v.aaplimg.com
[16:05:29] 2a00:e90:0:3:3:3:3:3:53 -> 2a06:63c1:110a:6c00:e433:15e:935f:6291:61161 | DNS | 131 bytes
  DNS_Query: smoot-searchv2-aeun1a.v.aaplimg.com
[16:05:30] 192.168.0.30:50590 -> 16.170.124.74:443 | TCP | 78 bytes
[16:05:30] 16.170.124.74:443 -> 192.168.0.30:50590 | TCP | 74 bytes
[16:05:30] 192.168.0.30:50590 -> 16.170.124.74:443 | TCP | 583 bytes
  TLS_SNI: api-glb-aeun1a.smoot.apple.com
```

---

## ⚙️ Packet structure:

```golang
type Packet struct {
    Timestamp   time.Time          // The exact time when the packet was captured
    SrcIP       string             // Source IP address of the packet
    DstIP       string             // Destination IP address of the packet
    SrcMAC      string             // Source MAC address of the packet
    DstMAC      string             // Destination MAC address of the packet
    Protocol    string             // Network protocol used (e.g., TCP, UDP, ICMP)
    SrcPort     string             // Source port number (if applicable, e.g., TCP/UDP)
    DstPort     string             // Destination port number (if applicable, e.g., TCP/UDP)
    Length      int                // Total length of the entire packet in bytes
    PayloadSize int                // Size of the actual payload (data) in bytes
    Extra       map[string]string  // Additional parsed information or metadata
}
```

```golang
Extra{
    "DNS_Query" : DNS,
    "ARP_SourceIP" : SRCARP,
    "ARP_DestIP" : DESTARP,
    "TLS_SNI" : SNI
}
```

---

## ✍️ From the Author

Hi! I’m the author of **go-pcaplite**.  

I also have a **CLI utility** for deeper traffic analysis.  
You can check it out here: [CLI sniffer](https://github.com/alexcfv/go-sniffer)

---

## 📜 License  
MIT © 2025 [alexcfv](https://github.com/alexcfv)
