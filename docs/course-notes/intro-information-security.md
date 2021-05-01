# Introduction to Information Security

--8<-- "includes/abbr.md"

## Textbook

- Computer Security: Principle and Practice, 4 th Edition, by William Stallings and Lawrie Brown.
(ISBN-10: 9780134794105)

## Attacks

Avenues for attack include:

- User input (injection)
    - E.g. command injection: construction of a command that is injected and later executed by the system with the privileges of the web server
- Server variables logged without sanitization
- Second-order injection
- Modifying cookies
- Physical input through RFID, barcodes, scanned forms, etc.
- Inference attacks: use authorized queries to infer unauthorized data

Contingency planning involves:

- Establishing a disaster-recovery failover environment
- Regularly scheduled backup operations
- Redundancy to ensure availability of critical resources and systems

Security maintenance involves:

- Monitoring and logging
- Regularly scheduled backup and archive
- Regular system tests
- Software patches, updates, configuration checks, preferably automatic

Detection includes:

- Signature-based: attempts to match specific attack patterns
- Anomaly-based: attempts to define normal behavior and detect patterns outside normal range
- Code analysis: test suites or static analysis

Effects include:

- Incapacitation: disabling system operation
- Disruption: interrupt or prevention of the correct operations of system functions
- Corruption: undesirable alteration of system operation or data by adversary
- Obstruction: interrupt or hindering delivery of system services

### Denial-of-Service (DoS)

SYN spoofing generates connection request packets with forged source addresses. TCP SYN flood attacks include indirect attacks that utilize multiple systems:

- DDoS
- Reflector attack
- Amplifier attack

### Distributed denial-of-service (DDoS)

Sends large volume of DoS traffic using techniques such as Amplified Distributed Reflective Attack:

- Attacker queries open recursive DNS servers while spoofing victim's IP
- Victim receives large DNS text (TXT) records which amount to many GB of traffic
- Difficult to distinguish legitimate traffic from DDoS traffic
- Attacker doesn't have to use his own computer

- Defenses include:
    - Prevention and preemption
    - Detection and filtering
    - Reaction
    - Source traceback and identification

### SQL injection (SQLi)

If user input is not sanitized, SQL can be injected to change intended queries.

With a query such as:

