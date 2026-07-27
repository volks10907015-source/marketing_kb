---
{"dg-publish":true,"permalink":"/1-server-knowledge-basic/1-1-server-overview/server-knowledge-chapter-01/","dg-note-properties":{}}
---

# Server Overview

From a hardware point of view, a **server is a computer designed to continuously provide computing, storage, networking, or application services to other systems**.

A server still uses the same fundamental components as a PC:

- CPU
- Memory
- Storage
- Network
- Power supply
- Cooling

However, server hardware is designed around very different priorities:

> **Performance + Capacity + Reliability + Serviceability + Scalability + Continuous Operation**

A simple server architecture can be viewed as:

```text
                 ┌─────────────────────┐
                 │       SERVER        │
                 │                     │
Network ───────► │ NIC                 │
                 │  │                  │
                 │  ▼                  │
                 │ CPU ───── Memory    │
                 │  │                  │
                 │  ├────── Storage    │
                 │  │                  │
                 │  └────── GPU / NIC  │
                 │                     │
                 │ BMC / BIOS          │
                 │ Power / Cooling     │
                 └─────────────────────┘
```

---

# 1. Server vs PC

A server and a PC are fundamentally both computers.

The difference is mainly:

> **What they are designed to do and how reliably they must do it.**

| | PC | Server |
|---|---|---|
| Main user | One user | Many users / systems |
| Operation | Intermittent | Often 24/7 |
| CPU | Consumer CPU | Server CPU |
| Memory | Standard DIMM | ECC RDIMM / MRDIMM |
| Storage | Few SSDs | Many SSDs / HDDs |
| Network | 1–10GbE | 25G–800G+ |
| Power | Usually single PSU | Redundant PSU |
| Management | Local | Remote BMC |
| Reliability | Normal | High |
| Expansion | Limited | High |
| Serviceability | Moderate | Designed for field replacement |

A PC is typically optimized for:

```text
Individual User Experience
```

A server is optimized for:

```text
Workload
+
Availability
+
Capacity
+
Manageability
```

---

# 2. Basic Server Hardware Architecture

A simplified server can be divided into several major subsystems:

```text
SERVER

├── Compute
│   ├── CPU
│   └── GPU / Accelerator
│
├── Memory
│   └── DRAM / DIMM
│
├── Storage
│   ├── SSD
│   └── HDD
│
├── I/O
│   └── PCIe
│
├── Networking
│   └── NIC / DPU
│
├── Power
│   ├── PSU
│   └── VRM
│
├── Cooling
│   ├── Fan
│   ├── Heat Sink
│   └── Liquid Cooling
│
├── Management
│   ├── BMC
│   ├── BIOS
│   └── Redfish
│
└── Mechanical
    ├── Chassis
    └── Rack
```

These subsystems work together as one system.

---

# 3. CPU — The Main Compute Engine

The CPU is responsible for executing general-purpose software.

Examples of server CPUs:

- [[Intel Xeon\|Intel Xeon]]
- [[AMD EPYC\|AMD EPYC]]
- [[NVIDIA Grace CPU\|NVIDIA Grace CPU]]

The CPU executes:

```text
Operating System
     ↓
Applications
     ↓
Instructions
     ↓
CPU
```

Typical server CPU responsibilities include:

- Application processing
- Operating system execution
- Virtual machines
- Database workloads
- Networking control
- Storage control
- GPU orchestration

---

# 4. CPU Socket

A server motherboard may contain:

```text
1 Socket
```

or:

```text
2 Sockets
```

Example:

```text
Dual-Socket Server

CPU 0                 CPU 1
  │                     │
  ├─ Memory             ├─ Memory
  │                     │
  ├─ PCIe               ├─ PCIe
  │                     │
  └──── CPU Link ───────┘
```

Each CPU may directly control:

- Memory channels
- PCIe lanes
- Accelerators
- NICs
- Storage devices

This creates:

> [[NUMA\|NUMA]]

---

# 5. Memory — Working Space for the CPU

Memory stores data that the CPU is actively using.

Typical server memory:

- [[DDR5\|DDR5]]
- [[RDIMM\|RDIMM]]
- [[MRDIMM\|MRDIMM]]
- [[ECC Memory\|ECC Memory]]

Architecture:

