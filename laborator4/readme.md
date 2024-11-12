# 5.1 Purpose of STP

## 5.1.1 Redundancy in Layer 2 Switched Networks
Redundancy provides backup paths to ensure network functionality if one path fails. However, extra paths can lead to problems like loops, where data continuously circulates without reaching its destination. Ethernet networks need a loop-prevention mechanism to avoid these physical and logical Layer 2 loops. A loop-free topology with a single path between any two devices is essential to prevent frame propagation until a link is disrupted and breaks the loop.

---

## 5.1.2 Spanning Tree Protocol (STP)
STP prevents loops by creating a loop-free network. It finds the best path for data and blocks other unnecessary paths, enabling redundancy without causing data loops.

---

## 5.1.3 STP Recalculation
If a network link fails, STP recalculates the optimal data path and adjusts the network to ensure continued data flow.

---

## 5.1.4 Issues with Redundant Switch Links
Without STP, multiple device paths cause loops, overloading switches and degrading network performance. Ethernet lacks a Layer 3 packet-limiting mechanism, making STP essential for loop prevention. A Layer 2 loop can cause MAC table instability, link saturation, and high CPU usage on devices, rendering the network unusable.

---

## 5.1.5 Layer 2 Loops
Without STP, data may get trapped in a loop, circulating the network indefinitely, which overloads the network and causes instability.

---

## 5.1.6 Broadcast Storm
A broadcast storm floods the network with excessive broadcast packets, often caused by hardware issues or Layer 2 loops. In Layer 2, broadcast messages (e.g., ARP requests) can continuously circulate, consuming resources and leading to network crashes. STP blocks certain paths to prevent loops and broadcast storms.

---

## 5.1.7 The Spanning Tree Algorithm (STA)
STP uses an algorithm developed by Radia Perlman to create a loop-free network. It designates a "root bridge" and calculates the best path, blocking any paths that may create loops.

---

# 5.2 STP Operations Overview
STP uses a four-step process to establish a loop-free topology by assigning switch and port roles.

- **Elect the Root Bridge**: Switch with the lowest Bridge ID (BID) is elected as the Root Bridge, serving as a reference for all path calculations.
- **Elect Root Ports**: Non-root switches select the port with the lowest path cost to the root bridge as the Root Port.
- **Elect Designated Ports**: One port per network segment is designated to have the lowest path cost to the root bridge.
- **Elect Alternate (Blocked) Ports**: Ports that aren't Root or Designated are blocked to prevent loops.

---

## 5.2.1 Steps to a Loop-Free Topology
1. **Root Bridge Election**: All switches consider themselves root initially and exchange BPDUs to decide on the root bridge.
2. **Port Roles**: Post Root Bridge election, switches assign each port a role based on its distance to the Root Bridge.

### Bridge Protocol Data Units (BPDUs)
BPDUs carry STP information:
- **Bridge ID (BID)**: Combination of Bridge Priority, Extended System ID, and MAC Address.
- **Root ID**: BID of the Root Bridge.
- **Path Cost**: Used to determine the best path to the Root Bridge.

---

## 5.2.2 Electing the Root Bridge
Switches send BPDUs with their BID; the lowest BID becomes the Root Bridge. If priorities match, the switch with the lowest MAC address wins. Root Bridge serves as the reference for all paths.

---

## 5.2.3 Impact of Default BIDs
Switches typically have a priority of 32768. If priorities are the same, the MAC address determines the Root Bridge. Adjusting switch priority can influence the root bridge selection.

---

## 5.2.4 Determine the Root Path Cost
Each switch calculates the Root Path Cost, the total port cost to reach the Root Bridge. Paths with the lowest cost are preferred, with other paths blocked to avoid loops.

---

## 5.2.5 Elect Root Ports
Non-root switches select the port with the lowest cost to the Root Bridge as the Root Port. If there are multiple paths, the one with the lowest cost becomes the Root Port.

---

## 5.2.6 Elect Designated Ports
For each network segment, one port with the lowest Root Path Cost is designated. Other ports are blocked to avoid loops.

---

# 5.3 Evolution of STP

## 5.3.1 Different Versions of STP
STP has evolved to meet modern network needs. IEEE 802.1D-2004 introduced Rapid STP (RSTP), improving convergence times. Cisco uses PVST+, allowing independent RSTP per VLAN.