```sql
`SELECT accounts FROM users WHERE login ='" + login + "'AND pass = '" + password + "' AND pin = " + pin;`
```

Input for `login` such as `" 'or 1=1 --"` will select the full `users` table (because `1=1` is always true) and the comment syntax `--` negates the rest of the query.

- Effectively prevented by using [parameterized queries](https://cheatsheetseries.owasp.org/cheatsheets/Query_Parameterization_Cheat_Sheet.html)

SQLi types include:

- Inband: uses the same communication channel for injection and getting results
- Out-of-band: data retrieval using a different channel than injection
- Inferential: no actual data transfer; data is reconstructed through observing behavior resulting from injection
    - E.g. error pages, long-running queries

### Advanced Persistent Threat (APT)

Persistent and long-term operations, can be nation states.

- High-value targets
- Uses special malware, altered common malware, social engineering (SE) including phishing, and 0-days
- Blends with normal activities, cleans up logging tracks

Lifecycle of operations is cyclical:

1. Define its (next) target
2. Research target and people involved (SE)
3. Test for network detection, test exploitation of target servers
4. Exploit deployment and success (infiltration)
5. Establish outbound connections (create a path for exfiltration)
6. Exfiltrate data
7. Remain undetected

## Stack Overflow

- *Stack buffer overflow* is also called *stack smashing*
- Memory leak is caused by incorrectly managed memory
    - Memory in languages like C must be allocated and released when done with
- Payload is **shellcode**: machine code corresponding to machine instructions that implements an attacker's desired function

Defenses include:

- Using a non-executable stack if the OS or hardware support it
    - Stack in this case is not writable
    - Does not prevent read-only attacks e.g. [Heartbleed](https://heartbleed.com/)
- Stack canaries
    - Writes a canary value just before return address that detects buffer overflow when overwritten
    - Before returning from function, the code checks to see if the canary value has changed
    - Detects modification to return address to prevent execution takeover, e.g. in return-to-libc attacks
- Static code analysis using software tools
- Address Space Layout Randomization (ASLR) makes it harder for attackers to find important locations (e.g. libc function address)
    - ASLR can't prevent read-only attacks where the attacker already has an address

The function's stack frame stores:

- Return address
- Arguments
- Local variables (referenced as offset to EBP, eg. `EBP - 4`)
- Base pointer (EBP) for current stack frame

The stack starts at higher memory addresses and "grows down" in memory (towards lower memory addresses). The heap starts at lower memory addresses "grows upward" (toward higher memory address). See [Smashing The Stack For Fun And Profit](stack_smashing.pdf).

### Reading x86

The below lists can help you understand output from [GNU Project Debugger (GDB)](https://www.gnu.org/software/gdb/documentation/) [commands](https://sourceware.org/gdb/current/onlinedocs/gdb/), such as `disassemble main`.

x86 32 bit registers include:

- %eax: accumulators used for arithmetic, interrupt calls, and I/O operations
- %ebx: base register used to access memory, pass system call arguments, return values
- %ecx: counter register
- %edx: data register used for arithmetic, interrupt calls, I/O operations
- %ebp: base pointer containing the address of the current stack frame
- %eip: instruction pointer containing the address of the next instruction to be executed
- %esi: source index register, a pointer for string or array operations
- %esp: current stack pointer containing the address of the "top" of the stack

x86 assembly language instructions include:

- `MOV src, dest`: copy value from source into destination
- `LEA src, dest`: copy address (load the address) of source into destination
- `ADD`/`SUB`/`AND`/`OR`/`XOR` `src, dest`: perform the mathematical operation in source and leave the result in the destination
- `CMP val1, val2`: compare the two values and set CPU flags as a result
- `JMP addr`: jump to address
- `PUSH src`: push source value onto stack
- `POP dest`: pop value off the top of the stack into destination
- `CALL addr`: call the function at address
- `LEAVE`: clean up the stack frame before leaving function
- `RET`: return from function correctly
- `INT num`: software interrupt to access an OS function
- `NOP`: no operation

## Authentication

Goals include:

- Availability
    - No false negatives -- if it's really me, I can get access
- Authenticity
    - No false positives, meaning if it's not me but they guess my password, they get access

### Trusted Computing Base (TCB)

The TCB needs to know who makes a request for a protected resource. The request comes from the process running on behalf of a user and contains claims about an identity and credentials to prove it.

- Protects access to physical resources
- Requires functional correctness
- Must maintain data integrity
- Protects disclosure of sensitive data in presence of untrusted software
- High assurance can be shown with formal proof

TCB design principles include:

- Least privilege (damage containment)
- Economy of mechanism (simple and easy to analyze and test)
- Open design (avoid "security by obscurity" which is not a thing)
- Complete mediation (check every access and prevent bypass)
- Fail-safe defaults (deny by default)
- Ease of use (users avoid security that's a hassle)

### Authentication Implementation

How can the user prove who they are?

- Password or personal identification number (PIN): something you know, e.g. passphrase
- Token: something you have, e.g. RFID keycards, physical keys
- Static biometrics: something you are, e.g. fingerprint, retina, face
- Dynamic biometrics: something you do, e.g. voice recognition, handwriting

### Passwords

- Reactive password checking: a system runs its own password cracker to find and cancel guessable passwords (see [John the Ripper](https://www.openwall.com/john/))
- Proactive password checking or complex password policy: check to see if password is allowable at time of selection and offer guidance

Passwords should be stored salted and hashed in a password file. (See [SP 800-63B, 5.1.1.2 Memorized Secret Verifiers](https://pages.nist.gov/800-63-3/sp800-63b.html#-5112-memorized-secret-verifiers)). Password salting:

- Prevents duplicate passwords from being visible in the password file
- Increases the difficulty of offline dictionary attacks
- Mitigates the risk of discovering whether a person uses the same password on more than one system

Defenses against client attacks on authentication include:

- Choosing passwords or passphrases with high entropy
- Limiting attempts at password input
- Using physical tokens that would require theft

Defenses against host attack on authentication include:

- Choosing passwords or passphrases with high entropy
- Protecting the password database
- Using one-time passwords (OTP)
- Using challenge-response protocols

### Assurance Requirements

Assurance levels help communicate an organization's confidence in their authentication. Levels range from L1 (relatively low) to L3 (relatively high). See NIST guidance for details in: [SP 800-63-3, Selecting Assurance Levels](https://pages.nist.gov/800-63-3/sp800-63-3.html#sec6).

- System analysis (architecture, integrity, testing)
- Design specification and verification (correctness of implementation, ideally formal verification)
- Covert channel analysis (identify possible means of bypass)
- Trusted facility management (roles, policies)
- Trusted recovery (operations after failures)
- Trusted distribution (protect against unauthorized modification along the chain)
- Configuration management

## Database Security

Threats to DB include:

- Insiders and unauthorized users
- Bevy of information all in one place means it's an attractive target
- Query languages can be abused to gain unauthorized access

## Access Control

For details on DAC, RBAC, and MAC, see [SP 800-53 Rev. 5, AC-3 Access Enforcement](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final).

Enterprise-wide access control needs to manage resources, privileges, and policies.

### Discretionary access control (DAC)

- Based on the identity of the requestor (user)
- Describes the privileges a subject has (who can access what) based on the policy
- A subject may be able to grant access to objects to other subjects at their discretion
- Access control matrix (ACM): 2-D matrix database that stores access rights of users for objects
    - Rows are users (subjects) or groups
    - Columns correspond to resources (objects) to be protected
    - `ACM [U,O]` defines what access rights `U`ser has for `O`bject
- Access control list (ACL): the columns of an ACM describing how objects can be accessed
    - Column for object `Oi`: `[(ui1, rights1), (ui2, rights2), ...]`
- Capability list (C-list): the rows of an ACM describing capabilities (rights) of each user
    - For user `Ui`, row in the matrix is: `[(oi1, rights1), (oi2, rights2), ...]`
    - Rights are described by permissions, e.g. read, write, execute

### Role-based access control (RBAC)

- Users have roles; roles grant certain rights as defined in policy
- Roles do not need to be changed frequently as users come and go
- If policy doesn't specify access for a user, fail safe and deny by default
- RBAC0 (minimum functionality), RBAC1 (adds role hierarchies for inheritance), RBAC2 (adds constraints for configuration, eg prerequisites, cardinality)

### Mandatory access control (MAC)

- Centrally-managed policies (company decides how data should be shared)
- Uses the comparison of security labels describing classification of objects and clearances of subjects
- Examples include [Bell-LaPadula](https://en.wikipedia.org/wiki/Bell%E2%80%93LaPadula_model) (DoD)
    - Read down, write up
- ...and [Biba Integrity Model](https://en.wikipedia.org/wiki/Biba_Model)
    - Read up, write-down
- Labels are totally ordered, sets are partially ordered
    - Succeeded sets are contained in the higher one, otherwise they're not comparable

#### Comparing labels

`L1` dominates `L2` if:
    `L1` > `L2` AND `comp1` > `comp2`
    - e.g. `L1 = (TS, {A,B,C})` and `L2 = (S, {A, B})`
    - levels are totally ordered and first set contains the second

`L2` dominates `L1` if:
    `L1` < `L2` AND `comp1` < `comp2`

`L1` equals `L2` if:
    `L1` = `L2` AND `comp1` = `comp2`

`L1` not comparable with `L2` if:
    `L1` not greater than and not less than and not equal to `L2`
    - probably because sets (partially ordered) aren't comparable
    - e.g. `L3 = (S, {B,C,D})` is not comparable with L1 above; first set doesn't contain second

### Attribute-based access control (ABAC)

- Access by subject (users) to objects (files, tables, programs, and networks), according to a preconfigured access control policy
- Policy rule base or policy store contains rules
- Access control compares attributes of the subject and object to rules and current environmental conditions to determine access
- Strength in expressive power

### UNIX file access control

- Permissions are: read, write, execute
- Classes are: owner, group, and other (world)
- Control checks occur when you try to open the file (`open()` executed by OS), not at read/write time
    - Granted access returns a file descriptor (`fd`), a pointer to a process' file table
- The `setUID` bit temporarily allocates permissions for a file's owner to the executor
- Time-to-check-time-to-use-vulnerability (TOCTOU) arises when file permissions change after `open()` completes and before file is closed

## Virtual Machines (VM)

- Full virtualization: multiple full OS instances execute in parallel on virtual hardware
- Hypervisor or virtual machine monitor (VMM): a trusted computing base (TCB) for VM that coordinates guest OS access to memory, storage, and CPU
    - Acts as API between hardware and OS
    - Partitions and manages physical resources
- Types of full virtualization systems:
    - Type 1 hypervisor: native
        - Software directly atop physical hardware
        - Better security and performance
        - Typically for servers
    - Type 2 hypervisor: hosted
        - Software atop host OS, atop physical hardware
        - Typically for clients

## Malware

- May require a host software
    - Trap doors (e.g. [flight simulator easter egg](https://eeggs.com/items/29841.html))
    - Logic bomb (predefined condition)
    - Trojan (keyloggers, phishing)
    - Viruses (copies itself to spread)
- ...or be independent
    - Worms (spreads through network connections)
    - Botnets
    - Advanced persistent threats (APT)
- Components include an infection mechanism, payload, and trigger

Obfuscation can be achieved by packing: part or all of the file is compressed, encrypted or transformed.

- Defeats signature matching
- Unpacking code needs to be included
- Hard to detect since legitimate programs may also use packing
- Static and dynamic analysis can be combined to unpack and analyze malware

### Viruses

Viruses are inserted into a host program (virus sandwich). Detection can be avoided by compressing the host program code. Steps:

1. Host program is run.
2. Go to main of virus.
3. Check flag to see if a previous infection is present, to avoid repeat.
4. Find and infect uninfected programs.
5. Execute arbitrary functionality.
6. Go to first line of host program.

Stages include:

- Dormant
- Propagation (spreading, e.g. via email)
- Triggered (host program runs)
- Executing (looking for new targets)

Types include:

- Boot sector infector (when system is booted, before the OS)
- File infector (executable files)
- Macro virus (with macro, or scripting code)
- Multipartite virus (multiple types of files)

Concealment classes include:

- Encrypted virus (encryption obscures content)
- Stealth virus (tries to hide from anti-virus)
- Polymorphic virus (changes its signature with every copy)
- Metamorphic (mutates with every infection, rewriting itself)

Memory-resident viruses (rootkit)

- Rootkit resides at the OS level, modifies OS and data structure to hide itself from the user
    - Can filter itself from calls like `ls` by intercepting function calls
- Classes include:
    - Persistent across boot (you can scan for these)
    - Memory-based (doesn't survive reboot)
    - User mode (hides via intercepted API calls)
    - Kernel mode (changes system calls, can be fixed with a new OS installation)
    - VM-based
    - External mode (outside the normal operations with direct hardware access, e.g. BIOS or system management modes)

Antivirus includes:

- Simple scanners (signature-based)
- Heuristic scanners (compare integrity checksums)
- Activity traps (activity signatures, not effective against new behavior)
- Full-featured (combines host-based, network-based, and sandbox)

### Botnet

- Network of many "zombie" computers controlled by an attacker
- The bot code (malware) needs communication with attacker's servers for C&C
    - C&C goals are to not be easily detected or blend with normal traffic, be efficient and reliable, and be hard to block
- Purposes include spamming, DDoS, key logging and ID theft, clickfraud, computation for password cracking, phishing, anonymized criminal communication, online games cheating

## Firewalls

- Active filtering, fails closed
- Prevention by enforcing defined policies
- Limited effectiveness:
    - Only filters what it sees (all traffic must pass through firewall)
    - Must be immune to subversion (some malware can disable firewall)
    - Can be misconfigured by hoomans
- Advantages:
    - Simple, fast, transparent to user
    - Provides insight to traffic via logging (not high-level though)
    - Can do network address translation
    - Can encrypt traffic

### Filtering

Two main types of filtering: packet and session.

**Packet filtering** is applied per-packet.

- Typically configured to filter packets in both directions
- Applies rules based on policy (ACL)
- Applies rules based on packet's info, e.g. source IP, destination IP, transport-level address, IP protocol field, interface
- Rules must be precise to allow wanted traffic and block unwanted traffic, specifying:
    - Direction
    - Source and destination addressesP
    - Protocol
    - Destination port
    - Source port
- Limitations:
    - Decisions are per-packet, not effective against attack over multiple packets
    - See [IP fragment attack](https://en.wikipedia.org/wiki/IP_fragmentation_attack)
    - No state info kept
- Default policies
    - **Discard**: prohibit unless permitted (whitelist)
    - **Forward**: permit unless prohibited (blacklist)
        - Easier to manage a blacklist, but less secure

**Session filtering** filters a packet based on its context within a session.

- Includes Dynamic Packet Filtering and Stateful Inspection Firewall

### Stateful Inspection Firewall

- Directory with entry for each established TCP connection (Connection State Table)
- Packet filter allows incoming traffic to high-numbered ports only for packets that fit the profile of a directory entry
- Reviews packet info, records info about TCP connections
- Keeps track of TCP sequence numbers to prevent attacks that depend on that sequence number
- Inspect data for protocols like FTP, IM, SIPS commands

### Application-Level Gateway

- Application proxy
- Relay of app-level traffic
- Must implement proxy code for each application you want to support
- Can restrict application features as preferred
- More secure that packet filtering
- Additional processing overhead for each connection (vs other types of firewalls)

Here's a typical workflow:

1. User contacts the gateway via TCP/IP to make request of application
2. Gateway requests name of remote host
3. User supplies valid auth info to gateway
4. Gateway contacts application and relays TCP segments
5. If proxy code is not implemented, remote host reports to gateway that service is not supported
6. Gateway forwards response to user

### Bastion Hosts

- Machine with an application-level gateway
- Critical strong point in network security
- Typically a secure OS with only essential services
- Requires authentication even from internal traffic to access proxy or host
- Limited disk use
- Each proxy is isolated and runs as a non-privileged user

### Host-based Firewalls

- Software module used to secure an individual host
- Filters and restricts traffic inbound and outbound
- Commonly on a server

### Personal Firewalls

- Home routers, typically less complex than server/standalone firewalls
- Primarily denies unauthorized remote access
- May monitor outgoing traffic to detect and block worms and malware activity

### Firewall Deployment

**External firewalls** are between the Internet-connected router and the internal DMZ network, where email, web, and DNS servers live.

**Internal firewalls** add additional separation between logical business areas of the internal network and provide two-way protection for the DMZ by monitoring incoming and outgoing (exfiltrated data, anyone?) traffic.

**Distributed firewall deployment** may use stand-alone external, internal, host-based, and personal firewalls together to section servers logically. This helps with security monitoring and log aggregation.

### Firewall Topologies

- **Host-resident firewall**: personal firewall software, software on servers
- **Screening router**: single router between internal/external networks with stateless or full packet filtering
- **Single bastion inline**: single firewall device between internal and external router
- **Single bastion T**: third network interface on bastion to DMZ for external services
- **Double bastion inline**: DMZ sandwiched between bastion firewalls
- **Double bastion T**: DMZ on separate network interface on bastion firewall
- **Distributed firewall configuration**: see [Firewall Deployment](#firewall-deployment)

## Intrusion Detection Systems (IDS)

A mechanism that detects evidence of an unwanted presence on the network.

- Good for known attack types, some sophisticated attacks
- Not good for 0-days or APT
- Alerts must be interpreted

Assumes that:

- System activities can be observed
- It's possible to differentiate between normal and intrusive activities

An IDS uses:

- Sensors, analyzers, and user interface to gather and analyze information from the computer or network to identify possible intrusion
- Passive monitoring (fails open)
- [Signature-based detection](#signature-detection) or [anomaly detection](#anomaly-detection-approaches)

A **host-based IDS (HIDS)** is deployed on a single host.

- Monitors system activity, e.g. process identifiers and system calls
- Detects internal and external intrusions

A **network-based IDS (NIDS)** monitors network traffic at selected points.

- Many sensors, one or more servers and management consoles
- Segments, devices, transport and application protocols
- Large networks may produce lots of alerts; prioritize these for ease of sysadmin
- Uses inline sensors...
    - Can block a detected attack (IPS)
- ...or passive sensors
    - Monitors a copy of network traffic, causing less overhead
- Sensor deployment follows [firewall deployment strategy]()

A **distributed or hybrid IDS** combines multiple sensors in a central analyzer for a more holistic view.

- Coordinates many NIDS
- May have one or more collection and analysis points on the network
- Uses raw data or summary data
- May deal with different sensor data formats

Workflow looks like this:

- Network/system data goes through data preprocessing
    - Generates activity records
- Detection engine uses detection models to examine activities
    - Produces alerts
- Decision engine uses decision table to produce responses/reports

Deployment strategies include:

- Between external network and Internet
    - Sees all attempted attacks targeting tne network
    - Nothing filtered by a firewall
- Just inside external firewall
    - Can catch any traffic that should have been blocked by firewall
    - Can analyze outgoing network traffic
- In front of a subnet or server group
    - Smaller traffic volume for ease of analysis
    - Can better locate intrusions from inside network

### Signature Detection

- Matches known patterns to detect malicious traffic
- Cannot detect new threats, only known ones
- New signatures need to be added to database
- Antivirus, NIDS
- Advantages:
    - Low cost and resource usage
- Disadvantages:
    - Effort to identify, review, create new malware signatures
    - Inability to detect 0-day attacks

### Anomaly Detection Approaches

**Statistical approach** uses univariate, multivariate, or time-series models of observed metrics.

- Multivariate: metrics considered by correlations
- Univariate: each metric treated as an independent random variable
- Time-series: considers time and order of events
- Advantages:
    - Relatively simple
    - Low computational cost
    - Lack of assumptions about expected behavior
- Disadvantages:
    - Must select suitable metrics
    - Cannot model all behaviors

**Knowledge-based approach** uses an expert system (knowledgeable experts write classification rules) to define normal parameters.

- E.g. "office programs are typically used between 9-5."
- Advantages:
    - Easy to update
    - Flexible rules
- Disadvantages:
    - Must trust the experts
    - Time and effort is required to develop knowledge from data

**Machine learning** uses data to train and develop a model to automatically classify behaviors.

- Will detect attacks that are similar to past attacks, possibly 0-days
- Advantages:
    - Adaptable, can handle small changes automatically
    - Captures interdependencies between observable metrics
- Disadvantages:
    - Depends on good training data
    - High false positives
    - High resource cost in the training phase

Machine learning approaches include:

- [Bayesian networks](https://en.wikipedia.org/wiki/Bayesian_network): probabilistic relationships
- [Markov models](https://en.wikipedia.org/wiki/Markov_model): model with a set of states
- [Neural networks](https://en.wikipedia.org/wiki/Neural_network): simulates the brain to classify data
- [Fuzzy logic](https://www.sciencedirect.com/topics/engineering/fuzzy-set-theory): fuzzy set theory, approximation
- [Genetic algorithms](https://www.sciencedirect.com/topics/engineering/genetic-algorithm): inheritance, mutation, selection, recombination
- [Clustering](https://developers.google.com/machine-learning/clustering/overview) and outlier detection

### Heuristic or Rules-Based Detection

- Uses rules to identify known patterns of behavior for penetrations
- Tends to be specific to the machine or OS
- Example: [SNORT](https://www.snort.org/) is a rule-based NIDS that uses a rule header and options
    - Actions are: alert, log, pass, activate, dynamic, drop, reject, sdrop

### Detection Accuracy

For overview, see [Classification: True vs. False and Positive vs. Negative](https://developers.google.com/machine-learning/crash-course/classification/true-false-positive-negative).

For formulas, see [Sensitivity and specificity](https://en.wikipedia.org/wiki/Sensitivity_and_specificity).

- Detection rate or True Positive (TP) rate: when there is an intrusion, the likelihood that the IDS will correctly output an alert
- False Negative Rate: how many intrusions were missed (`FN = - TP`)
- False alarm or False Positive (FP) rate: without an intrusion, likelihood that the IDS will falsely produce an alert
- True Negative Rate: how likely normal activities are correctly classified as normal (`TN = 1 - FP`)
- Bayesian detection rate: given that alert was produced, how likely is it that an intrusion occurred
    - See [Bayesian based intrusion detection system](https://www.sciencedirect.com/science/article/pii/S1319157811000292)

The base rate fallacy says that people tend to ignore an established base rate in the face of individual information. See [base rate fallacy](https://en.wikipedia.org/wiki/Base_rate_fallacy) for interesting general applicability.

- Base rate: prior probability of attacks (`P(I)`)
- Base rate depends on where it is measured

At the network packet level, data volume is huge and base rate is low, so the base rate fallacy tells us that applying analysis here can result in a low Bayesian detection rate.

- Instead, apply detection algorithms to data that has a higher base rate
    - Process event data by selection, filtering, and summarizing
    - Use a filtered packet stream (libpcap, tcpdump filters)
    - This decreases volume of data
- Higher base rate, thus higher Bayesian detection rate, is desired
- A low false alarm rate, as close to zero as possible, is desired

As long as intrusion evidence is kept, decreasing the volume of overall packets produces a much higher base rate than the original packet data.

### Attacking IDS

Evasion techniques can allow attackers to bypass an IDS.

- IDS monitoring is passive, so traffic may still reach its destination while an ongoing attack is analyzed
- Different operating systems and ambiguities in TCP/IP protocol implementations can be exploited, e.g. if the IDS runs on Linux and the target is Windows
- Overlapping (malicious) packet fragments may be discarded by an IDS while the end host accepts them

**Insertion attacks** can use bad checksums to disguise malicious traffic. The IDS will accept a packet with a bad checksum, which disrupts the overall attack pattern, but the end host will reject the bad packet, which reconstructs the attack.

**DoS attacks** can cause resource exhaustion and disrupt IDS:

- Large traffic results in resource exhaustion and can keep the IDS busy while another attack slips by
- A reactive IDS can be abused by causing it to create an overwhelming number of alerts that humans have to deal with (DoS on humans)

See more at [Intrusion detection system evasion techniques](https://en.wikipedia.org/wiki/Intrusion_detection_system_evasion_techniques#Evasion).

## Intrusion Prevention Systems (IPS)

It's an IDS that also tries to block malicious activity.

## Honeypots

- Decoy systems with fabricated information designed to catch illegitimate access
- Logs information about attacker's activities

Low-interaction honeypots mimic services or systems without implementing a full version of the system. They're a low resource operation but won't fool an attacker too long.

High-interaction honeypots are a real, full OS or service. They're instrumented and provide a more realistic target for attackers, but require more resources to run.

Deployment strategies include:

- Outside external firewall
    - No increase to risk for internal network
    - Can reduce alerts issued from firewall
    - Cannot trap internal attackers
- In DMZ
    - Attempted attacks may be blocked by firewall
- In internal network
    - Can detect firewall misconfiguration
    - If compromised, can attack internal systems
    - Firewall has to allow traffic to internal network

## Cryptography

Auguste [Kerckhoffs's principle](https://en.wikipedia.org/wiki/Kerckhoffs%27s_principle): A cryptosystem should be secure even if everything about the system, except the key, is public knowledge.

In other words, "security by obscurity" is not a thing.

### Encryption

The most often used cryptographic operation, encryption converts plaintext data to ciphertext that is unintelligible to a third-party. It provides:

- One-to-one mapping of plaintext to ciphertext (ensure that the message can always be decrypted)
- Confidentiality
- Integrity checking (assurance that message was not tampered with)
- Authenticity and authentication (the message is genuinely sent from the sender)

Encryption can be [symmetric](#symmetric-cryptography) or [asymmetric](#asymmetric-cryptography).

Attacks on encryption include:

- Brute-force attack (iteratively trying all possible keys)
- Cryptanalysis (of cipher's data characteristics)
- Attacks on implementation, e.g. side channel
- Social engineering, see [$5 wrench](https://xkcd.com/538/).

### Decryption

The process of converting the ciphertext back to plaintext. Requires the decryption key.

### Ciphers

An encryption scheme. Simple cipher examples: see Caesar Cipher or shift cipher. Or for fun, see [A Unicode substitution cipher algorithm](https://victoria.dev/blog/a-unicode-substitution-cipher-algorithm/).

A fixed-shift cipher has only 26 possible encodings (for alphabetical letters) and can be trivially brute-forced. A monoalphabetic or substitution cipher that arbitrarily maps letters will have 26! or (~2⁸⁸) possible keys.

Substitution ciphers can be defeated by analyzing statistical frequencies of letters. See [letter frequency](https://en.wikipedia.org/wiki/Letter_frequency).

[Vigenère cipher](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher) uses a matrix to map letters (polyalphabetic substitution).

### Symmetric vs. Asymmetric

When 4 people want to talk, they need:

- 6 individual keys for symmetric encryption ⌧
- 4 key pairs for asymmetric encryption (8 total keys: 4 private, 4 public)

Asymmetric is better for:

- Securely distributing a session key, e.g. TLS
- Scalability (you need fewer key pairs)

Symmetric is better for:

- Confidentiality with speed (asymmetric is slower)

### Symmetric Cryptography

Uses a single shared secret key for encryption and decryption.

Symmetric encryption components:

- Plaintext and ciphertext
- Encryption algorithm

#### Symmetric Cipher Attacks

**Known-plaintext attacks**: a specific known plaintext is compared to its ciphertext to the determine key.

**Chosen-plaintext attacks**: Any known plaintext compare to its ciphertext.

**Differential cryptanalysis**: analyze (compare) effect of changes to the known plaintext on the resulting ciphertext.

**Linear cryptanalysis**: determine the encryption function by analyzing (modeling) known plaintexts and their ciphertext using linear equations and derive the key bits.

### Asymmetric Cryptography

Also "public key cryptography." Uses a key pair (two mathematically-paired keys): a public key for encryption and verification, and a private key for decryption and signing.

Principle components include:

- A public and private key pair
- Encryption and decryption algorithms (cipher suite)
- Plaintext and ciphertext

Public key algorithms include:

- RSA (see RSA)
- **[Diffie-Hellman key exchange](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange)**: one of the first public-key protocols and uses asymmetric encryption to enable two users to agree on a shared secret key for subsequent symmetric encryption exchange
- **Digital Signature Standard (DSS)**: a Federal Information Processing Standard (FIPS) specifying a suite of cryptographic algorithms including Digital Signature Algorithm (DSA).
    - Only for signing
- **Elliptic-Curve Cryptography (ECC)**: meant to improve on RSA; see [Elliptic Curve Cryptography](https://csrc.nist.gov/Projects/Elliptic-Curve-Cryptography)
    - Equal security to RSA with a smaller bit size

#### Public Key Certificate (Digital Signatures)

A Certificate Authority (CA) vouches for the validity of the public key:

1. Key holder provides public key and identifying information to the CA
2. CA combines indentifying information and public key with certificate information (e.g. validity period), and hashes it
3. CA signs the resulting certificate block with its own private key (vouching for authenticity)

When you provide your public key certificate to someone, they can verify it's valid:

1. Extracting and calculating the hash code of the certificate
2. Verify the digital signature using the CA's public key and the signature verification algorithm

Relies on trust in CA: see [DigiNotar](https://darknetdiaries.com/episode/3/).

#### Digital Envelopes

Uses public key encryption to send a "sealed letter."

A message is encrypted with a shared key. The shared key is then encrypted with someone's public key. Only the intended recipient (who holds the private key) can use the shared key to decrypt the message.

### Block Cipher

The most common symmetric encryption algorithm, a block cipher takes plaintext input in fixed-size blocks and produces an equally-sized block of ciphertext.

The algorithm need not be secret and is encouraged to be public, so others can verify it.

Primitives include:

- **Confusion**: substitution is used to obscure the relationship between the key and ciphertext
- **Diffusion**: permutation is used to spread the influence of one plaintext bit over many ciphertext bits and hide the statistical properties of the plaintext
- **Round**: a combination of substitution and permutation
    - Many rounds increase security by propagating changes throughout the ciphertext

Comparisons of block cipher sizes:

| Thing                 | DES size (bits) | Triple DES size (bits) | AES size (bits)  |
| --------------------- | --------------- | ---------------------- | ---------------- |
| Plaintext block size  | 64              | 64                     | 128              |
| Ciphertext block size | 64              | 64                     | 128              |
| Key size              | 56              | 112 or 168             | 128, 192, or 256 |

A longer key length means more keys, and is harder to brute-force.

#### DES

Symmetric key block cipher algorithm, Feistel and IBM, 1977

- 64 bit key (8 bytes) with one parity bit for each byte; effective key length is only 56 bits
- Outputs a 64 bit block ciphertext

There are 16 DES rounds:

- Each round's input is the ciphertext produced by the previous round
- Encryption rounds use K1...K16, decryption uses K16...K1
- Implementation of a **Feistel** cipher, using [F-function]](https://en.wikipedia.org/wiki/Data_Encryption_Standard#The_Feistel_(F)_function) with the four stages:
    - Expansion: use expansion permutation (E-expansion) to duplicate half the bits
    - Key mixing: XOR the result with subkey (derived from main key using key schedule)
    - Substitution: processing by substitution boxes (S-boxes), one of 8 predefined tables -- this is the core of DES security
        - Given a six bit value `011011`, the outer two bits (`0` and `1`) represent the rows and the inner four (`1101`) the columns
    - Permutation: rearrange the 32 outputs according to fixed permutation (P-box)

How to XOR, or "same-same zero, different is one":

- `0 XOR 0 = 0`
- `1 XOR 1 = 0`
- `0 XOR 1 = 1`
- `1 XOR 0 = 1`

DES isn't the most secure anymore:

- 2⁵⁶ keys is too small a keyspace
- S-box design criteria is secret, although still shown to be resistant to attacks

#### Triple DES

It's DES run three times with three different keys to overcome the small key space. Effective key lengths are 56, 112, or 168.

Triple DES is vulnerable to collision attacks.

#### AES

Symmetric key block cipher algorithm, NIST, 2001

A subset of the Rijndael block cipher, more efficient than Triple DES. AES replaces DES.

- Variant of Rijndael, not a Feistel structure
- Fixed block size of 128 bits
- Entire block processed in parallel during each round
- Substitution and permutation
- Key length can be 128, 192, or 256 bit (effectively defeats brute forcing at time of writing)

AES rounds differ with key length:

- 10 rounds for 128-bit keys
- 12 rounds for 192-bit keys
- 14 rounds for 256-bit keys

`KeyExpansion`: round keys are derived from the cipher key using the AES key schedule

For each round:

1. `AddRoundKey`: bitwise XOR combines each byte of the state with a byte of the round key
2. For 9, 11, or 13 rounds:
   1. `SubBytes`: substitution step replaces each byte according to lookup table
       - Uses a 16x16 matrix (S-box) with permutation of all possible 256 8-bit values
       - Left 4 bits are the row value, right 4 bits are the column value for indexing
       - S-box is designed to have a low correlation between input bits and output bits
   2. `ShiftRows`: transposition of last three rows of the state, cyclically shifted some number of steps (diffusion)
       - A circular left shift is performed starting with 1 byte at the second row, 2 bytes at the third row, then 3 bytes at the fourth row.
   3. `MixColumns`: linear mixing operation combines four bytes in each column of the state (diffusion)
       - The four bytes of each column are combined using an invertible linear transformation (matrix multiplication)
   4. `AddRoundKey` again
3. Final round:
   1. `SubBytes`
   2. `ShiftRows`
   3. `AddRoundKey`

Each operation in AES is reversible, which is how AES is decrypted.

[Theoretical attacks](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard#Security) include:

- XSL attack
- Side-channel attack
- Related-key attack
- Known-key distinguishing attack
- Key-recovery attack

At time of writing there are no known feasible attacks that would break AES.

### Block Cipher Modes

A [block cipher mode of operation](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Electronic_codebook_(ECB)) uses a block cipher to provide confidentiality or authenticity. The idea is to repeatedly apply a cipher's single-block operation to transform data larger than a single block.

Authenticated Encryption (AE) protects confidentiality and integrity of communications simultaneously, and can provide security against chosen ciphertext attacks. AE is implemented using a block cipher mode structure.

Block cipher modes defined in [NIST SP 800-38A](https://csrc.nist.gov/publications/detail/sp/800-38a/final) are:

- Electronic Codebook (ECB)
- Cipher Block Chaining (CBC)
- Cipher Feedback (CFB)
- Output Feedback (OFB)
- Counter (CTR)

| Mode | Description                                                                                                                                                        | Typically used in                                                             |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------- |
| ECB  | Each block of 64 bits independently encrypted using the same key                                                                                                   | Transmission of single values, e.g. encryption key                            |
| CBC  | Input to the encryption algorithm is the XOR of the next 64 bits of plaintext and the preceding 64 bits of ciphertext                                              | Block-oriented transmission, authentication                                   |
| CFB  | Input processed *s* bits at a time using preceding ciphertext as input to produce pseudorandom output. Output XORed with plaintext to produce next ciphertext unit | Stream-oriented transmission, authentication                                  |
| OFB  | Like CFB, but input is the preceding DES output                                                                                                                    | Stream-oriented transmission on noisy channels, e.g. satellite communication) |
| CTR  | Each block of plaintext is XORed with an encrypted counter, incremented for each subsequent block                                                                  | Block-oriented transmission, high-speed requirements                          |

#### Electronic Codebook (ECB)

ECB is the simplest [confidentiality mode](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Confidentiality_only_modes).

Issues abound:

- ECB has **weak confidentiality**: the same plaintext block produces the same ciphertext block, which makes it vulnerable to analysis because it does not hide patterns well
- ECB has **weak integrity**: plaintext blocks are encrypted independently, making them vulnerable to cipher block substitution and rearrangement attacks

#### Cipher Block Chaining (CBC)

More secure than ECB, this is the most widely used approach for encrypting large messages.

The IV is XORed with the first plaintext block to produce the first ciphertext. Subsequent blocks are XORed with the resulting ciphertext from the previous block. This makes CBC more resistant to cryptanalysis.

CBC maintains integrity since, if the order of blocks are changed, it will not be properly decrypted. Both parties need the IV to decrypt the ciphertext.

The last block of CBC is the CBC residue and can be sent as a measure of protecting message integrity. Modification of the plaintext means that the CBC residue computed by the receiver will differ from the CBC residue from the sender.

To have both confidentiality and integrity with CBC, two separate keys should be used to do encryption and generate residue. Alternatively, compute a hash of the message then append and encrypt it together.

Some helpful formulas:

| Mode | Formula                             | Ciphertext                                  |
| ---- | ----------------------------------- | ------------------------------------------- |
| ECB  | `Yᵢ = F(PlainTextᵢ, Key)`           | `Yᵢ`                                        |
| CBC  | `Yᵢ = PlainTextᵢ XOR Ciphertextᵢ₋₁` | `F(Y, Key); Ciphertext₀ = IV`               |
| CFB  | `Yᵢ = Ciphertextᵢ₋₁`                | `Plaintext XOR F(Y, Key); Ciphertext₀ = IV` |

### Stream Cipher

A stream cipher converts a plaintext message to ciphertext bit-by-bit.

#### RC4

Variable-key-size stream cipher, Ron Rivest and RSA Security, 1987

Based on the use of a random permutation. Was used in SSL/TLS, WEP, WPA, until prohibited for all versions of TLS by RFC 7465 in 2015.

1. A 256-byte state vector, *S*, is initialized from a variable length key of 1-256 bytes (8 to 2048 bits)
    - Initially the entries of *S* are 0-255 (*S[0]... S[255]*)
    - If the private key, *K*, is 256 bytes, a temporary vector *T* holds *K*; otherwise, the bytes of *K* are repeated to fill up *T*
2. *T* is used to produce the initial permutation of *S*
    - For each byte in *S* (*S[ᵢ]*), we swap with another byte according to the scheme dictated by *T*
3. Each byte of the now-initialized *S* is again swapped with another byte in *S* to achieve stream generation

Encryption is performed by XOR-ing each byte of *K* with the next byte of the plaintext. Decryption is by XOR-ing each byte of *K* with the next byte of ciphertext.

The [Fluhrer, Mantin and Shamir attack](https://en.wikipedia.org/wiki/Fluhrer,_Mantin_and_Shamir_attack) broke the WEP standard when they found RC4 to be vulnerable due to the first few bytes of the output keystream being strongly non-random. Andreas Klien showed more correlations between the RC4 keystream and key in [Klien's attack](https://en.wikipedia.org/wiki/RC4#Klein's_attack), which lead to Erik Tews, Ralf-Philipp Weinmann, and Andrei Pychkine cracking 128-bit WEP in under a minute.

Vulnerable to bit-flipping attack if not used with a strong message authentication code (MAC).

### RSA (Rivest, Shamir, Adleman)

A block cipher that depends on the difficulty of factoring a large integer (𝒏) into two primes (*p* and *q*). The plaintext *M* and ciphertext *C* are integers between 0 and *𝒏-1* for some 𝒏.

Two prime numbers, *p* and *q*, are chosen (using RNG). They must be different and sufficiently large and random. Their product 𝒏 is used to generate public and private keys.

- The public key is *{𝒆,𝒏}*
- The private key is *{𝒅,𝒏}*
- The relationship between 𝒆 and 𝒅 is: *𝒅𝒆 mod φ(𝒏) = 1*
- 𝒆 and 𝒅 are multiplicative inverses modulo *φ(𝒏)* (the [Euler totient function](https://mathworld.wolfram.com/TotientFunction.html))
- The key 𝒅 can be found with modular multiplicative inverse: *𝒅 ≡ 𝒆⁻¹(mod φ(𝒏))*

The message *M* is independent of the values for *p* and *q*, allowing any message to be encrypted using the same public-private key pair. The values *p* and *q* are independent of the exponent 𝒆.

Approaches to attacking RSA include:

- Brute force (try all private keys)
- Mathematical attacks
    - Factoring 𝒏 into *p* and *q*
    - Determining *φ(𝒏)* (without finding *p* and *q*)
    - Determining 𝒅 without determining *φ(𝒏)*
- Timing attacks (using the running time of the decryption algorithm)
- [Chosen ciphertext attacks](https://en.wikipedia.org/wiki/Chosen-ciphertext_attack)

RSA is susceptible to these when the key space is insufficiently small.

### Modular Math

Concepts to review:

- Modular multiplication
- Modular exponentiation
- Multiplicative inverse
- [Euclidean algorithm](https://en.wikipedia.org/wiki/Euclidean_algorithm) and [Bézout's identity](https://en.wikipedia.org/wiki/B%C3%A9zout%27s_identity)

### Hash Function

Cryptographic algorithm that:

- Takes a message digest (data) input of any size and produces a fixed-length output
    - Output is typically 128-512 bits
- Efficient on computational resources (fast to compute)
- Is a one-way function (cannot be reversed to find the original input)

Hash functions are useful for message authentication, password security, and intrusion detection.

Hash functions should have collision resistance, which means that it is not desirable for different inputs to produce the same output.

**Weak collision resistance**: bound to a particular message `m`, it's computationally infeasible to find `m'` with `m' != m` so that `h(m) = h(m')`.

**Strong collision resistance**: given any arbitrary message `x`, there exists no `x` and `x'` with `x != x'` so that `h(x) = h(x')`. This automatically satisfies weak collision resistance.

See [What is the difference between weak and strong resistance](https://stackoverflow.com/questions/8523005/what-is-the-difference-between-weak-and-strong-resistance).

Hash function weaknesses include:

- **Pigeonhole principle**: `n = m`
    - One pigeon (n) per hole (m)
    - If `n > m` then at least one hole has more than one pigeon
- **Birthday paradox**: how many people do you need to put in a room until you have a greater than 50% chance that two of them will have the same birthday?
    - For 100% chance that two people in the room have the same birthday, you need 367 people (leap years)
    - For less than 100%, computed as `P(A) = 1 − P(A′)`
    - Probability that a pair share the same birthday: `1-(k)ₙ/kⁿ`
    - See [Birthday problem](https://en.wikipedia.org/wiki/Birthday_problem)

How long of a hash value do we need to avoid collisions?

| Hash length | Possible number of hash values | Number of messages for collision |
| ----------- | ------------------------------ | -------------------------------- |
| l           | 2¹                             | 2¹/²                             |
| 64          | 2⁶⁴                            | 2³²                              |

### Secure Hash Algorithm (SHA)

- SHA-1: 1995 FIPS 180-1, from National Security Agency (NSA) in DSA -- insecure as of 2010
- SHA-2: 2002 FIPS 180-2, encompasses:
    - SHA-256
    - SHA-384
    - SHA-512
- SHA-3: 2015 FIPS 202 -- compact and useful for embedded devices

Message digest generation (SHA-512) follows these steps:

1. Message is (always) padded (`100...0` appended to last block) so it is a multiple of 1024 bits
2. A 128-bit block is appended (unsigned 128-bit integer containing pre-padded length of original message)
3. 512-bit hash buffer is initialized to hold intermediate and final results
   - Registers initialized to the hard-coded IV
4. Message processed in 1024-bit (128-words) blocks:
   - Each block has 80 rounds
   - First block is processed with IV, giving hash buffer the first intermediate value
   - Each round takes as input the previous intermediate value, a constant (k), and some words from the previous block
   - Operations include circular shifts, primitive pulling functions (and, or, not, XOR)
5. After all *N* blocks are processed, the output is the 512-bit message digest

#### SHA Numbers Table

| Variant | Message digest size | Message size | Block size | Word size     | Rounds |
| ------- | ------------------- | ------------ | ---------- | ------------- | ------ |
| SHA-1   | 160-bit             | <2⁶⁴         | 512        | 32-byte words | 80     |
| SHA-256 | 256 bits            | <2⁶⁴         | 512        | 32-byte words | 64     |
| SHA-384 | 384 bits            | <2¹²⁸        | 1024       | 64-byte words | 80     |
| SHA-512 | 512 bits            | <2¹²⁸        | 1024       | 64-byte words | 80     |

### Hash Based Message Authentication (HMAC)

Hash functions are suitable for message authentication because:

- They are fast to compute compared to encryption algorithms (DES)
- Library code is widely available

To use a hash algorithm for message authentication, we need to incorporate use of a secret key. Enter HMAC, RFC 2104.

HMAC makes modular use of the hash function. A new hash function could easily be used instead, if so desired one day.

The HMAC process goes like this:

1. Secret key *K* is padded with zeroes so it is the same length as the number of bits in a block, *b*
      - E.g. if this HMAC uses SHA-512, *K* is padded to make it 1024 bits (see [SHA numbers table](#sha-numbers-table))
2. XOR the padded key *K⁺* with ipad, producing *Sᵢ*
3. Append the message *M* to *Sᵢ*, producing the stream for step 3
4. Apply the embedded hash function *H* to the stream from step 3
5. XOR the padded key *K⁺* with opad to produce *S₀*
6. Append the hash result from step 4 to *S₀*, producing the stream for step 6
7. Apply the embedded hash function *H* to the stream from step 6
8. Output the result

Not Apple devices:

- **ipad**: 36 in hexadecimal (00110110), repeated *b/8* times
- **opad**: 5C in hexadecimal (01011100), repeated *b/8* times

HMAC security:

- Depends on the cryptographic strength of the embedded one-way hash function
- Makes it harder to find collisions (compared to the underlying hash function) due to the use of the secret key
    - This means HMAC cannot be attacked by generating message/code pairs for the underlying hash function offline because attacker does not know the secret key

### Diffie-Hellman Key Exchange

- Allows two people to exchange a secret value, with math
- Relies on the infeasibility of computing discrete logarithms

See [cryptographic explanation](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange#Cryptographic_explanation):

1. Alice and Bob publicly agree to use a public prime modulus *p = 23* and public prime base *g = 5* (which is a primitive root modulo 23).
2. Alice chooses a secret integer (private key) *a = 4*, then sends Bob her public key *A = gᵃ mod p*
    - *A = 5⁴ mod 23 = 4*
3. Bob chooses a secret integer (private key) *b = 3*, then sends Alice his public key *B = gᵇ mod p*
    - *B = 5³ mod 23 = 10*
4. Alice computes *s = Bᵃ mod p*
    - *s = 10⁴ mod 23 = 18*
5. Bob computes *s = Aᵇ mod p*
    - *s = 4³ mod 23 = 18*
6. Alice and Bob now share a secret (the number 18).

Limitations of Diffie-Hellmain include:

- Only used for key exchange
- It involves expensive exponential operations, so DoS is possible by requesting multiple sessions
- Susceptible to MitM attack if there is no authentication of the participants (fix this by signing the messages, digital signatures, public key certificates)

## Security Protocols

A **protocol** defines rules and conventions, in other words, "the way we do things around here."

Secure communication involves:

- Mutual authentication
- Establishing and exchanging keys
- Agreeing on which cipher suite to use

### Mutual Authentication

Alice and Bob share a key.

1. Alice claims she's Alice
2. Bob sends a challenge value, *R1*
3. Alice encrypts the challenge value using their shared key and sends it back
4. Bob decrypts Alice's response and sees if it matches *R1*
5. Bob says, OK Alice.
6. Alice sends a challenge value, *R2*
7. Bob encrypts *R2* with the shared key and sends it back to Alice
8. Alice decrypts the response and sees if it matches *R2*
9. Alice says, OK Bob.

Mutual authentication depends on:

- Random challenges being unpredictable large numbers
- Challenge values and authentication messages not being repeatable (**replay attack**)
- The shared secret key remaining secret

Simplified mutual authentication can be susceptible to **reflection attack**. Here are the steps of the genuine path, with the sub-points describing the attack:

1. Alice claims she's alice and sends *R1*
    - Charlie can impersonate Alice and send *R1*
2. Bob sends the encrypted *R1* (ciphertext) and his own challenge *R2*
    - Charlie can receive this but doesn't have the key to encrypt *R2*
    - In a new connection, Charlie impersonates Alice and sends *R2* as the first challenge
    - Bob responds with the encrypted *R2*, which Charlie can use in the first connection
3. Alice decrypts the ciphertext and matches it to *R1*, then sends the encrypted *R2*
    - Charlie impersonates Alice, sending the encrypted *R2* from Bob

Defeat the MitM reflection attack by using different keys for Alice and Bob, or odd and even challenge numbers.

Signing or encryption using [public key cryptography](#public-key-certificate-digital-signatures) can also be used for mutual authentication.

### Establishing Session Keys

Asymmetric cryptography is more computationally expensive. You can establish symmetric keys (shared secret) after authentication so that exchanging messages is more efficient.

- A long-term (master) key can be derived from some password
- Master key is used to authenticate and establish a session key
- Session keys are per-session only to limit consequence of exposure

[Diffie-Hellman key exchange](#diffie-hellman-key-exchange) is used for this purpose.

#### Key Distribution Center

A Key Distribution Center (KDC) can be used to scale the sharing of master keys with the help of the Security Service Module (SSM).

If Alice and Bob each share a master key with the KDC:

1. Alice sends a connection request packet to the SSM
2. The SSM asks the KDC for permission to establish the session with Bob
3. KDC approves the connection request and generate and delivers a session key to Alice and Bob's SSMs

- The communication between any SSM and the KDC is encrypted using a master key unique to this SSM and KDC
- Data exchanged between Alice and Bob is encrypted using the secret session key, which is unique to this session

Public key certificates are exchanged through a CA. A certificate can be verified using its public key whether or not the CA is online. See [public key certificate](#public-key-certificate-digital-signatures).

#### Kerberos Protocol

- Provides authentication and access control, session keys
- Uses ticket-granting-tickets, per-day keys to reduce use and transmission of master keys
- Each principal has a master key derived from a password (or configured, if not human)
    - Keys are stored encrypted in KDC

### Public Key Infrastructure (PKI)

The X.509 standard defines a format for public-key certificates used in IPSec, SSL, SET, and more. The standard describes these components:

- Version
- Certificate serial number
- Signature algorithm identifier
- Issuer name
- Period of validity
- Subject name
- Subject's public-key info
- Issuer's unique identifier
- Subject's unique identifier
- Extensions
- Signature

Most commonly these are:

- CA certificate: used only to sign other certificates
- End user certificate: used to verify identities, sign and encrypt data

The PKI standard has these problems:

- Reliance on user to make an informed decision when certificate can't be verified
- Different implementations use different trust stores and present different security views to users
- Assumption that all CAs in the trust store are equally trusted and apply equivalent policies

### IP Security (IPSec)

The main goal is to encrypt or authenticate all traffic at the IP level, below the transport layer. It has these [main protocols](https://en.wikipedia.org/wiki/IPsec#Security_architecture):

> - Authentication Headers (AH) provides connectionless data integrity and data origin authentication for IP datagrams and provides protection against replay attacks.
> - Encapsulating Security Payloads (ESP) provides confidentiality, connectionless data integrity, data origin authentication, an anti-replay service (a form of partial sequence integrity), and limited traffic-flow confidentiality.
> - Internet Security Association and Key Management Protocol (ISAKMP) provides a framework for authentication and key exchange...

AH uses a hash function to authenticate the IP header. It does not provide encryption.

ESP provides authentication, integrity using hash functions, and confidentiality through encryption. It has two modes:

- Transport mode: encrypts and optionally authenticates IP payload (not IP header)
- Tunnel mode: protects the entire IP packet (used for VPN)

An IP can be spoofed unidirectionally, such as for DoS attacks.

IPSec ensures that:

- A router advertisement comes from an authorized router
- A routing update is not forged
- A redirect message comes from the router to which the initial packet was sent

**Sequence number checking** is used to help prevent replay attack in AH or with ESP authentication. A sliding window is used:

- If the sequence number in IPSec header is greater than the largest number of the current anti-replay window, packet is accepted and window is advanced
- If it is smaller, the packet is rejected

#### Internet Key Exchange (IKE)

Key exchange for IPSec standard can be provided by IKE. IKE:

1. Establishes a shared secret using Diffie-Hellman
2. Sets up a Security Association (SA), a logical group of security information describing a one-way relationship between sender and receiver
3. This SA is then used to negotiate further SAs for other services like IPSec

- IKE offers perfect forward secrecy (PFS) if the SA keys established in step 3 are independent of the SA in step 2
- SAs are stored in a Security Association Database (SADB)
    - Each SA has a unique Security Parameter Index (SPI) (or SA ID) in the SADB
    - SPI is included in outgoing packets and used to identify SA associated with a packet
- Policies used to establish SAs are stored in the Security Policy Database (SPD)

If the packet is outgoing, the SPD is checked for the policy.

If the packet is incoming, the SPI is used to look up the SA in the SADB. SPD is checked for matching security measures before the packet is delivered.

### SSL and TLS

You may like to read [What is TLS? Transport Layer Security encryption explained in plain english](https://victoria.dev/blog/what-is-tls-transport-layer-security-encryption-explained-in-plain-english/).

- A TLS session is created using the handshake protocol and defines the cipher suite used by connections in this session
    - Sessions are used to avoid re-negotiating every connection
- A TLS connection is transient and associated with one only session
- TLS doesn't rely on IPSec

Handshake protocol parameters include:

- The highest TLS version understood by the client
- A random 32-bit timestamp and 28 bytes from an RNG
- A session ID
- The cipher suites supported by the client
- The compression methods supported by the client

## WiFi

- Broadcast communications with no inherent physical protection
- Security threats include:
    - Accidental association (user connects to the neighbor's network)
    - Malicious association (fake WiFi access point)
    - Ad hoc networks (P2P networks without central access point)
    - Nontraditional networks (Bluetooth and eavesdropping)
    - Identity theft (MAC spoofing)
    - MitM
    - DoS
    - Network injection

Wireless security measures for securing transmissions include:

- Signal-hiding techniques: hide wireless AP by:
    - Turning off SSID
    - Reducing signal strength
    - Minimizing signal at building exterior
    - Using directional antennas
    - Using signal shielding
- Encryption

### WEP and WPA2

You might like to read [WPA Key, WPA2, WPA3, and WEP Key: Wi-Fi security explained](https://victoria.dev/blog/wpa-key-wpa2-wpa3-and-wep-key-wi-fi-security-explained/).

WEP is insecure. WPA introduced these improvements:

- Access control model based on [IEEE 802.1X](https://en.wikipedia.org/wiki/IEEE_802.1X)
- A flexible authentication protocol based on [EAP](https://en.wikipedia.org/wiki/Extensible_Authentication_Protocol)
- Message integrity check algorithm TKIP, which dynamically generates a new 128-bit key per-packet
- (WPA2) Mandatory support for [CCMP](https://en.wikipedia.org/wiki/CCMP_(cryptography)), based on AES

## Mobile Security

Android vs. iOS Sandbox permissions:

| Android                            | iOS                        |
| ---------------------------------- | -------------------------- |
| App announces permissions required | Apps have same permissions |
| Installation-time approval         | First-use time approval    |
| App may escalate permissions       | No escalation              |

### iOS Security Overview

- The crypto engine and keys are hardware embedded
    - Each device has dedicated AES-256 crypto engine
    - Apple provides device ID (UID) and device group ID (GID) as AES 256 bit keys burned into the silicon (secure enclave)
- App Sandbox isolates apps from each other
- The trusted bootchain, BootROM code is burned into hardware
    - Each step verifies the kernel is properly signed by Apple, and iOS only runs on Apple-validated devices
- Uses Data Protection key hierarchy
    - Uses per-file key, files are assigned with a class
    - The file key is wrapped with the class key, class key is stored in the file's metadata
    - The metadata of all files is encrypted with the File System Key
- Apps have mandatory code signing with Apple-issued certificates
    - There's no dynamic code generation or self-modifying code
    - At run time, signature checks are performed of all executable memory pages to ensure there was no modification since the last update
- App Store has restricted app distribution and developers are verified by Apple to gain certificates
    - App Store apps are reviewed by Apple
- Third-party apps are sandboxed
- ASLR
- Non-executable memory pages (write and execute permissions are mutually exclusive)

### Android Security Overview

- The software stack is based on Linux kernel
- Uses Android Runtime (Dalvik VM), Java-based core libraries wrapping C and C++ libraries
- Application sandbox is essentially provided from the VM
    - UID provides CPU and memory protection enforced by Unix kernel
- Apps are self-signed by developers, no vetting
    - Code signing is used to ensure apps come from the same developer, to facilitate upgrades

## Web Security

You may like to read [SQL injection and XSS: what white hat hackers know about trusting user input](https://victoria.dev/blog/sql-injection-and-xss-what-white-hat-hackers-know-about-trusting-user-input/).

Injection attacks include:

- Cross Site Scripting (XSS): code is injected into a web page and changes that page’s behavior
- SQLi: malicious actors may be able to send SQL commands that affect a SQL DB; see [Bobby Tables](https://xkcd.com/327/)
- Cross-Site Request Forgery (CSRF): a separate external site causes a user to send unauthorized commands to a target site

## Cybercrime Law

US [Computer Fraud and Abuse Act (CFAA)](https://en.wikipedia.org/wiki/Computer_Fraud_and_Abuse_Act) defines criminal sanctions for unauthorized access.

### Intellectual Property

Three types of property (US legal system):

- Real property: land and things attached to them that you can't pick up and move
- Personal property: things you can move, or that you own
- Intellectual property: intangible assets that consist of human knowledge and ideas

Copyrights protect the tangible expressions of an idea. Rights protected against infringement include:

- Reproduction
- Modification
- Distribution
- Public performance
- Public display

Types of patents include:

- Utility: for inventing or discovering any new and useful process, machine, articule of manufacturer, composition of matter, or new and useful improvement
- Design: for inventing a new, original, and ornamental design for an article of manufacture
- Plan: for inventing or discovering and asexually reproducing a distinct and new plant variety

Software, databases, digital content, and algorithms are intellectual property.

RSA was patented by RSA Security from 1983-2000.

The [Digital Millennium Copyright Act (DMCA)](https://www.copyright.gov/dmca/) seeks to protect intellectual property (IP) by defining circumventing anti-piracy functionality as a crime.

Exemptions to the DMCA:

- Fair use
- Reverse engineering
- Encryption research (Ed Felten's reserach on audio watermarking removal)
- Security testing
- Personal privacy

[Digital Rights Management (DRM)](https://en.wikipedia.org/wiki/Digital_rights_management) is not a single standard but refers generally to systems that ensure identification of holders of digital rights. A typical model has these components:

- Content provider (e.g. music record label)
- Distributor (e.g. online shop)
- Consumer (uses the system and pays for the digital license)
- Clearinghouse (pays royalties to content provider, distributor)

## Ethics

Professional **Codes of Conduct** provide guidance and articulate ethical expectations, since ethics cannot be reduced to precise laws or facts. E.g. the ACM, IEEE, and AITP have codes and standards of conduct. Common themes include:

1. Dignity and worth of other people
2. Personal integrity and honesty
3. Responsibility for work
4. Confidentiality of information
5. Public safety, health, welfare
6. Participation in professional societies to improve professional standards
7. The idea that public knowledge and access to technology is equivalent to social power

[The Rules](https://ieeexplore.ieee.org/document/5779006), or Moral Responsibility for Computing Artifacts, are guidelines produced by the Ad Hoc Committee on Responsible Computing. Generally, they say that people who design, develop, or deploy computing artifacts (programs) are responsible (shared responsibility) for the foreseeable effects of doing so.

## Privacy

General privacy policy guidelines address:

- Consent: participants can make informed decisions
- Privacy and confidentiality
- Ownership and authorship: who has the responsibilty for and rights to the data
- Data sharing: data matching and reuse related to the social benefits of research
- Governance and custodianship: oversight and implementation of the management, organization, access, and preservation of data

### OECD Privacy Guidelines

The [OECD Guidelines on the Protection of Privacy and Transborder Flows of Personal Data](https://www.oecd.org/sti/ieconomy/oecdguidelinesontheprotectionofprivacyandtransborderflowsofpersonaldata.htm) address:

- Collection limitation
- Data quality
- Purpose specification
- Use limitation
- Security safeguards
- Openness
- Individual participation:
    - to obtain from a data controller, or otherwise, confirmation of whether or not the data controller has data relating to them;
    - to have communicated to them, data relating to them within a reasonable time; at a charge, if any, that is not excessive; in a reasonable manner; and in a form that is readily intelligible to them;
    - to be given reasons if a request made under (a) and (b) is denied, and to be able to challenge such denial
    - to challenge data relating to them and, if the challenge is successful to have the data erased, rectified, completed, or amended.
- Accountability

## IT Security Controls

**Risk** is the potential for loss computed as the combination of:

- Likelihood that a given threat exploits a vulnerability to an asset
- Magnitude of harmful consequences that result

An equation for calculating risk is: (Probability that threat occurs) * (Cost to organization)

Security control classes include:

- Management Controls (security policies, guidelines, standards)
- Operational Controls (Implementing security policy and standards with mechanisms)
- Technical Controls (Correctly using hardware and software security capabilties)

Each class can include:

- Supportive controls
- Preventative controls (mitigations)
- Detection and recovery controls (detecting and responding to a breach)
