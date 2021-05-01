# Cyber-Physical Systems Security

--8<-- "includes/abbr.md"

Cyber-physical systems (CPS) integrate computing (processes, systems) and the physical world (processes, environments). Consequences can be more dire than in cyberspace: there's no "rollback" in the real world.

## Textbooks

- Industrial Network Security, Second Edition: Securing Critical Infrastructure Networks for Smart Grid, SCADA, and Other Industrial Control Systems (2nd Edition), by Eric D. Knapp and Joel Thomas Langill, ISBN: 978-0124201149
- Applied Cyber Security and the Smart Grid: Implementing Security Controls into the Modern Power Infrastructure (1st Edition), by Eric D. Knapp and Raj Samani, ISBN: 978-1597499989
- Hacking Exposed Industrial Control Systems: ICS and SCADA Security Secrets & Solutions (1st Edition), by Clint Bodungen, Bryan Singer, Aaron Shbeeb, Kyle Wilhoit, and Stephen Hilt, ISBN: 978-1259589713

## Security Goals

- **Assurance**: how trust is provided and managed
- **Authentication**: ability to determine that something is genuine
- **Authorization**: ensuring necessary permissions for an action
- **Availability**: accessibility of information in a timely manner
- **Integrity**: ensuring that information has not been altered
- **Confidentiality**: maintaining secrecy

The main goal of a CPS is **availability**, while the typical goal of IT security is **confidentiality**.

### Security Concepts to Review

- Access control
- Symmetric and asymmetric encryption
- Public key cryptography and distribution
- Digital signatures
- Hash functions
- Message authentication

## Ten Security Principles

