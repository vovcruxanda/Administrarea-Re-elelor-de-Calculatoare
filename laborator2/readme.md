## 3.1 Overview of VLANs
VLANs (Virtual Local Area Networks) allow you to organize devices on a network into smaller groups. Instead of using physical connections, VLANs use logical connections, making it easier to manage and secure networks.

### 3.1.1 VLAN Definitions
VLANs segment your network into smaller sections, like creating separate rooms in a house. Each VLAN can group devices (e.g., IT, HR, Sales) together, even if they are on different floors or physical switches. Devices within a VLAN communicate as if they are connected to the same cable, and each VLAN is treated as its own small network. 

Packets (data) are only sent to devices within the same VLAN, reducing unnecessary traffic to other VLANs. For example, a broadcast sent in one VLAN won't be seen by devices in another VLAN.

### 3.1.2 Benefits of VLAN Design
Each VLAN in a network has its own IP network, making it easier to manage addresses and data flow. By assigning specific IP ranges to each VLAN (e.g., Faculty, Students, Guests), the network becomes more organized. VLANs reduce the amount of broadcast traffic, improving network efficiency.

| Benefit      | Description    |
|--------------|--------------|
| Smaller broadcast domain | Dividing a network into VLANs reduces the number of devices in the broadcast domain. |
| Improved security | Only users in the same VLAN can communicate together. Only users in the same VLAN can communicate without the services of a router. The router may have a security feature such as an access control list to restrict communications between VLANs. | 
| Improved IT efficiency | VLANs simplify network management because users with similar network requirements can be configured on the same VLAN. VLANs can be named to make them easier to identify. | 
| Reduced cost | VLANs reduce the need for expensive network upgrades and use the existing bandwidth and uplinks more efficiently, resulting in cost savings. | 
| Better performance | Smaller broadcast domains reduce unnecessary traffic on the network and improve performance. | 
| Simpler project and application management | VLANs aggregate users and network devices to support business or geographic requirements. Having separate functions makes managing a project or working with a specialized application easier; an example of such an application is an e-learning development platform for faculty. | 


### 3.1.3 Types of VLANs
There are several types of VLANs with specific functions:

- **Default VLAN**: All devices are automatically assigned to VLAN 1 unless changed.
    • All ports are assigned to VLAN 1 by default.
    • The native VLAN is VLAN 1 by default.
    • The management VLAN is VLAN 1 by default.
    • VLAN 1 cannot be renamed or deleted.
    `show vlan brief` output shows the all ports assigned to the default VLAN1.
- **Data VLAN**: Used for general data traffic.
    They are referred to as user VLANs because they separate the network into groups of users or devices. 
- **Native VLAN**: Special VLAN that handles untagged traffic between devices.
- **Management VLAN**: Reserved for network management (like controlling switches), including SSH, Telnet, HTTPS, HTTP, and SNMP.
- **Voice VLAN**: Separates voice traffic (e.g., phones) to prioritize calls. VoIP traffic requires the following:
    • Assured bandwidth to ensure voice quality
    • Transmission priority over other types of network traffic
    • Ability to be routed around congested areas on the network
    • Delay of less than 150 ms across the network

VLAN 1 is the default for all Cisco switches, and all ports are assigned to it initially. However, using VLAN 1 for management and native VLAN is a security risk. Therefore, it’s better to configure these functions on different VLANs.

## 3.2 VLANs in a Multi-Switched Environment

### 3.2.1 Defining VLAN Trunks
- **VLAN Trunks** allow VLAN traffic to travel between switches, connecting devices in the same VLAN across different switches without using a router.
- A **trunk** is a point-to-point link between two devices that carries multiple VLANs using the IEEE 802.1Q protocol.
- In Cisco switches, all VLANs are supported on a trunk port by default, enabling the transmission of traffic for multiple VLANs, such as VLANs 10, 20, 30, and the native VLAN 99.

### 3.2.2 Network without VLANs
- In a network without VLANs, a broadcast sent by a host (e.g., PC1) is forwarded out of all ports on the switch, meaning that all devices in the network receive it, as the network is a single broadcast domain.