---

## 5.3.2 RSTP Concepts
RSTP, IEEE 802.1w, is compatible with 802.1D STP but improves convergence times, allowing ports to transition to a forwarding state quickly.

---

## 5.3.3 RSTP Port States and Roles
STP and RSTP share port roles but handle states differently:
- **STP**: Ports can be Disabled, Blocking, Listening, Learning, or Forwarding.
- **RSTP**: States include Discarding, Learning, and Forwarding.

---

## 5.3.4 PortFast and BPDU Guard
PortFast allows immediate transition to forwarding on access ports, preventing DHCP issues. BPDU Guard prevents loops by shutting down ports upon receiving a BPDU.

---

## 5.3.5 Alternatives to STP
Complex networks have alternatives like Layer 3 switching, MLAG, SPB, and TRILL, which enhance performance and scalability in larger networks.

Here's a simplified overview of EtherChannel and its key components:

---

# 6.1 EtherChannel Operation

## 6.1.1 Link Aggregation
When devices need more bandwidth or redundancy, adding multiple links between them can help. However, **Spanning Tree Protocol (STP)** will block extra links to avoid network loops. EtherChannel allows redundant links without STP blocking them, combining multiple Ethernet links into a single logical link. This boosts fault-tolerance, load sharing, and bandwidth.

---

## 6.1.2 EtherChannel Overview
EtherChannel was created by Cisco to link multiple Ethernet ports between switches into one logical connection, known as a **port channel**. This virtual interface groups multiple physical ports into a single channel.

---

## 6.1.3 Benefits of EtherChannel
- **Simplified Configuration**: Configure the entire EtherChannel instead of each port.
- **Enhanced Bandwidth**: Uses existing ports without requiring expensive upgrades.
- **Load Balancing**: Distributes traffic across the links in the EtherChannel.
- **STP Efficiency**: Sees the EtherChannel as one link, so all ports stay active without STP blocking.
- **Redundancy**: If one link fails, EtherChannel remains operational.

---

## 6.1.4 Implementation Rules
On Catalyst 2960 switches:
- Mix of interface types (e.g., Fast Ethernet and Gigabit Ethernet) in a single EtherChannel isn’t allowed.
- Supports up to 8 ports per EtherChannel, giving up to 8 Gbps bandwidth for Gigabit Ethernet.
- Each port in an EtherChannel must be set consistently on both ends (e.g., trunks must match, same VLAN).
- Supports up to 6 EtherChannels per switch.

---

## 6.1.5 AutoNegotiation Protocols
EtherChannel can form automatically through **Port Aggregation Protocol (PAgP)** or **Link Aggregation Control Protocol (LACP)**, which negotiate the channel setup between switches.

---

## 6.1.6 PAgP Operation
PAgP is a Cisco protocol for automating EtherChannel creation. It monitors for configuration consistency and detects if ports can be grouped. PAgP modes include:
- **On**: Forces channel creation without negotiation.
- **Desirable**: Actively initiates negotiations.
- **Auto**: Passively waits for the other end to initiate.

---

## 6.1.7 PAgP Mode Settings Example
Consider the two switches in the figure. Whether S1 and S2 establish an EtherChannel using PAgP depends on the mode settings on each side of the channel.