```text
CPU
 │
 ├── Memory Channel 0 ── DIMM
 ├── Memory Channel 1 ── DIMM
 ├── Memory Channel 2 ── DIMM
 └── ...
```

Server platforms normally provide significantly more:

- Memory capacity
- Memory bandwidth
- Memory channels

than consumer PCs.

---

# 6. Why Servers Use ECC Memory

Servers often use:

> **ECC — Error Correcting Code**

ECC can detect and correct certain memory errors.

Conceptually:

```text
Normal Memory

Data Error
   ↓
Wrong Data
```

versus:

```text
ECC Memory

Data Error
   ↓
Detect
   ↓
Correct / Report
```

This is important because servers may operate:

```text
24 hours × 365 days
```

and a single memory error could affect:

- Databases
- Virtual machines
- AI workloads
- Customer applications

---

# 7. Storage — Persistent Data

Memory loses its contents when power is removed.

Storage keeps data permanently.

Typical server storage includes:

- [[NVMe SSD\|NVMe SSD]]
- [[SATA SSD\|SATA SSD]]
- [[SAS SSD\|SAS SSD]]
- [[HDD\|HDD]]

Example:

```text
CPU
 │
 ▼
PCIe
 │
 ▼
NVMe SSD
```

Modern servers increasingly use:

> PCIe + NVMe

because NVMe offers high:

- IOPS
- Bandwidth
- Low latency

---

# 8. Server Storage Form Factors

Common storage form factors include:

```text
2.5-inch
U.2
E1.S
E3.S
M.2
```

For example:

```text
Front of Server

┌───┬───┬───┬───┬───┬───┐
│SSD│SSD│SSD│SSD│SSD│SSD│
└───┴───┴───┴───┴───┴───┘
```

Servers often provide:

> Hot-swappable drives

allowing failed storage devices to be replaced without shutting down the server.

---

# 9. PCIe — The Server's High-Speed I/O Backbone

PCIe connects the CPU to high-speed devices.

Examples:

```text
CPU
 │
 ├──── GPU
 │
 ├──── NIC
 │
 ├──── NVMe SSD
 │
 ├──── DPU
 │
 └──── FPGA
```

Typical server PCIe generations:

- [[PCIe Gen4\|PCIe Gen4]]
- [[PCIe Gen5\|PCIe Gen5]]
- [[PCIe Gen6\|PCIe Gen6]]

A key server design question is:

> How many PCIe lanes does the CPU provide, and how are those lanes allocated?

---

# 10. PCIe Topology

A server's PCIe topology has a major impact on performance.

Example:

```text
CPU
 │
 ├── PCIe x16 ── GPU
 │
 ├── PCIe x16 ── GPU
 │
 ├── PCIe x16 ── NIC
 │
 └── PCIe x4  ── SSD
```

If more devices are required than the CPU can directly support:

```text
CPU
 │
 ▼
PCIe Switch
 │
 ├── GPU
 ├── GPU
 ├── NIC
 └── SSD
```

may be used.

Related:

- [[PCIe Switch\|PCIe Switch]]
- [[PCIe Retimer\|PCIe Retimer]]
- [[PCIe Bifurcation\|PCIe Bifurcation]]

---

# 11. Networking — How Servers Communicate

Servers rarely operate independently.

They communicate with:

- Other servers
- Storage systems
- Users
- Internet
- AI clusters

through:

> [[NIC\|NIC]] — Network Interface Card

Example:

```text
Server
  │
 NIC
  │
  ▼
Switch
  │
  ├── Server
  ├── Server
  └── Server
```

Typical server network speeds include:

```text
1G
10G
25G
100G
200G
400G
800G
```

AI servers are rapidly moving toward:

> 400G / 800G networking

---

# 12. NIC vs DPU

A traditional NIC primarily handles network connectivity.

```text
CPU
 │
 ▼
NIC
 │
 ▼
Network
```

A DPU can perform additional infrastructure processing.

```text
CPU
 │
 ▼
DPU
 ├── Networking
 ├── Security
 ├── Storage
 └── Infrastructure Services
```

Example:

- [[NVIDIA BlueField\|NVIDIA BlueField]]

The purpose is to:

> Offload infrastructure workloads from the CPU.

---

# 13. GPU and Accelerators

Traditional servers mainly depend on CPU compute.