### 3.2.3 Network with VLANs
- VLANs segment a network by associating switch ports with specific VLANs. For example, PCs in **VLAN 10** (Faculty) only communicate with other PCs in VLAN 10, and broadcasts within VLAN 10 are only forwarded to VLAN 10 devices.
- **Trunk ports** are configured to support all VLANs in the network, ensuring communication between VLANs across multiple switches.

### 3.2.4 VLAN Identification with a Tag
- VLAN tagging is used to identify which VLAN an Ethernet frame belongs to when transmitted over a trunk. This is done using the IEEE 802.1Q standard, which inserts a 4-byte VLAN tag in the Ethernet frame header.
- The tag includes:
  - **Type** - A 2-byte value called the tag protocol ID (TPID) value. For Ethernet, it is set to hexadecimal 0x8100.
  - **User priority** - A 3-bit value that supports level or service implementation.
  - **Canonical Format Identifier (CFI)** - A 1-bit identifier that enables Token Ring frames to be carried across Ethernet links.
  - **VLAN ID (VID)** - A 12-bit VLAN identification number that supports up to 4096 VLAN IDs.

### 3.2.5 Native VLANs and 802.1Q Tagging
- **Native VLANs** (default is VLAN 1) are used for untagged traffic on a trunk link.
  #### Tagged Frames on the Native VLAN
- Some devices add a **VLAN tag** to traffic meant for the **native VLAN**, but this is a problem for Cisco switches.
- **Native VLAN traffic** (like management or control traffic) should be **untagged**.
- If a switch gets a tagged frame with the same **VLAN ID** as the native VLAN (e.g., VLAN 1), it **drops the frame**.
- Make sure devices (like IP phones, routers, etc.) don’t send tagged frames on the native VLAN when connected to Cisco switches.
  #### Untagged Frames on the Native VLAN
- **Untagged frames** sent to a switch on a trunk port are forwarded to the **native VLAN**.
- If there are no devices in the native VLAN, these frames will be **dropped**.
- The **default native VLAN** is **VLAN 1**, but it can be changed for security reasons.
- The **Port VLAN ID (PVID)** handles untagged traffic. If VLAN 99 is set as the native VLAN, all untagged traffic will be sent to VLAN 99.

In short, make sure that only untagged traffic goes to the native VLAN to avoid issues, and configure other devices carefully.

### 3.2.6 Voice VLAN Tagging
- **Voice VLANs** are required for VoIP (Voice over IP) to ensure that voice traffic receives the necessary priority and quality of service (QoS).
  - A Cisco IP phone can use a switch port configured with two VLANs—one for **voice traffic** and another for **data traffic** from a connected PC.
  - The switch instructs the phone to tag voice traffic with a specific VLAN (e.g., VLAN 150 for voice).

### 3.2.7 Voice VLAN Verification Example
- The command `show interfaces fa0/18 switchport` reveals the configuration of VLANs on the switch port, showing the **data VLAN (VLAN 20)** and the **voice VLAN (VLAN 150)**.

This configuration allows the segmentation of network traffic based on its purpose (data or voice), enhancing both performance and security.

## 3.3 VLAN Configuration

### 3.3.1 VLAN Ranges on Catalyst Switches
- **VLAN Types:**
  - **Normal Range VLANs:** IDs 1 to 1005.
  - **Extended Range VLANs:** IDs 1006 to 4094.
  
- **Normal Range VLANs:**
  - Used in small to medium businesses.
  - IDs 1 and 1002-1005 (for legacy networks) are created automatically and can't be deleted.
  - Stored in `vlan.dat` in the switch flash memory and synchronized with VTP.

- **Extended Range VLANs:**
  - Used for service providers or very large networks.
  - They are identified by a VLAN ID between 1006 and 4094.
  - Requires VTP transparent mode and stores configuration in running-config.

### 3.3.2 VLAN Creation Commands
- To create a VLAN:
  ```bash
  S1(config)# vlan [vlan_id]          //Create a VLAN with a valid ID number
  S1(config-vlan)# name [vlan_name]   // Specify a unique name to identify the VLAN
  ```