1. **Economy of mechanism**: complexity is the enemy of security, choose simplicity
2. **Fail-safe defaults**: default behavior of a system should be the secure option
3. **Complete mediation**: every access to a resource is checked for compliance to an authorization scheme, e.g. getting on a flight, session timeouts.
4. **Open design**: open source systems can be examined and verified
5. **Separation of privilege**: access should be granted only for the purpose of the access; i.e. separate groups reduces attack surface if unauthorized access is gained (see [Security Zones](#security-zones))
6. **Least privilege**: systems should grant the bare minimum privileges necessary for a task
7. **Least common mechanism**: shared resources should be minimized, e.g. having separate user accounts
8. **Psychological accessibility**: security should be intuitive for the user (if you make it too complicated, no one will do it)
9. **Work factor**: security measures should not be unnecessarily excessive -- what will suffice?
10. **Compromise recording**: it may make more sense to record a compromise than stop it

## CPS Challenges

- **Interoperability**: systems need to work together, i.e. compatible standards
- **Predictability**: system state and functionality, high predictability means reliability
- **Reliability**: degree of correctness
- **Sustainability**: efficiency, dynamic evolving
- **Dependability**: trust in the system, not failing
- **Security**: integrity and confidentiality

Considerations for CPS include:

- Countermeasures: designing attack-resilient algorithms and systems
- Compliance with standards and regulation
- Detection and recovery: training operations in response, reconfiguration, fault detection, isolation
- Resilience and redundancy to guarantee availability
- Legislation and enforcement

## Networks

- Body Area Network (BAN): wearables, smartphone
- Local Area Network (LAN)
- Metropolitan Area Network (MAN)
- Wide Area Network (WAN): Internet

Network segmentation can support security zones. Logical segments include:

- Public networks, e.g. Internet
- Business networks
- Operations networks
- Plant control networks
- Supervisory control networks
    - ICS servers, workstations, HMI
- Basic or local control networks
    - Controllers, PLC, RTU, field devices, IED, subsystems
- Process networks
    - Device network, analyzer network, equipment monitoring, automation systems
- SIS and devices (see Safety Instrumented Systems (SIS))

### Network Topologies

- **Bus**: all nodes are connected to one cable
    - Linear, serial, multiple devices via taps
    - Shared resources, so limited reliability
    - For a small number of devices
- **Mesh**: each device is connected to all others, e.g. wireless
    - Maximum performance and uptime
    - Redundant and fault-tolerant
    - Expensive when wired
    - Wireless mesh can be limited by power availability
- **Star**: point-to-multi-point network with a central support
- **Branch** or **tree**: a trunk topology supporting additional branches
- **Ring**: circular serial nodes
    - provides redundancy
    - for network access switches
- **Multihoming** or **dual-homing**: single node connected to two or more networks
    - not recommended, instead use DMZ or read-only data diode or unidirectional gate

### Network Architecture Importance

| Focus                     | Industrial Command and Control | Industrial Supervisory | Business Network     |
| ------------------------- | ------------------------------ | ---------------------- | -------------------- |
| Real-time operation       | Critical                       | High                   | Best-effort          |
| Reliability or resiliency | Critical                       | High                   | Best-effort          |
| Bandwidth                 | Low                            | Medium                 | High                 |
| Sessions                  | Few, defined                   | Few                    | Many                 |
| Latency                   | Low, consistent                | Low, consistent        | Retransmission is OK |
| Network                   | Serial, ethernet               | Ethernet               | Ethernet             |
| Protocols                 | Real-time, proprietary         | Not real-time, open    | Not real-time, open  |

### Network Considerations

- **Latency**: round-trip time for a packet to traverse a network from source to destination
    - Consideration for data historians, choice of database, network hops, deep packet inspection
- **Jitter**: variability in latency over time
    - Severe latency can affect vital precision for ICS
- **Bandwidth**: total amount of data transmitted over a given period, e.g. MB/s or GB/s
    - Most ICS need low bandwidth but have bursts
- **Contention**: competition between different active nodes in a segment
- **Throughput**: volume of data that can physically flow through a network
    - Affected by cabling, interfaces, MAC, protocol choice, application, network, packets per second

Quality of Service (QoS) is the ability to prioritize some types of traffic over other types. Traffic between a PLC and HMI needs to be prioritized so that the most critical communication isn't delayed. To identify important traffic, Type of Service (ToS) and Class of Service (CoS) are used.

## OSI Model

The [Open Systems Interconnection (OSI) conceptual model](https://en.wikipedia.org/wiki/OSI_model) has seven layers describing functionality.

| Layer        | Protocols                                                                   | Data Type         | Function                                                                                                              |
| ------------ | --------------------------------------------------------------------------- | ----------------- | --------------------------------------------------------------------------------------------------------------------- |
| Application  | FTP, HTTP, SMTP, TFTP, DNS, SNMP, DHCP (most common ICS protocols are here) | Message data      | High-level application APIs                                                                                           |
| Presentation |                                                                             | Message data      | Character encoding, compression, encryption and decryption                                                            |
| Session      |                                                                             | Message data      | Communication session management                                                                                      |
| Transport    | UDP, TCP, SCTP                                                              | Segment, datagram | Transmission of data; end-to-end communications over a network, including segmentation, acknowledgement, multiplexing |
| Network      | IP, IPX, IGMP, ICMP, ARP                                                    | Packets           | Addressing, routing, traffic control                                                                                  |
| Data link    | 802.11 (wireless), Ethernet, LAN, WAN                                       | Frame             | Transmission of data frames between nodes physically connected                                                        |
| Physical     | Bits                                                                        | Bits              | Transmission of raw bit stream over physical medium                                                                   |

## TCP vs. UDP

[Transmission Control Protocol(TCP)](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) is a byte stream without record bounds. [User Datagram Protocol (UDP)](https://en.wikipedia.org/wiki/User_Datagram_Protocol) sends connectionless diagrams.

TCP offers reliability, retries, and flow control using a sliding window.

See [What is TCP/IP? Layers and protocols explained](https://victoria.dev/blog/what-is-tcp/ip-layers-and-protocols-explained/).

TCP IP addressing examples (these don't correspond to the layers directly):

| Layer       |          | Type                           | Example                  |
| ----------- | -------- | ------------------------------ | ------------------------ |
| Application | Message  | Application-specific addresses | `data[]`                 |
| Transport   | Segment  | Port                           | `80`                     |
| Network     | Datagram | Logical addresses              | `192.168.11`             |
| Data link   | Frame    | Physical addresses             | `12::34::56::78::9A::BC` |
| Physical    | Bits     |                                | `100101010`              |

A three-way TCP handshake has these steps:

1. Sender sends initial sequence number (SYN)
2. Receiver sends its initial sequence number with acknowledgement appended (ACK)
3. Sender acknowledges receiver's acknowledgement with another appendage

See [What is TLS? Transport Layer Security encryption explained in plain english](https://victoria.dev/blog/what-is-tls-transport-layer-security-encryption-explained-in-plain-english/).

TCP connection termination has these steps:

1. Sender sends active close (FIN)
2. Receiver sends ACK
3. Receiver sends FIN
4. Sender sends ACK

## Industrial Control Systems (ICS)

ICSs can be...

- **Open-Loop**: one-way flow, no feedback
    - Desired output > controller > actuator > process > output
- **Closed-Loop**: additional control of output based on feedback signal compared to desired output
    - Desired output > error > controller > actuator > process > actual output > feedback > adjust controller
    - A fully automated loop means the process is controlled by comparing established set points against inputs
- **Multiloop Feedback Control System**: same as closed-loop but more feedback loops

Actuators include:

- Valves
- Boiler, Turbine, shaft, generators that carry output to someplace else

Controllers include:

- Computers
- Speed generators

Sensors include things that take measurements.

The [ISA95](https://www.isa.org/standards-and-publications/isa-standards/isa-standards-committees/isa95) is a standard for integration of enterprise and production control systems.

![Graphic of the enterprise-control model](https://web.archive.org/web/20141105030807if_/https://www.isa.org/uploadedImages/Content/Standards_and_Publications/ISA_Publications/InTech_Magazine/Issues/2001/October/20011048.gif){ align=left }

It defines these levels:

- L4: Business planning and logistics
- L3: Manufacturing operations and control
- L2-L0:

    - Batch, continuous, or discrete control
    - Field devices and networks
    - PLC, SCADA, HMI

### ICS Devices

- **Remote Terminal Unit (RTU)**
- **Intelligent Electronic Device (IED)**: supports specific function, e.g. managing unstable voltage
- **Human-Machine Interface (HMI)**: the operator's buttons
    - Simplifies PLC logic
    - Security relies on access control here
    - Common attack vector
    - Sends feedback that becomes input for control loops
- **Supervisory Workstations**: a HMI with admin mode
    - Remote monitor
    - May be a read-only dashboard
- **Data Historian**: keeps historized data that can be analyzed for optimization
    - Interoperates with ICS server
    - Hardened, strict user access controls
- **Business Information Console**: provides business intelligence to upper management
    - Usually has the same data as HMI historian
    - May be physical console, or remote HMI/historian
    - The best place to isolate the process control network from outside systems
    - Needs network security since the business info is kept outside of process control
- **Process Management**: HMI control point with real-time information
    - Allows manually affecting output of the control loop (force) by translating function code to human-readable controls (a GUI)
    - These have various protocols and are typically written in vendor-proprietary code
    - Important to security, as a compromise can permanently alter systems (see [The Real Story of Stuxnet](https://spectrum.ieee.org/telecom/security/the-real-story-of-stuxnet))

### Programming ICS

A Programmable Logic Controller (PLC) has these components:

- Power supply (AC/DC)
- Communication module(s) (to talk to other PLCs, packaged equipment)
- Control processor
- Input module(s) (sensors)
- Output module(s) (actuators)

A PLC scan (loop) has these steps:

1. Check input status
2. Execute program
3. Update output status

> The IEC (International Electrotechnical Commission) officially recognizes five PLC programming languages in the IEC61131-3 Standard. They are Ladder Diagram (LD), Function Block (FBD), Structured Text (ST), Instruction List (IL), and Sequential Function Chart (SFC) [(Source)](https://www.learnrobotics.org/blog/plc-programming-languages/)

## Critical Infrastructure

Initially defined in [Homeland Security Presidential Directive 7 (HSPD-7)](https://www.cisa.gov/homeland-security-presidential-directive-7) and revoked and redefined in [Presidential Policy Directive 21 (PPD-21)](https://obamawhitehouse.archives.gov/the-press-office/2013/02/12/presidential-policy-directive-critical-infrastructure-security-and-resil), critical infrastructure is described as these 16 sectors:

1. Banking and finance
2. Chemical
3. Information technology
4. Critical manufacturing
5. Defense industrial base
6. Food and agriculture
7. Commercial facilities
8. Communications
9. Dams
10. Emergency services
11. Energy
12. Government facilities
13. Health care and public health
14. Nuclear reactors, materials, waste
15. Transportation systems
16. Water and waste water systems

## Remote Access Controls

Considerations for remote access include:

- Minimizing attack vectors
- Least privilege (see [Ten Security Principles](#ten-security-principles))
- Minimal access paths
- Further segmentation:
    - Application control (limit remote users to certain authorized applications)
    - No direct access to critical systems
    - A strong security policy
    - Avoidance of remote credential storage
    - Ability to terminate the remote connection locally
    - Log all the things

## Safety Instrumented Systems (SIS)

A SIS is a dedicated controller or logic solver designed to protect against the effects of an attack by reacting to unsafe conditions. A SIS is designed specifically for its purpose, e.g. providing protection at a gas refinery, or chemical or nuclear facility.

The IEC 61511 standard describes design and management for SISs and describes Safety Integrity Levels (SIL) 1-4. See a description of the standard and purposes: [S84 / IEC 61511 Standard for Safety Instrumented Systems](https://instrumentationtools.com/s84-iec-61511-standard-safety-instrumented-systems/).

## Industrial Network Protocols

### Fieldbus

Fieldbus is a category of protocols commonly found in process and control between sensors and actuators and their corresponding PLCs. A standard of nine protocol profiles was introduced in IEC 61784:

1. FOUNDATION Fieldbus
2. CIP
3. PROFIBUS/PROFINET
4. P-NET
5. WorldFIP
6. INTERBUS
7. CC-Link
8. HART
9. SERCOS

### Modbus

Modicon communication bus (Modbus) was designed in 1979 and, as an open standard, is widely used today. It operates at the application layer of the TCP/IP protocol stack.

Modbus on its own lacks security. There's no:

- Authentication
- Encryption
- Checksums
- Broadcast suppression (all devices receive all messages)

Security recommendations include:

- Use of an ICS-aware IDS to monitor communications
- Fingerprint normal behavior patterns to facilitate whitelisting
- Use of an application-aware firewall

A protocol data unit (PDU) consists of a function code and data.

```text
╔═══════════════╗ ╔══════════════════════════════════╗
║ Function code ║ ║               Data               ║
╚═══════════════╝ ╚══════════════════════════════════╝
```

An application data unit (ADU) consists of an address, a PDU, and an error check.

```text
╔═════════╗ ╔═══════════════╗ ╔══════════════════════════════════╗ ╔═════════════╗
║ Address ║ ║ Function code ║ ║               Data               ║ ║ Error check ║
╚═════════╝ ╚═══════════════╝ ╚══════════════════════════════════╝ ╚═════════════╝
```

The Modbus data specification looks like this:

| Data table       | Object type | Access     | Data provided by |
| ---------------- | ----------- | ---------- | ---------------- |
| Discrete input   | Single bit  | Read-only  | Physical I/O     |
| Coil             | Single bit  | Read-write | Application      |
| Input register   | 16-bit word | Read-only  | Physical I/O     |
| Holding register | 16-bit word | Read-only  | Read-write       |

A Modbus Application Protocol (MBAP) header consists of:

```text
╔════════════════╗ ╔═════════════╗ ╔════════╗ ╔═════════╗
║ Transaction ID ║ ║ Protocol ID ║ ║ Length ║ ║ Unit ID ║
╚════════════════╝ ╚═════════════╝ ╚════════╝ ╚═════════╝
```

Variants of Modbus have been adapted for other purposes, including:

- **Modbus over TCP/IP**
    - Combines the Modbus Application Protocol (MBAP) header with a Modbus RTU ADU (legacy systems)
- **Modbus TCP**
    - Combines the MBAP header with only the Modbus RTU PDU (modern)
- **Modbus RTU** (binary)
    - Has an ADU of maximum 256 bytes
- **Modbus ASCII**
    - Has an ADU of maximum 510 bytes
- **Modbus Plus**

Modbus is a request-response protocol with three PDUs:

1. Modbus Request
2. Modbus Response
3. Modbus Exception Response

A typical transaction without errors goes like this:

1. Client initiates a request, sending the function code and data request
2. Server performs the action and initiates a response, sending the function code and data response
3. Client receives response

Each device communicating via Modbus needs a specific address. Capturing packets with [Wireshark](https://www.wireshark.org/) is a great way to observe Modbus in action.

### Distributed Network Protocol (DNP3)

Designed for use in electric utility and extended for oil and gas, water, and wastewater, with specific features that apply to these industries, including:

- Report by exception
- Data quality indicators
- Time-stamped data including sequence-of-events, to aid reconstruction
- "Select before operate" two-pass procedure on outputs

DNP3 is reliable and efficient, and well suited for real-time data transfer:

- Cyclical redundancy checks
- Optional link-layer acknowledgements
- Retransmission of faulty frames

Two types of data:

- Static data, or class 0: represents a static value
- Event data, or class 1, 2, or 3 by priority: represents a change

Communication is initiated from the master station to the slave outstation:

1. Master sends request message
2. Slave sends response message
3. Master confirms (ACK)

DNP3 also supports unsolicited responses for alarms:

1. Slave sends unsolicited response
2. Master confirms (ACK)

Every DNP3 device needs a unique address.

DNP3 lacks security. There's no:

- Authentication
- Encryption

### Secure DNP3

This DNP3 variant adds authentication to the response-request process, issued as a challenge by the receiving device. A unique session key is hashed with the message data to verify authority, integrity, and pairing. It can also operate over TLS for confidentiality.

Secure DNP3 operates at the application layer of the TCP/IP protocol stack as a payload of the TCP/IP packet.

```text
├─────────────────────────────────────── IP Packet ─────────────┼────────── Payload ───────────┤
                                                                ├───────── DNP3 LPDU ──────────┤
╔════════╗ ╔════════════════════════╗ ╔═══════════════════════╗ ╔═════════════╗ ╔══════════════╗
║  HDLC  ║ ║ Source/Destination MAC ║ ║ Source/Destination IP ║ ║ DNP3 Header ║ ║ DNP3 Payload ║
╚════════╝ ╚════════════════════════╝ ╚═══════════════════════╝ ╚═════════════╝ ╚══════════════╝
```

The process for critical requests looks like this:

1. Master sends request message
2. Slave sends authentication challenge
3. Master sends authentication response
4. Slave confirms ACK
5. Slave sends response message
6. Master confirms ACK

### Process Fieldbus (PROFIBUS)

Designed in late 1980s to allow PLCs to communicate with host computers, there are a number of specialized variants:

- PROFIBUS PA: for instrumentation for process automation
    - Developed to address needs of field instrumentation in hazardous areas (explosive vapors, dust)
    - Uses *intrinsic safety* concept to to limit power on communication lines so that dust or vapor in the vicinity will not be ignited
- PROFIsafe: safety applications
- PROFIdrive: high-speed drive applications
- PROFIBUS DP (decentralized periphery): the most widely deployed variant, and it's turtles all the way down...
    - PROFIBUS DP-V0
    - PROFIBUS DP-V1
    - PROFIBUS DP-V2
    - These three variants are minor upgrade versions of the former

PROFIBUS communications have three profiles: asynchronous, synchronous, and via Ethernet (ethertype 0x8892) -- also called PROFINET.

PROFIBUS uses token sharing to allow multiple master nodes (PLCs or RTUs) to communicate with its slaves (sensor, motor, or other control system device). Slaves can initiate communication to their master node or other slaves.

PROFIBUS lacks security. There's no authentication (a spoofed node could impersonate a master node), but as it uses a naturally segmented serial network, compromise requires physical access. [Stuxnet](https://spectrum.ieee.org/telecom/security/the-real-story-of-stuxnet) is an example of PROFIBUS exploitation.

### Industrial Ethernet

Adapts the IEEE 802.3 Ethernet standard to real-time industrial automation applications and provides physical enhancements to harden Ethernet, such as more rugged wiring, connectors, and hardware designed for industrial application. Some considerations:

- Electrical noise and interference (EMI)
- Vibration
- Extended temperatures and humidity
- Power requirements
- Support of real-time performance: low latency, low jitter, minimal packet loss

There are [30 different varieties of Industrial Ethernet](https://www.pdv.reutlingen-university.de/rte/), because apparently we can't all just get along.

The five most widely-accepted are:

1. **EtherNet/IP**
    - Based on TCP/IP
    - Real-time protocol using TCP and UDP
    - Susceptible to any vulnerabilities of its underlying protocols
2. **PROFINET**
    - Based on TCP/IP
    - Open standard (IEC 61158 and IEC 61784)
    - Version 1: standard Ethernet and TCP/IP
    - Version 2: software-based Real Time (RT) support with cycle times of 5-10ms
    - Version 3: Isochronous Real Time (IRT) with cycle times of less than 1ms and jitter less than 1ms
    - Susceptible to any vulnerabilities of its underlying protocols
3. **EtherCAT**
    - Direct Ethernet access
    - Communicates large amounts of data over a single Ethernet frame
    - Short cycle time, low jitter: only 1-2 frames required for a complete cycle
    - Susceptible to any Ethernet vulnerabilities
    - Highly susceptible to DoS attack
4. **Ethernet POWERLINK**
    - Direct Ethernet access
    - Based on Fast Ethernet with direct encapsulation of Ethernet frames
    - Three time periods: "start of cycle" frame, synchronous phase, then asynchronous phase
    - Depends on controlled request-response cycles: susceptible to disruption
    - Highly susceptible to DoS attack
    - Susceptible to any Ethernet vulnerabilities
5. **SERCOS III (Serial Real-Time Communication System)**
    - Direct Ethernet access
    - Deterministic real-time motion control and I/O applications
    - High-speed, low jitter
    - Network supports up to 511 slave devices in ring or straight topologies
    - Cyclical master-slave protocol
    - Susceptible to any Ethernet vulnerabilities
    - When option to support embedded open TCP/IP and UDP/IP communications is enabled, a device using SERCOS III could launch an inbound attack to other systems

Security support for all these protocols include:

- Use of an ICS-aware IDS to monitor communications
- Use of an application-aware firewall

### Backend Protocols

#### Open Process Communications (OPC)

A series of standard specifications (not actually a protocol) released in 1996 (referred to as OPC Classic). It provides a mechanism for various technologies to exchange data at a higher level than fieldbus. As a Windows interconnection, these technologies include:

- Object Linking and Embedded (OLE)
- Component Object Model (COM)
- Distributed Component Object Model (DCOM)

A client application calls a local process that is executed on a remote server. A remote procedure call (RPC) is used to provide functionality. Roughly, this takes the form:

1. OPC client establishes a connection to the OPC server
2. OPC server communicates with a data source via native protocol (Modbus, DNP3, etc.)
3. OPC server receives data.
4. OPC client receives data over the network and provides it to the requesting application.

OPC security concerns include:

- Use of DCOM and RPC, vulnerable to multiple attack vectors
- Host security concerns due to Windows support
- Legacy authentication services

The newer Unified Architecture (OPC-UA) should be used over OPC Classic.

#### Inter-Control Center Communications Protocol (ICCP)

Designed for bidirectional Wide-Area Network (WAN) communication between a utility control center and other control centers, power plants, and substations.

Used for:

- Establishing connection
- Accessing information (read requests)
- Information transmission
- Notifications of changes, alarms, exceptions
- Configuring remote devices
- Control of operating programs

A bilateral table is used to define permissions to control access for each control center. It works like this:

1. Client sends request
2. Server checks bilateral table to validate permissions
3. Server returns response

Security concerns include:

- Lack of authentication and encryption
- Exploitation of bilateral tables to explicitly define trust
- High accessibility

Secure ICCP variants incorporating digital certificates should be used where possible.

### IEC 61850 Standard

Designed originally for subsystem automation and adapted for other applications (hydroelectric plants, wind turbines, distribution feeders, high-voltage switch gears).

- Configuration by Substation Configuration Language (SCL) (based on XML)
- Data model and communications model
- Designed to run on standard Ethernet LAN
- Fiber optics has the advantages of avoid electromagnetic interference, prevent unplanned power conductors, and permit high throughput
- Seeks to be [the single complete standard](https://xkcd.com/927/) for configuration, monitoring, reporting, storing, and communicating

![xkcd: How Standards Proliferate](https://imgs.xkcd.com/comics/standards.png)

Classes of communications protocols:

- Machine-to-machine (M2M)
    - Based on Generic Substation Event (GSE) protocol (P2P layer 2 multicast)
    - Generic Substation State Events (GSSE) and Generic Object Oriented Substation Events (GOOSE)
    - GOOSE is most common and was designed to operate at layer 2 of the OSI model (link layer) to ensure 4ms performance requirement
- Client-server
- Configuration protocols

Doesn't include security specifications on its own (they're in IEC 62351-6):

- Requires TLS
- Use VLANs for protection (encryption would be too slow for GOOSE)
- VLANs can be spoofed

## Smart Grid

Many interconnected and inter-communicating systems using open and proprietary protocols, including:

- Billing systems
- Bulk electric generation systems
- Customer information and management systems
- Electric distribution systems
- Electric transmission systems
- Load management systems
- Meteorological and environment monitoring systems
- Meter data management systems
    - Smart meter device logs
    - Storage of usage statistics
- Protection systems
- Substation automation systems

To remain available, the smart grid needs to have high resiliency. Threats to resiliency include:

- Growing demand and peak demand for resources
    - Baseload, intermediate load, and peak load correspond to rising costs
- Cyber attacks

Key components include:

- Substation automation
- Synchronized phasor measurements (using PMU)
- Advanced Metering Infrastructure

Security strategies include:

- Monitoring for situational awareness
- Risk and threat analysis early in deployment and planning stages
- Separation of services
- Defense-in-depth

Cyberattacks specific to the smart grid involve:

- Mapping grid architecture through reconnaissance and scanning
- Identifying and penetrating target systems
- Propagating throughout a breached system (more connections makes propagation easier)

Common attack motives include:

- Information theft for profit or reconnaissance
    - PII of smart grid consumers
    - Energy consumption information
    - SCADA and automation systems that give operational details of smart grid
- DoS
- Service manipulation, e.g. GPS spoofing to desynchronize phasors

### Generation System Architecture

Familiarity with general common architectural components can help identify possible risks. Possible risk areas include:

- Manipulation of fuel supply
- Burner controls
- Valve or flow manipulation
- Turbine operation
- Intake pumps
- Voltage or capacity manipulation
- Generation SCADA (G-SCADA) for managing automation

Anything with a microprocessor can be compromised, and the manipulation of any one component of a system can compromise the system.

### Transmission System Architecture

Main focus areas:

- Generation control
- Delivery
- Energy storage
- Condition, reserve and interchange management

Highly distributed, highly physically accessible systems covering wide areas. Primary components that present risk if compromised include:

- Substations, or yards (substation automation systems)
    - Provide voltage measurement and regulation, and line protection
    - Handles step-up and step-down of voltage for distribution (see Transformers... below, not the movie)
- GPS satellite timing (see PMU)
- Transmission SCADA (T-SCADA) for managing substation processes
- Phase measurement unit (PMU) systems and phase data concentrators
    - A PMU is also called synchrophasor, which sounds like a super 1337 80s-neon cyberweapon.
    - Synchronizes multiple phasor measurements from different points on the grid using GPS, which allows accurate measurement of grid quality (voltage and current)
    - Provides real-time data to TSCADA that is used to manage substation functions
    - Uses [IEEE C37.118](https://standards.ieee.org/standard/C37_118-2005.html) standard for phasor measurements (superseded by [IEEE/IEC 60255-118-1-2018](https://standards.ieee.org/standard/60255-118-1-2018.html))
        - Lacks inherent security, uses intrinsic mechanisms for data and control
        - If not properly protected, measurements can be easily intercepted and manipulated
    - Commercially produced, support multiple protocols, uses commercial OS like Windows, and are vulnerable to compromise
- Line protection systems
    - Prevent surges (overcurrent) and outages (undercurrent) with breakers, line monitoring systems, automation
    - Built on common commercial OS
- Transformers (more than meets the eye)
    - Step-up for electric transmission, located at transmission system ingress
    - Step-down for end-user consumption, located at transmission system egress to distribution
- Gateway enabling communication with control center and WAN
    - Translates messages using many different protocols (broad attack surface) from substation systems and controllers and between a central transmission control
    - Provides activity logs for the substation, i.e. to historian
    - Prime target for attack as host security or malware prevention here is rare
    - Direct, indirect, remote manipulation from attack will affect many components
- Transmission RTU, IED, PMU, and wide area measurement

### Distribution System Architecture

Main focus areas:

- Load management and modelling
- Two-way power flow
- Risk analysis and outage management
- Dynamic feed or reconfiguration to avoid outages

Primary components that present risk if compromise include:

- Distribution SCADA (D-SCADA)
    - Handles many remote C&C functions, different protocols (broad attack surface)
    - Established communication channel means you can use it to access many connected systems, e.g. AMI or G-SCADA
- Field controller (RTU)
    - Many protocols supported (broad attack surface)
    - Compromise may mess up protection systems, cause random false failures

### Advanced Metering Infrastructure (AMI)

Primary components:

1. Smart meters
      - Solid-state meter with RT data collection
      - Microprocessor and local memory
      - Use of communication network, often including home network (additional attack surface)
      - Diagnostic ports
      - Remote management and configuration via software
2. Communication network
      - Broadband over powerline, powerline communications, radio, telecommunication network
3. AMI server or headend
      - Software based, commercial OS (usually Windows) and DB

Common features include:

- Remote meter reading
- Remote billing
- Demand and response
- Energy delivery
- Remote connect/disconnect
- Remote payment and pre-payment

Common attack targets include:

- Meter data management system
- Customer information system

Security concerns include:

- Protocols that vary widely in inherent security and vulnerabilities (broad attack surface)
- Smart meters are physically accessible and require board and chip security
- Home and business LAN can be used as ingress to AMI and thus smart grid

Adjacent to Home Area Network (HAN), which provides additional attack entry points:

- In-Home Devices (IHD) (smart appliances, like your toaster)
- Home Energy Management Systems (HEMS)

## Hacking ICS

- Security does not lead to safety and vice versa
- The more details (an attacker has) the more damage (they can inflict)

Possible cyberattack incidents include:

- Disrupting feedback loops
- Changing configuration, PLC logic, RTU programming, or other controllers
- C&C channels affecting secure systems

Impact of cyberattack on ICS is local, regional, possibly global, and includes:

- Damage to equipment, facilities, supply to end users, loss of life
- Deterioration of business reputation
- Loss of production and intellectual property

Common targets include:

- SCADA servers via control network
- Engineering workstation (single host with access to configure control mechanisms)
- Yard protection equipment, e.g. via feeder measurements
- Energy management systems, e.g. manipulating current load

Common attack methods include:

- MitM
- DoS
- Replay attack
- Reverse-engineering devices
- Use of known unpatched vulnerabilities

Attacks succeed due to:

- Insecure communication protocols
- Lack of device-to-device authentication
- Fragile communication stacks in embedded devices
- Using readily available software tools on COTS components

Handling infection (compromise):

- Removing malware immediately may activate dormant subsequent malware and removes the ability to learn from the compromise
- First logically isolate the infected host (remove from network or physical assets)
    - Possibly continue to allow malware C&C in a sandbox
    - Collect logs and other data for later investigation

### CPS Sensory Channel Attacks

Sensors are input for CPS. Sensory channels include:

- Visible light
    - Automatic controls, e.g. brightness control for LCDs
    - Light detection and ranging (LIDAR)
    - Use as side channels for sensitive data
- Infrared light
    - Remote control systems
    - Navigation assistance, obstacle avoidance
    - 3D mapping
    - Covert channel support
- Acoustic
    - Obstacle avoidance and map building (short burst sound wave used to take measurements by reflections, with transducers)
- Seismic
    - Accelerometers

Maliciously, sensory channels can be used as:

- Triggers for existing malware on devices
- Transfer of malware between devices
- A combination medium with other sensory channels and traditional channels

Conventional security focuses on traditional channels and is not sufficient for novel attacks on sensory channel threats.

## Securing ICS

Main challenges include:

- Zero tolerance for false positives
- Commercial security software may be unsuitable for time-sensitive, high-precision ICS

### Security Zones

Establish security zones (logical or physical asset groups that have common security requirements) and defined conduits for communication. (See Separation of Privilege in [Ten Security Principles](#ten-security-principles))

Zones can be created according to:

- Security level
- Logical (functional) requirements or discrete functionality
- Control loop or process
- Physical asset access
- Placing trusted and untrusted assets (i.e. those that use insecure protocols)
- Network connectivity
- Remote access requirements
- RBAC requirements
- Allowed and disallowed technologies within a zone

Creating zones can help with:

- Areas where security controls should be applied
- Security policies needed
- Defining expected behavior in and between zones
- Placement for IDS and IPS, especially on conduits
- Seeing information flow requirements
- Seeing need for nesting based on security level

Zone examples and security:

- Field zone
    - Often embedded systems, low cost and power means you can't install commercial security software
    - Device security relies on manufacturer, e.g. with whitelisting
    - On conduits to and between field zones: ICS firewall, protocol filter, IPS
- Control zone
    - Whitelisting
    - Anti-virus
    - Configuration management and system hardening
    - Separation of services
    - Host IDS/IPS and DLP
    - Event logging
    - On conduits between: SCADA firewall and IDS/IPS, NAC, NBAD/NTBA, DLP
- Energy services
    - On conduits to and from: enterprise firewall and IDS/IPS, NAC, NBAD/NTBA, DLP
- Back-office services
    - Data integrity and information assurance tools: DB security, auditing, DLP
        - Value of data increases as you move inward from the field
        - Increased reliance on integrity (less on availability)
    - DB Activity Monitoring (DAM)
    - On conduits to and from: enterprise firewall and IDS/IPS, NAC, NBAD/NTBA, DLP
- Enterprise zone
    - On conduits and from: enterprise firewall and IDS/IPS, NAC, NBAD/NTBA, DLP

Modelling systems and physical devices can provide CPS-specific rules for detecting anomalies and fingerprinting device behavior.

**Situational awareness** is the process of perception, decision, and action that enables assessment of and reaction to a situation. (A widely applicable concept; see [The Tao of Boyd: How to Master the OODA Loop](https://www.artofmanliness.com/articles/ooda-loop/).) Considerations include:

- Where is your data stored and how is it being used?
- Collecting information in a format that can be analyzed from a cybersecurity standpoint

### Compensating Controls

These help meet security requirements when other measures are too difficult or impractical to implement. These include:

- Firewalls
- Industrial protocol filters
- IDS and IPS that perform DPI
- Network access control systems
- Application content inspection
- TLS
- Logging and monitoring
    - SIEM or log management tools can detect and compare anomalies after an incident
        - Automated capabilities include data correlation, scoring risk associated with assets, filtering and cross-referencing information with outside sources
    - Network forensics tools
    - DLP detects sensitive information at rest and in motion, e.g. stop sensitive info from being saved to a USB drive

### Security Level (SL)

- ISA-62443 Security Level (SL) standard provides a measurement for the relative security of a zone or conduit
- Used to determine Target Security Level and Capability Security Level to help ensure assets grouped together meet the same SL
- Physical assessment determines the Achieved Security Level, for assurance

## Privacy in CPS

> These patterns can be useful for analyzing our energy use for energy conservation but, together with data from other sources, the potential for extensive data mining is very significant.  
> ⁠— Giovanni Buttarelli, assistant director of the European Data Protection Supervisor

Privacy risks associated with the Smart Grid include:

- The ability to identify a person from data originating from AMI
- PII that is easily accessible due to lack of proper safeguards
- Integration with home smart appliances and communication channels (your smartphone)
- Third parties can utilize energy usage data to profile activities happening at a property
    - If the home is vacant
    - How many individuals live there
    - Number of appliances, use patterns, and identification using libraries of known patterns
    - Whether a property is growing marijuana (subpoena of utility records; see [Kyllo v. United States](https://www.law.cornell.edu/supremecourt/text/99-8508))
- Data sold to third-parties

Privacy strategies for the Smart Grid include periodic sampling and differential privacy.

Privacy Impact Assessment (PIA) or Data Protection Impact Assessment (DPIA) analyzes privacy implications of a system and may:

- Satisfy, meet, or exceed legal requirements
- Demonstrate due diligence
- Help identify and manage risks
- Avoid unnecessary costs, inadequate solutions, loss of trust and reputation
- Inform organizational communications strategy

DPIA subsequently has these options:

- **Risk acceptance**: acknowledge and take no action
- **Risk mitigation** or **reduction**: take steps to reduce the risk or its consequences to an acceptable level
- **Risk transfer**: transfer the risk to a third-party
- **Risk avoidance**: decide not to implement the risky system entirely

Other CPS domains with security and privacy considerations include:

- Healthcare and medicine (implantable devices)
- Intelligent transportation (traffic management systems, infrastructure, vehicle advanced driver assistance systems)