AI and HPC servers add accelerators such as:

- GPU
- FPGA
- AI ASIC

Example:

```text
CPU
 │
 ├── GPU 0
 ├── GPU 1
 ├── GPU 2
 └── GPU 3
```

Modern AI servers may contain:

```text
8 GPUs
```

or rack-scale systems containing:

```text
72 GPUs
```

Examples:

- [[NVIDIA HGX\|NVIDIA HGX]]
- [[NVIDIA GB200 NVL72\|NVIDIA GB200 NVL72]]
- [[NVIDIA GB300 NVL72\|NVIDIA GB300 NVL72]]

---

# 14. CPU vs GPU

CPU:

```text
General-purpose processing
Control
Operating system
Serial / mixed workloads
```

GPU:

```text
Massively parallel processing
AI
Matrix operations
HPC
```

A simplified AI server:

```text
          CPU
           │
     Control / Host
           │
           ▼
        PCIe / C2C
           │
           ▼
          GPU
           │
      AI Compute
```

---

# 15. Power Supply

A server requires stable power.

Typical architecture:

```text
AC Power
   ↓
PSU
   ↓
12V / 48V
   ↓
Motherboard
   ↓
VRM
   ↓
CPU / GPU / Memory
```

Server PSUs commonly use redundancy.

Example:

```text
PSU 0 ──┐
        ├── Server
PSU 1 ──┘
```

If one PSU fails:

```text
PSU 0 FAIL
PSU 1 CONTINUES
```

The server can continue operating.

This is called:

> **Power Redundancy**

---

# 16. Power Budget

Server designers must calculate the total system power requirement.

Example:

```text
CPU          350 W
GPU × 8    5,600 W
Memory       400 W
NIC          150 W
Storage      150 W
Fans         500 W
Others       200 W
------------------
Total      7,350 W
```

The PSU design must support:

- Normal workload
- Peak workload
- Transient power
- Redundancy

Related:

- [[TDP\|TDP]]
- [[Power Budget\|Power Budget]]
- [[Power Capping\|Power Capping]]
- [[EDP\|EDP]]

---

# 17. Cooling

All electrical power eventually becomes heat.

Therefore:

```text
Higher Compute
      ↓
Higher Power
      ↓
More Heat
      ↓
More Cooling
```

Traditional server cooling:

```text
Cold Air
   ↓
Fans
   ↓
CPU Heat Sink
   ↓
Hot Air
```

Modern high-power systems increasingly use:

> [[Direct Liquid Cooling\|Direct Liquid Cooling]]

---

# 18. Air Cooling

Air-cooled server:

```text
Front
 │
 ▼
Cold Air

[SSD] [DIMM] [CPU] [GPU]

                 │
                 ▼
               Fans
                 │
                 ▼
              Hot Air

Rear
```

Server layout must consider:

- Airflow
- Component placement
- Pressure drop
- Fan power
- Heat sink design

---

# 19. Liquid Cooling

As CPU and GPU power increases, air cooling becomes more difficult.

Liquid cooling architecture:

```text
CPU / GPU
    │
    ▼
Cold Plate
    │
    ▼
Coolant
    │
    ▼
Manifold
    │
    ▼
CDU
```

Liquid cooling can remove significantly more heat from high-density systems.

Related:

- [[Cold Plate\|Cold Plate]]
- [[CDU\|CDU]]
- [[Manifold\|Manifold]]
- [[Direct Liquid Cooling\|Direct Liquid Cooling]]

---

# 20. BMC — Server Management Controller

One major difference between a server and a PC is:

> Servers have an independent management controller.

This is typically:

> [[BMC\|BMC]] — Baseboard Management Controller

The BMC can operate even when the main CPU is powered off.

```text
              BMC
               │
       ┌───────┼────────┐
       │       │        │
      PSU     Fan    Sensors
       │       │        │
       └───────┼────────┘
               │
              CPU
```

---

# 21. What Can BMC Do?

BMC can:

- Monitor temperature
- Monitor power
- Monitor fans
- Check hardware status
- Read system logs
- Turn server on/off
- Update firmware
- Provide remote console
- Monitor PSU status
- Monitor CPU/GPU status

This allows administrators to manage servers remotely.

---

# 22. Redfish

[[Redfish\|Redfish]] is a standard API used to manage server hardware.

