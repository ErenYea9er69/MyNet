# MyNet 🚀

A powerful Windows networking tool that gives you control over your local network. Discover connected devices and manage their bandwidth (throttle or block) using advanced Layer-2 manipulation.

> [!IMPORTANT]
> **LEGAL DISCLAIMER:** Use this tool strictly on networks you own or have explicit permission to test. Unauthorized ARP spoofing is illegal in many regions. This project is for educational and network management research. **(if it's the programe hash it's for educatinal purpose)**

---

## ⚡ Quick Start

Get up and running in 3 simple steps:

1.  **Install Npcap:** Download and install [Npcap](https://npcap.com/#download). 
    *   *Crucial:* During installation, make sure to check **"Install Npcap in WinPcap API-compatible mode"**.
2.  **Clone the Repo:**
    ```bash
    git clone https://github.com/ErenYea9er69/MyNet.git
    cd MyNet
    ```
3.  **Build & Run:**
    Open `MyNet.csproj` in Visual Studio 2022 (Run as Administrator) and press **F5**.
    *Alternatively, via CLI (Admin Terminal):*
    ```bash
    dotnet restore
    dotnet build -c Release
    dotnet run
    ```

---

## 🛠️ Requirements

| Requirement | Notes |
| :--- | :--- |
| **OS** | Windows 10 or 11 (x64) |
| **Runtime** | [.NET 8 SDK](https://dotnet.microsoft.com/download) |
| **Driver** | [Npcap](https://npcap.com/#download) (WinPcap compatibility mode required) |
| **Privileges** | Must **Run as Administrator** to access raw network sockets |

---

## 📖 How to Use

1.  **Select Adapter:** Choose your network interface (Wi-Fi or Ethernet) from the dropdown.
2.  **Scan:** Click the Scan button to discover all devices on your current subnet.
3.  **Control:** Select a device from the list to:
    *   **Block:** Completely cut off its internet access (Parasite Evasion).
    *   **Throttle:** Use the sliders to limit Upload/Download speeds.
    *   > [!IMPORTANT]
    *   > **Scan Required:** You must wait to see the network scan is **finished** on the log output before you can block a device. Blocking is disabled during an active scan to ensure the ARP table is stable and all devices are correctly identified.
4.  **Restore:** Click "Restore All" to stop spoofing and return the network to its normal state.

---

## ⚖️ License & Copyright

**FREE TO COPY & USE**
This project is provided as-is, with no rights reserved. You are free to:
*   ✅ Copy and redistribute the code.
*   ✅ Modify and build upon it.
*   ✅ Use it for any personal or educational purpose.
*   **"Free to copywrite"** — This software is in the public domain.

---

## 🔬 In-Depth Technical Operation (No Hold Back)

This tool executes a sophisticated **Layer-2 MITM (Man-in-the-Middle)** attack known as **Asymmetric IP Hijacking** or **The Parasite Evasion**.

### 1. The Parasite Evasion (Bypassing Router IDS)
Modern routers often have Intrusion Detection Systems (IDS) that ban any MAC address claiming to be the Gateway. To bypass this:
*   **We NEVER spoof the Gateway IP directly.**
*   Instead, we "legally" claim the **Victim's IP** at our own fully-authenticated MAC address.
*   By sending targeted ARP Requests to the Router stating "The Victim IP is at My PC MAC", the Router updates its routing table. 
*   Because our MAC is legitimate and authenticated by the AP, the hardware firewall mathematically cannot ban us. All download traffic for the Victim is natively routed to our PC.

### 2. NDIS Silencing (Packet Vaporization)
Once the traffic is routed to our PC, we use the **NDIS Silencer** concept:
*   We use **Npcap + SharpPcap** to open the network interface in **Promiscuous Mode**.
*   We intercept the raw Ethernet frames before they reach the Windows TCP/IP stack.
*   If a device is marked as **BLOCKED**, we simply discard (vaporize) the packets. The victim's TCP streams timeout, resulting in a flawless, permanent disconnect that looks like a dead connection.

### 3. Asymmetric Poisoning
The attack is asymmetric:
*   **Router-Side:** We tell the Router that we are the Victim.
*   **Victim-Side:** We tell the Victim that we are the Router.
*   We re-poison the cache every **250ms - 2000ms** to override the Data-Plane learning of modern switches and routers.

### 4. Network Discovery (Subnet Mapping)
The scanner works by broadcasting ARP Requests for every possible IP in your subnet (e.g., `192.168.1.1` to `192.168.1.254`). Any device that replies is added to the SQLite-backed device list, complete with its MAC vendor lookup (OUI).

---

### 🌟 Credits
Inspired by the original SelfishNet utility. MyNet is a completely modernized, re-engineered for .NET 8 version with asynchronous patterns to handle high-frequency packet injection without UI lag.