- Example:
  ```bash
  S1(config)# vlan 20
  S1(config-vlan)# name student
  ```

### 3.3.4 VLAN Port Assignment Commands
The switchport mode access command is optional, but strongly recommended as a security best practice. With this command, the interface changes to strictly access mode.
- Assigning ports to VLANs requires the following commands:
  ```bash
  S1(config)# interface [interface_id]                //Enter interface configuration mode
  S1(config-if)# switchport mode access               //Set the port to access mode
  S1(config-if)# switchport access vlan [vlan_id]     //Assign the port to a VLAN
  ```

- Example to assign port Fa0/6 to VLAN 20:
  ```bash
  S1(config)# interface fa0/6
  S1(config-if)# switchport mode access
  S1(config-if)# switchport access vlan 20
  ```
 Use the interface range command to simultaneously configure multiple interfaces.

### 3.3.6 Data and Voice VLANs
- Ports can be assigned to both a **data VLAN** and a **voice VLAN**.
- Example for VLAN 20 (data) and VLAN 150 (voice):
  ```bash
  S3(config)# vlan 20
  S3(config)# vlan 150
  S3(config)# interface fa0/18
  S3(config-if)# switchport mode access
  S3(config-if)# switchport access vlan 20
  S3(config-if)# switchport voice vlan 150
  ```
  Use the mls qos trust [cos | device cisco-phone | dscp | ip-precedence] interface configuration command to set the trusted state of an interface, and to indicate which fields of the packet are used to classify traffic.

### 3.3.8 Verifying VLANs
- Verify VLANs with:
  ```bash
  S1# show vlan brief
  ```