A management system can use Redfish to:

```text
Management Software
       │
       ▼
    Redfish API
       │
       ▼
      BMC
       │
       ├── CPU
       ├── Memory
       ├── Fan
       ├── PSU
       └── Sensors
```

Typical operations:

- Read hardware information
- Monitor system status
- Check temperatures
- Check power
- Control server power
- Configure server settings

---

# 23. BIOS

[[BIOS\|BIOS]] / [[UEFI\|UEFI]] initializes server hardware before the operating system starts.

Boot sequence:

```text
Power On
   ↓
BMC
   ↓
BIOS / UEFI
   ↓
Hardware Initialization
   ↓
POST
   ↓
Boot Device
   ↓
Operating System
```

BIOS configuration can control:

- CPU settings
- Memory settings
- PCIe configuration
- Power policy
- NUMA
- Boot order

---

# 24. Server Chassis

Server hardware must fit into standardized mechanical systems.

The most common form factor is:

> Rack-mounted server

Examples:

```text
1U
2U
4U
8U
```

One Rack Unit:

```text
1U = 44.45 mm
```

Example:

```text
Rack

┌─────────────────┐
│ 1U Server       │
├─────────────────┤
│ 2U Server       │
│                 │
├─────────────────┤
│ 4U GPU Server   │
│                 │
│                 │
│                 │
└─────────────────┘
```

---

# 25. Why Different Server Sizes Exist

More chassis space allows more:

- GPUs
- Storage
- PCIe cards
- Cooling
- Power
- Memory

Example:

```text
1U
→ Compute density

2U
→ Balanced expansion

4U+
→ GPU / Storage / High-power system
```

But larger systems consume more rack space.

Therefore server design involves trade-offs between:

> Density vs Expandability vs Cooling

---

# 26. Server Motherboard

The server motherboard connects all major components.

```text
Motherboard

CPU
 │
 ├── DIMM
 │
 ├── PCIe
 │    ├── GPU
 │    ├── NIC
 │    └── SSD
 │
 ├── BMC
 │
 └── Chipset / I/O
```

The motherboard must support:

- High-speed signaling
- Power distribution
- Firmware
- Management
- Mechanical constraints
- Thermal constraints

---

# 27. Server PCB Complexity

Server PCBs are significantly more complex than typical PC motherboards.

They may contain:

```text
20+ PCB layers
```

or considerably more depending on architecture.

The PCB must carry high-speed interfaces such as:

- PCIe Gen5 / Gen6
- DDR5
- 400G / 800G networking
- NVLink
- CXL

Important concepts include:

- [[Signal Integrity\|Signal Integrity]]
- [[Power Integrity\|Power Integrity]]
- [[Differential Pair\|Differential Pair]]
- [[Insertion Loss\|Insertion Loss]]
- [[Return Loss\|Return Loss]]
- [[Crosstalk\|Crosstalk]]

---

# 28. Reliability

Servers are designed to continue operating despite component failures.

Examples:

```text
Dual PSU
ECC Memory
Hot-swap SSD
Redundant Fan
RAID
```

This philosophy is called:

> [[RAS\|RAS]]

RAS means:

- Reliability
- Availability
- Serviceability

---

# 29. Serviceability

A server should be easy to repair.

Example:

```text
Failed PSU
   ↓
Pull PSU
   ↓
Insert New PSU
   ↓
Server Continues Running
```

Components commonly designed for easy replacement include:

- PSU
- Fan
- SSD
- NIC
- GPU
- DIMM

---

# 30. Hot Swap

Hot-swappable components can be replaced while the system remains operational.

Common examples:

- PSU
- SSD
- Fan

Example:

```text
Server Running
     │
     ├── PSU 0
     └── PSU 1

Remove PSU 0
     ↓
PSU 1 continues powering server
```

This reduces downtime.

---

# 31. Server as a System

The most important concept is:

> A server is not simply a CPU + memory + storage.

Every component affects other components.

Example:

```text
More GPUs
   ↓
More PCIe Lanes Required
   ↓
Larger CPU / PCIe Topology
   ↓
More Power
   ↓
More Heat
   ↓
More Cooling
   ↓
Larger Chassis
   ↓
Higher Rack Power
```

Therefore server design is:

> **System Engineering**

---

# 32. Example: Basic Enterprise Server