![image](https://github.com/user-attachments/assets/3004fbd2-e4a5-401e-855f-1b2e94d7265b)

The table shows the various combination of PAgP modes on S1 and S2 and the resulting channel establishment outcome.

![image](https://github.com/user-attachments/assets/23d449be-0d31-432b-ad8b-0773515d696b)

---

## 6.1.8 LACP Operation
LACP is an IEEE standard for cross-vendor compatibility, similar to PAgP. LACP modes include:
- **On**: Forces a channel without negotiation.
- **Active**: Starts the negotiation.
- **Passive**: Waits to respond to negotiation requests.

Both protocols ensure ports on both sides are compatible, enabling smooth link formation.

---

## 6.1.9 LACP Mode Settings Example

Consider the two switches in the figure. Whether S1 and S2 establish an EtherChannel using LACP depends on the mode settings on each side of the channel.

![image](https://github.com/user-attachments/assets/1d81c5b5-b506-4eec-b0e3-a2a7d89925f1)

The table shows the various combination of LACP modes on S1 and S2 and the resulting channel establishment outcome.

![image](https://github.com/user-attachments/assets/fcecde25-a121-4eb5-b9e9-fd18cff1c99d)


---

# LACP VS PAgP

### 1. **Standardization**
   - **PAgP**: A Cisco-proprietary protocol, meaning it only works between Cisco devices (or some Cisco-compatible equipment).
   - **LACP**: An open standard protocol defined by IEEE (IEEE 802.3ad), which allows it to work across devices from different vendors.

### 2. **Modes of Operation**
   - **PAgP Modes**:
     - **Auto**: The port will automatically negotiate EtherChannel if the other end is in the desirable mode.
     - **Desirable**: Actively initiates the formation of an EtherChannel if the other end is set to auto or desirable mode.
   - **LACP Modes**:
     - **Active**: Actively attempts to negotiate the EtherChannel with the other side.
     - **Passive**: Forms an EtherChannel only if the other end is in active mode. It does not initiate the connection on its own.

### 3. **Compatibility**
   - **PAgP**: Limited to Cisco devices due to its proprietary nature.
   - **LACP**: Works across various vendor devices, making it the preferred choice in multi-vendor environments.

### 4. **Max Number of Links in an EtherChannel**
   - **PAgP**: Can support up to 8 active links.
   - **LACP**: Can support up to 16 links, with 8 active links and 8 in standby mode, ready to become active if one fails.

### 5. **Link Management**
   - **PAgP**: Offers limited monitoring of links.
   - **LACP**: Provides more robust link management. It can dynamically detect link failures and activate standby links if needed.

### Summary

In essence:
- **PAgP** is best suited for networks with Cisco devices only.
- **LACP** is more versatile and preferred for interoperability in networks with devices from multiple vendors.

---

# 6.2 Configure EtherChannel

EtherChannel bundles multiple physical links into a single logical connection for improved bandwidth and redundancy. Here's how to set it up and ensure it functions smoothly:

---

## 6.2.1 Configuration Guidelines

1. **EtherChannel Support:** Ensure all interfaces can support EtherChannel.
2. **Speed & Duplex Matching:** All interfaces in an EtherChannel must have the same speed and duplex settings.
3. **VLAN Match:** All interfaces must be in the same VLAN or be configured as trunks. If they are different, EtherChannel won’t form.
4. **Range of VLANs:** Set parameters (e.g., VLANs, speed) directly on the port channel to apply them across all bundled interfaces.

**Note:** Configurations applied to individual interfaces may cause compatibility issues if they differ from the port channel’s settings.

---

## 6.2.2 Example - Configuring EtherChannel with LACP

To set up EtherChannel using LACP (Link Aggregation Control Protocol), follow these steps:

1. **Select Interfaces:**
   - Use the `interface range` command to select multiple ports simultaneously.
   
2. **Create Port Channel:**
   - Use `channel-group [ID] mode active` to create a port channel in LACP mode.
   
3. **Configure as Trunk:**
   - Set the port channel to trunk mode if necessary, and specify allowed VLANs.

**Example Configuration for S1 (with FastEthernet ports 0/1 and 0/2):**

```shell
S1(config)# interface range FastEthernet 0/1 - 2
S1(config-if-range)# channel-group 1 mode active
Creating a port-channel interface Port-channel 1
S1(config-if-range)# exit
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk allowed vlan 1,2,20
```

In this setup:
- `channel-group 1 mode active` initiates LACP.
- `switchport mode trunk` configures the channel for trunking.
- `switchport trunk allowed vlan 1,2,20` specifies VLANs that the EtherChannel allows.

---

# 6.3 Verify and Troubleshoot EtherChannel

## 6.3.1 Verify EtherChannel

1. **Verify Port Channel Status**:
   - Use the `show interfaces port-channel` command to check the status of the port channel. If the output shows “Port-channel1 is up, line protocol is up (connected),” it indicates that the EtherChannel is operational.

   ```shell
   S1# show interfaces port-channel 1
   Port-channel1 is up, line protocol is up (connected)
   ```

2. **Check EtherChannel Summary**:
   - Use the `show etherchannel summary` command to get a quick overview of the status of all EtherChannel groups on the device. Look for flags that indicate whether the EtherChannel is up or down and if any ports are not correctly bundled.

   ```shell
   S1# show etherchannel summary
   ```

3. **Inspect Port Channel Configuration**:
   - The `show etherchannel port-channel` command displays more specific configuration details for the port channels.
   
4. **View Interfaces within EtherChannel**:
   - The `show interfaces etherchannel` command helps verify the individual status of interfaces within the EtherChannel.

---

## 6.3.2 Common EtherChannel Issues

1. **Mismatched Configurations**:
   - **VLAN Consistency**: Ensure all interfaces in the EtherChannel are assigned to the same VLAN or configured as trunks with identical allowed VLAN ranges.
   - **Trunk Configuration Consistency**: All interfaces should either be trunks or access ports with the same mode.
   - **Dynamic Protocol Configuration**: Make sure PAgP or LACP configurations are compatible on both ends (e.g., both in `active` or `desirable` mode).

2. **Protocol Confusion**:
   - Be careful not to confuse PAgP or LACP (for link aggregation) with DTP (for automating trunk creation).

---

## 6.3.3 Troubleshooting EtherChannel Example

If the EtherChannel is not operational, use these steps to identify and fix the issue:

1. **View EtherChannel Summary**:
   - The `show etherchannel summary` output flags can indicate issues. In the example, the flags for Port-channel 1 show `(SD)`, which indicates that the port is down.

   ```shell
   S1# show etherchannel summary
   Flags:  D - down        P - bundled in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      N - not in use, no aggregation
        f - failed to allocate aggregator
        M - not in use, minimum links not met
        m - not in use, port not aggregated due to minimum links not met
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port
        A - formed by Auto LAG
   Number of channel-groups in use: 1
   Number of aggregators:           1
   Group  Port-channel  Protocol    Ports
   ------+-------------+-----------+-----------------------------------------------
   1      Po1(SD)         -      Fa0/1(D)    Fa0/2(D)
   ```

2. **Check Port Channel Configuration**:
   - Review the configuration with `show run | begin interface port-channel ` to view if there are incompatible PAgP modes configured on S1 and S2.
```
S1# show run | begin interface port-channel
interface Port-channel1
 switchport trunk allowed vlan 1,2,20
 switchport mode trunk
!
interface FastEthernet0/1
 switchport trunk allowed vlan 1,2,20
 switchport mode trunk
 channel-group 1 mode on
!
interface FastEthernet0/2
 switchport trunk allowed vlan 1,2,20
 switchport mode trunk
 channel-group 1 mode on
!======================================
S2# show run | begin interface port-channel
interface Port-channel1
 switchport trunk allowed vlan 1,2,20
 switchport mode trunk
!
interface FastEthernet0/1
 switchport trunk allowed vlan 1,2,20
 switchport mode trunk
 channel-group 1 mode desirable
!
interface FastEthernet0/2
 switchport trunk allowed vlan 1,2,20
 switchport mode trunk
 channel-group 1 mode desirable    
```

3. **Correct Misconfigurations**:
   - Reconfigure any mismatched settings (e.g., VLANs, speed, duplex, or trunking mode).
```
S1(config)# no interface port-channel 1
S1(config)# interface range fa0/1 - 2
S1(config-if-range)# channel-group 1 mode desirable
Creating a port-channel interface Port-channel 1
S1(config-if-range)# no shutdown
S1(config-if-range)# exit
S1(config)# interface port-channel 1
S1(config-if)# switchport mode trunk
S1(config-if)# end
S1# 
```

4. **Verify EtherChannel is Operational**:
   - After adjustments, run `show etherchannel summary` again to confirm the EtherChannel is now active.

```
S1# show etherchannel summary
Flags:  D - down        P - bundled in port-channel
        I - stand-alone s - suspended
        H - Hot-standby (LACP only)
        R - Layer3      S - Layer2
        U - in use      N - not in use, no aggregation
        f - failed to allocate aggregator
        M - not in use, minimum links not met
        m - not in use, port not aggregated due to minimum links not met
        u - unsuitable for bundling
        w - waiting to be aggregated
        d - default port
        A - formed by Auto LAG
Number of channel-groups in use: 1
Number of aggregators:           1
Group  Port-channel  Protocol    Ports
------+-------------+-----------+-----------------------------------------------
1      Po1(SU)         PAgP      Fa0/1(P)    Fa0/2(P)
```

---

Using these steps and commands, you should be able to verify and troubleshoot EtherChannel configurations effectively.