- Verify a specific port's VLAN assignment:
  ```bash
  S1# show interfaces fa0/18 switchport
  ```

  ![image](https://github.com/user-attachments/assets/af110046-dd27-4559-a9ba-d84783989703)


### 3.3.10 Deleting VLANs
- To delete a specific VLAN:
  ```bash
  S1(config)# no vlan [vlan_id]
  ```

- To delete all VLAN configurations and reset the switch:
  ```bash
  S1# delete vlan.dat
  S1# erase startup-config
  S1# reload
  ```

## 3.4 VLAN Trunks
### 3.4.1 Trunk Configuration Commands
A VLAN trunk is a Layer 2 link between two switches that carries traffic for all VLANs by default unless restricted. To enable trunking on an interface (e.g., `F0/1`), you use the following commands:

1. **Switchport mode trunk:** Enables the trunking mode on the interface.
2. **Switchport trunk native vlan [VLAN ID]:** Configures the native VLAN.
3. **Switchport trunk allowed vlan [list]:** Restricts the allowed VLANs to those specified.

4. ![image](https://github.com/user-attachments/assets/313f6f0a-acec-4b7f-8e3d-bbd99a3c4ed0)


### 3.4.2 Trunk Configuration Example
The configuration involves enabling a trunk link between two switches (S1 and S2) and allowing traffic from specific VLANs (10, 20, 30, and 99). The native VLAN is VLAN 99. The following configuration is applied to switch S1 on port `F0/1`:

```bash
S1(config)# interface fastEthernet 0/1
S1(config-if)# switchport mode trunk
S1(config-if)# switchport trunk native vlan 99
S1(config-if)# switchport trunk allowed vlan 10,20,30,99
S1(config-if)# end
```

The command `switchport mode trunk` turns port `F0/1` into a trunk. The native VLAN is set to 99, and the allowed VLANs are 10, 20, 30, and 99.

### 3.4.3 Verifying Trunk Configuration
To verify that the configuration is successful, the `show interfaces fa0/1 switchport` command provides detailed information. Some of the key outputs include:

- **Administrative Mode:** Shows the interface is configured as a trunk.
- **Operational Mode:** The interface is operating in trunk mode.
- **Native VLAN:** Verifies that the native VLAN is set to 99.
- **Allowed VLANs:** Lists the VLANs enabled for trunking (10, 20, 30, 99).

### 3.4.4 Resetting the Trunk to Default
If needed, you can reset the trunk configuration to its default state, where all VLANs are allowed, and the native VLAN is set back to VLAN 1.

```bash
S1(config)# interface fa0/1
S1(config-if)# no switchport trunk allowed vlan
S1(config-if)# no switchport trunk native vlan
S1(config-if)# end
```

Running the `show interfaces fa0/1 switchport` command again will confirm that the trunk settings have reverted to default values (native VLAN 1, allowing all VLANs).

Finally, the trunk can be disabled entirely by setting the interface back to access mode:

```bash
S1(config)# interface fa0/1
S1(config-if)# switchport mode access
S1(config-if)# end
``` 

This returns the port to static access mode, restricting it to a single VLAN (VLAN 1 by default).

### 3.5 Dynamic Trunking Protocol (DTP)

#### 3.5.1 Introduction to DTP
Dynamic Trunking Protocol (DTP) is a proprietary Cisco protocol that automates the negotiation of trunking between Cisco switches. DTP facilitates quick and efficient configuration of trunk links by allowing switches to automatically determine if they should operate in trunk or access mode.

- **Trunking Modes:** An interface can be set to:
  - **Trunking:** Always trunk.
  - **Nontrunking:** Always in access mode.
  - **Negotiation:** Attempt to negotiate trunking with a neighbor interface.

DTP is enabled by default on Cisco Catalyst 2960 and 3650 Series switches. However, it only works if the neighboring switch port also supports DTP. If connected to non-Cisco devices, or devices that do not support DTP, it is best practice to disable DTP to prevent misconfigurations.

To manually set an interface to trunk without DTP negotiation, use:
```bash
S1(config-if)# switchport mode trunk
S1(config-if)# switchport nonegotiate
```
To re-enable DTP, use:
```bash
S1(config-if)# switchport mode dynamic auto
```

If both ends of a trunk are set to `switchport mode dynamic auto`, the ports will not negotiate a trunk and will remain in access mode, leading to an inactive trunk link. Hence, it is often recommended to set trunk ports explicitly to trunk mode for clarity.

#### 3.5.2 Negotiated Interface Modes
The `switchport mode` command can take several options, as shown below:

```bash
Switch(config-if)# switchport mode { access | dynamic { auto | desirable } | trunk }
```
- **Access:** The port is set to access mode.
- **Dynamic Auto:** The port will negotiate trunking if the neighboring port is set to trunking mode.
- **Dynamic Desirable:** The port actively attempts to negotiate trunking.
- **Trunk:** The port is forced into trunk mode.

To prevent DTP negotiation, the command `switchport nonegotiate` is used. This command can only be applied if the port is set to either access or trunk mode, requiring manual configuration on the neighboring interface to establish a trunk link.

#### 3.5.3 Results of a DTP Configuration
The results of DTP configuration options are typically summarized in a table showing the states of the interfaces on either side of a trunk link. It's advisable to configure trunk links statically whenever possible to avoid any negotiation issues.

#### 3.5.4 Verify DTP Mode
To check the current DTP mode of an interface, the following command can be used:

```bash
S1# show dtp interface fa0/1
```
The output will provide detailed information about the DTP configuration, including:

- **Current mode:** Indicates whether the port is in access or trunk mode.
- **Neighbor information:** Displays information about the neighboring device.
- **FSM state:** Provides the current state of the Finite State Machine (FSM) regarding DTP.

**Example Output:**
```
DTP information for FastEthernet0/1:
TOS/TAS/TNS: ACCESS/AUTO/ACCESS
TOT/TAT/TNT: NATIVE/NEGOTIATE/NATIVE
Neighbor address 1: C80084AEF101
Neighbor address 2: 000000000000
...
FSM state: S2:ACCESS
Enabled: yes
In STP: no
```

#### Best Practices
- **For trunk links:** Configure interfaces to `trunk` mode and use `nonegotiate` to avoid any DTP frame generation, ensuring clarity in trunk configurations.
- **For access links:** Disable DTP to prevent unwanted trunk negotiations on interfaces not intended to be trunk ports.

This approach helps maintain a more predictable and stable network environment by reducing the potential for misconfigurations related to automatic trunk negotiation.