```text
2U Server

CPU
├── 2 × Intel Xeon / AMD EPYC
│
Memory
├── 16–32 DIMMs
│
Storage
├── 8–24 SSDs
│
Network
├── 2 × NIC
│
Power
├── 2 × PSU
│
Cooling
└── Redundant Fans
```

Typical workloads:

- Database
- Virtual machines
- Enterprise applications
- Cloud workloads

---

# 33. Example: AI Server

An AI server looks very different.

```text
4U / 8U AI Server

CPU
├── 2 × Server CPU
│
GPU
├── 8 × GPU
│
Memory
├── System DRAM
│
GPU Memory
├── HBM
│
Networking
├── 400G / 800G NIC
│
Storage
├── NVMe SSD
│
Cooling
├── DLC
│
Power
└── Multi-kW PSU
```

The dominant design constraints become:

```text
GPU
 ↓
Power
 ↓
Cooling
 ↓
Networking
```

---

# 34. Example: Storage Server

Storage servers emphasize:

```text
Large Drive Count
       +
High Storage Capacity
       +
High I/O Throughput
```

Example:

```text
Storage Server

CPU
 │
PCIe / SAS
 │
 ├── SSD
 ├── SSD
 ├── HDD
 ├── HDD
 └── HDD
```

The system may prioritize:

- Drive capacity
- IOPS
- RAID
- NVMe
- Network bandwidth

---

# 35. Example: Edge Server

Edge servers usually prioritize:

- Compact size
- Low power
- Environmental tolerance
- Low latency
- Telco networking

Example:

```text
Edge Server
   │
   ├── CPU
   ├── GPU / Accelerator
   ├── Telco NIC
   └── Local Storage
```

Typical use cases:

- 5G
- AI inference
- Retail
- Industrial automation

---

# 36. Server Hardware Design Trade-offs

Every server architecture is a trade-off.

## Performance vs Power

```text
More Performance
      ↓
More Power
```

---

## Density vs Cooling

```text
More Components
      ↓
Higher Density
      ↓
Harder Cooling
```

---

## Capacity vs Cost

```text
More Memory / Storage
        ↓
Higher Cost
```

---

## Flexibility vs Complexity

```text
More PCIe Options
       ↓
More PCB / Mechanical Complexity
```

---

# 37. The Most Important Server Hardware Questions

When evaluating a server, ask:

## Compute

- What CPU?
- How many sockets?
- How many cores?
- What accelerators?

## Memory

- How much memory?
- How many channels?
- What DIMM type?
- What bandwidth?

## I/O

- How many PCIe lanes?
- Which PCIe generation?
- Is a PCIe switch required?

## Storage

- How many drives?
- NVMe or SATA/SAS?
- What form factor?

## Networking

- 100G?
- 400G?
- 800G?
- Ethernet or InfiniBand?

## Power

- Maximum system power?
- PSU capacity?
- Redundancy?

## Thermal

- Air cooling?
- Liquid cooling?
- Maximum CPU/GPU TDP?

## Mechanical

- 1U?
- 2U?
- 4U?
- Rack compatibility?

---

# 38. Server Hardware Mental Model

The easiest way to understand server hardware is:

```text
                    WORKLOAD
                       │
                       ▼
                    COMPUTE
                  CPU / GPU
                       │
           ┌───────────┼───────────┐
           ▼           ▼           ▼
        MEMORY       STORAGE     NETWORK
           │           │           │
           └───────────┼───────────┘
                       ▼
                      I/O
                     PCIe
                       │
                       ▼
              POWER + COOLING
                       │
                       ▼
                  MECHANICAL
                       │
                       ▼
                SERVER SYSTEM
```

Start with:

> **What workload does the server need to run?**

Then determine:

> What hardware architecture is required to support that workload?

---

# 39. Product Manager View

For a Server Product Manager, do not start by asking:

> Which CPU should we use?

Start with:

```text
Customer Workload
       ↓
Performance Requirement
       ↓
CPU / GPU Requirement
       ↓
Memory Requirement
       ↓
Storage / Network Requirement
       ↓
PCIe Architecture
       ↓
Power Requirement
       ↓
Cooling Requirement
       ↓
Mechanical Design
       ↓
Cost
       ↓
Product
```

The workload drives the architecture.

---

# 40. One-Minute Summary

A server is fundamentally:

```text
Compute
+
Memory
+
Storage
+
Networking
+
I/O
+
Power
+
Cooling
+
Management
+
Mechanical
```

The major hardware components are:

```text
CPU
DIMM
SSD
NIC
GPU
PSU
Fan
Motherboard
BMC
Chassis
```

The key difference from a PC is that servers are designed for:

> **24/7 operation, high performance, high capacity, remote management, reliability, scalability, and serviceability.**

And the most important concept is:

> **A server is a system, not a collection of independent components.**

Changing one component usually affects the rest of the architecture.

---

# 41. Knowledge Check

## Q1. What are the main hardware components in a server?

CPU, memory, storage, networking, I/O, power, cooling, management, and mechanical components.

---

## Q2. What connects GPUs, NICs, and NVMe SSDs to the CPU?

**PCIe**

---

## Q3. Why do servers use ECC memory?

To detect and correct certain memory errors and improve system reliability.

---

## Q4. What is BMC used for?

Remote server monitoring and management.

---

## Q5. What is Redfish?

A standard API used to remotely monitor and manage server hardware.

---

## Q6. Why do servers often have two PSUs?

For power redundancy.

---

## Q7. Why is cooling becoming more important?

CPU and GPU power density continues to increase.

---

## Q8. What should determine server architecture first?

The target workload and system requirements.

---

# 42. Related Knowledge

## Fundamentals

- [[Server Architecture\|Server Architecture]]
- [[Server Form Factor\|Server Form Factor]]
- [[Rack Architecture\|Rack Architecture]]
- [[1U vs 2U vs 4U Server\|1U vs 2U vs 4U Server]]
- [[Scale-Up vs Scale-Out\|Scale-Up vs Scale-Out]]

## Compute

- [[CPU\|CPU]]
- [[GPU\|GPU]]
- [[Intel Xeon\|Intel Xeon]]
- [[AMD EPYC\|AMD EPYC]]
- [[NVIDIA Grace CPU\|NVIDIA Grace CPU]]
- [[RISC vs CISC\|RISC vs CISC]]
- [[NUMA\|NUMA]]

## Memory

- [[DDR5\|DDR5]]
- [[RDIMM\|RDIMM]]
- [[MRDIMM\|MRDIMM]]
- [[ECC Memory\|ECC Memory]]
- [[Memory Bandwidth\|Memory Bandwidth]]

## I/O

- [[PCIe\|PCIe]]
- [[PCIe Gen5\|PCIe Gen5]]
- [[PCIe Gen6\|PCIe Gen6]]
- [[PCIe Switch\|PCIe Switch]]
- [[PCIe Retimer\|PCIe Retimer]]

## Storage

- [[NVMe\|NVMe]]
- [[NVMe SSD\|NVMe SSD]]
- [[E1.S\|E1.S]]
- [[E3.S\|E3.S]]
- [[RAID\|RAID]]

## Networking

- [[NIC\|NIC]]
- [[DPU\|DPU]]
- [[Ethernet\|Ethernet]]
- [[InfiniBand\|InfiniBand]]
- [[ConnectX\|ConnectX]]
- [[BlueField\|BlueField]]

## Power & Thermal

- [[PSU\|PSU]]
- [[VRM\|VRM]]
- [[Power Budget\|Power Budget]]
- [[TDP\|TDP]]
- [[Air Cooling\|Air Cooling]]
- [[Direct Liquid Cooling\|Direct Liquid Cooling]]
- [[Cold Plate\|Cold Plate]]
- [[CDU\|CDU]]

## Management

- [[BMC\|BMC]]
- [[BIOS\|BIOS]]
- [[Redfish\|Redfish]]
- [[IPMI\|IPMI]]
- [[OpenBMC\|OpenBMC]]

## Hardware Design

- [[Server Motherboard\|Server Motherboard]]
- [[PCB Stackup\|PCB Stackup]]
- [[Signal Integrity\|Signal Integrity]]
- [[Power Integrity\|Power Integrity]]
- [[Differential Pair\|Differential Pair]]

## Reliability

- [[RAS\|RAS]]
- [[Hot Swap\|Hot Swap]]
- [[Redundant PSU\|Redundant PSU]]
- [[Hardware Validation\|Hardware Validation]]