**10.1 Endpoint Security**  
This section explains how to protect devices in a network, focusing on network and endpoint security.

**10.1.1 Network Attacks Today**  
There are several types of network attacks:
- **DDoS (Distributed Denial of Service)**: Many devices attack a network to overwhelm it and stop access to a website or service.
- **Data Breach**: Attackers steal sensitive data from servers or hosts.
- **Malware**: Malicious software, like ransomware, infects devices, causing damage or holding data hostage until a ransom is paid.

**10.1.2 Network Security Devices**  
To protect a network, certain devices are used:
- **VPN-Enabled Router**: This ensures a secure connection for remote users accessing a network.
- **NGFW (Next-Generation Firewall)**: This firewall monitors and protects the network from external threats.
- **NAC (Network Access Control)**: This device controls who can access the network.

**10.1.3 Endpoint Protection**  
Endpoints like laptops, desktops, servers, and phones are vulnerable to attacks. Protection methods include:
- **NAC**: Helps control network access.
- **AMP (Advanced Malware Protection)**: Protects endpoints from malware.
- **ESA (Email Security Appliance)**: Protects against phishing and malicious email attachments.
- **WSA (Web Security Appliance)**: Protects against web-based threats by controlling access to websites and scanning for malware.

**10.1.4 Cisco Email Security Appliance**  
The Cisco ESA helps protect against email-based threats:
- It blocks known threats and removes malware that bypassed initial detection.
- It can discard emails with dangerous links and encrypt emails to prevent data loss.
- It is updated every 3-5 minutes with the latest threat information.

**10.1.5 Cisco Web Security Appliance**  
The Cisco WSA protects against web threats:
- It controls web traffic, blocking access to harmful websites and filtering URLs.
- It can restrict certain web activities like video or chat, according to company rules.
- It scans for malware and can block harmful websites or encrypt web traffic.

### 10.2 Access Control Simplified

**10.2.1 Authentication with a Local Password**  
The simplest way to access network devices like routers and switches is by using a password. However, this method is not secure because the password is sent in plain text, and anyone who knows the password can access the device. A more secure option is SSH (Secure Shell), which encrypts the username and password, making it harder for attackers to intercept the credentials. You can configure a device to use a local database (passwords stored on the device) or use a central server for authentication.

**10.2.2 AAA Components**  
AAA stands for Authentication, Authorization, and Accounting, which is a way of controlling access to network resources.  
- **Authentication**: Verifies who the user is.
- **Authorization**: Defines what the user can do once logged in.
- **Accounting**: Tracks what the user does during their session (e.g., actions taken, commands run).

**10.2.3 Authentication**  
There are two main ways to authenticate users:  
- **Local Authentication**: The device stores usernames and passwords directly. It's suitable for small networks.
- **Server-Based Authentication**: A central server (using RADIUS or TACACS+) stores the usernames and passwords. This is better for large networks with multiple devices, as it makes management easier.

**10.2.4 Authorization**  
After authentication, the network determines what actions the user is allowed to perform. This is based on attributes assigned to the user. For example, some users may have access to all network services, while others may be restricted.

**10.2.5 Accounting**  
Accounting tracks what authenticated users do on the network, such as the commands they run and how much data they use. This data is useful for auditing or troubleshooting and helps detect malicious actions. It logs when a user starts and finishes their session and records any activities they perform.

**10.2.6 802.1X**  
IEEE 802.1X is a protocol that controls access to a Local Area Network (LAN) by requiring devices to authenticate before being allowed to connect.  
- **Supplicant**: The device that wants to access the network.
- **Authenticator**: The device (usually a switch) that controls access and communicates with the authentication server.
- **Authentication Server**: The server that checks if the device is allowed to connect.

This system ensures that only authorized devices can connect to the network through switch ports or wireless access points.

### 10.3 Layer 2 Security Threats

**10.3.1 Layer 2 Vulnerabilities**  
Layer 2, also known as the Data Link layer, is a critical part of the OSI model, and securing it is essential for protecting the entire network. While most security solutions focus on Layer 3 and above (using firewalls, VPNs, etc.), compromising Layer 2 can undermine the security of all other layers. For example, if a malicious actor gains access to Layer 2 frames (Ethernet frames), they could bypass the security measures at higher layers, leading to potential damage across the network infrastructure. 

**OSI Layers Breakdown**:  
- Layer 7: Application
- Layer 6: Presentation
- Layer 5: Session
- Layer 4: Transport
- Layer 3: Network
- **Layer 2: Data Link**  
- Layer 1: Physical

**10.3.2 Switch Attack Categories**  
Layer 2 security is often considered a weak link because it was traditionally trusted, as networks were under a single organization's control. With the advent of BYOD (Bring Your Own Device) and more advanced attack techniques, LANs are increasingly vulnerable. Various attacks can target Layer 2, compromising the integrity of the network, and these attacks need to be mitigated to protect the overall infrastructure.

Some common Layer 2 attacks include:  
- **MAC Address Spoofing**: Impersonating another device on the network by changing the MAC address.
- **ARP Spoofing**: Manipulating the ARP table to redirect traffic to malicious devices.
- **VLAN Hopping**: Gaining unauthorized access to different VLANs.
- **DHCP Spoofing**: Offering fake IP addresses to clients, causing denial of service or redirecting traffic to malicious servers.

**10.3.3 Switch Attack Mitigation Techniques**  
Cisco provides several solutions to mitigate Layer 2 attacks. Effective mitigation depends on securing both the management protocols and the Layer 2 infrastructure:

**Key Mitigation Strategies**:  
1. **Secure Management Protocols**:  
   - Use secure versions of protocols like SSH, Secure Copy Protocol (SCP), and Secure FTP (SFTP), instead of their unencrypted counterparts (e.g., Telnet, FTP).
   - Implement SSL/TLS for secure communication.
   
2. **Out-of-Band Management**:  
   - Use a dedicated management network that does not interact with the production network, reducing exposure to threats.

3. **Dedicated Management VLAN**:  
   - Isolate management traffic into its own VLAN, ensuring that only authorized management devices can access it.

4. **Access Control Lists (ACLs)**:  
   - Implement ACLs to filter unwanted access to critical network resources, protecting the devices from unauthorized management or data flows.

These techniques help prevent or mitigate attacks by securing Layer 2 traffic and ensuring that management protocols are not exploited by attackers.

### 10.4 MAC Address Table Attack

**10.4.1 Switch Operation Review**  
A Layer 2 switch uses a **MAC address table** to store MAC addresses associated with each port. This table helps the switch make forwarding decisions by identifying the appropriate port for incoming frames. When a switch receives a frame, it checks the source MAC address, learns it, and stores it in the MAC address table. If the table does not have an entry for a destination MAC address, the switch floods the frame to all ports in the VLAN.  

Example of a MAC address table output:

```
S1# show mac address-table dynamic
          Mac Address Table
-------------------------------------------
Vlan    Mac Address       Type        Ports
----    -----------       --------    -----
   1    0001.9717.22e0    DYNAMIC     Fa0/4
   1    000a.f38e.74b3    DYNAMIC     Fa0/1
   1    0090.0c23.ceca    DYNAMIC     Fa0/3
   1    00d0.ba07.8499    DYNAMIC     Fa0/2
S1#
```

**10.4.2 MAC Address Table Flooding**  
MAC address flooding is a common attack that exploits the fixed size of a switch's MAC address table. In a flooding attack, the attacker sends a large number of frames with random source MAC addresses to overwhelm the table. Once the table is full, the switch cannot store additional MAC addresses and treats all incoming frames as unknown unicast. It then floods all traffic to all ports in the VLAN.

This makes the attacker capable of capturing all frames within the local VLAN. The attack is contained within the local LAN or VLAN, meaning the attacker can only capture traffic within their network segment.

The **macof** tool can be used to flood the MAC address table with random addresses, causing the switch to flood all traffic.

**Attack Example**:  
- The attacker, connected to VLAN 10, runs **macof** to generate bogus frames with random MAC addresses.
- As **macof** continues to send these frames, the switch's MAC address table becomes full, and the switch starts flooding frames to all ports.
- The attacker can then capture these frames, including potentially sensitive data, using packet sniffing tools.

**10.4.3 MAC Address Table Attack Mitigation**  
MAC address flooding attacks can be extremely rapid, as tools like **macof** can send thousands of bogus frames per second. A powerful switch like the **Catalyst 6500** can hold up to 132,000 MAC addresses, and with an attack tool like **macof**, the table can be filled within seconds.

These attacks can not only disrupt the local switch but also affect other interconnected Layer 2 switches, as the flooding propagates through the network.

**Mitigation Strategies**:
1. **Port Security**:  
   The primary mitigation against MAC address table flooding is **port security**. This feature limits the number of MAC addresses that can be learned on a specific switch port. Once the limit is reached, the switch can either:
   - Block further learning of MAC addresses.
   - Drop incoming frames from unknown MAC addresses.

2. **Other Recommendations**:
   - **Monitor MAC Address Table**: Regularly check the MAC address table for unusual activity, such as a large number of learned addresses on a single port.
   - **Implement VLAN Segmentation**: Limiting the scope of VLANs reduces the number of devices in each broadcast domain, reducing the effectiveness of a flooding attack.
   - **Use Dynamic ARP Inspection**: Protect against ARP spoofing, which could further exploit MAC address table attacks.

By implementing these strategies, network administrators can significantly reduce the risk of MAC address flooding attacks and enhance the security of Layer 2 networks.

The topic of LAN attacks you presented covers several critical vulnerabilities and their respective mitigations. Here's a summarized breakdown of the key points for each attack type:

### 1. **VLAN Hopping Attacks**
   - **Description**: An attacker can make traffic from one VLAN accessible to another, bypassing the need for a router. This is achieved by spoofing 802.1Q and Dynamic Trunking Protocol (DTP) signaling to establish an unauthorized trunk link.
   - **Mitigation**:
     - Disable trunking on access ports.
     - Disable auto trunking on trunk links.
     - Ensure the native VLAN is used only for trunk links.

### 2. **VLAN Double-Tagging Attack**
   - **Description**: The attacker sends a frame with two 802.1Q tags, the first matching the native VLAN of the trunk port and the second directing traffic to the victim VLAN.
   - **Mitigation**:
     - Disable trunking on all access ports.
     - Manually configure trunk links.

### 3. **DHCP Attacks**
   - **DHCP Starvation**: The attacker floods the network with DHCP requests to exhaust the IP address pool, preventing legitimate clients from obtaining an address.
   - **DHCP Spoofing**: A rogue DHCP server provides false configuration details (e.g., wrong gateway, DNS, or IP address).
   - **Mitigation**: Implement DHCP snooping to prevent unauthorized DHCP servers.

### 4. **ARP Attacks**
   - **Description**: The attacker sends spoofed ARP messages to associate its MAC address with the IP of another device (often the default gateway), intercepting network traffic.
   - **Mitigation**: Implement Dynamic ARP Inspection (DAI) to validate ARP messages and prevent ARP spoofing.

### 5. **Address Spoofing Attacks**
   - **IP and MAC Spoofing**: The attacker forges the IP or MAC address to impersonate another device, redirecting traffic intended for the original device.
   - **Mitigation**: Implement IP Source Guard (IPSG) and MAC address filtering.

### 6. **STP Attack**
   - **Description**: Attackers manipulate the Spanning Tree Protocol (STP) to become the root bridge of the network, capturing all traffic in the switched domain.
   - **Mitigation**: Use Root Guard or BPDU Guard to prevent unauthorized devices from becoming the root bridge.

Each attack type has unique ways of exploiting network vulnerabilities, but with proper configurations like DHCP snooping, DAI, and root guard, the risks can be significantly mitigated. These practices help ensure network integrity and prevent malicious actors from compromising the LAN.

The provided information covers various aspects of port security in networking, specifically on Cisco switches, and details how to implement it to secure network ports from unauthorized access or attacks. Here’s a summary of the steps and commands involved:

### 11.1.1 Secure Unused Ports
To mitigate security risks, it's important to disable unused switch ports. The following commands can disable unused ports:

- **Disable individual ports**: 
  ```bash
  Switch(config)# interface fa0/8 - 24
  Switch(config-if-range)# shutdown
  ```
  This command disables ports Fa0/8 to Fa0/24 on the switch.

### 11.1.2 Mitigate MAC Address Table Attacks
Port security is an effective method for preventing MAC address table overflow attacks. It allows the administrator to limit the number of MAC addresses allowed on a port, preventing unauthorized devices from gaining network access.

### 11.1.3 Enable Port Security
To enable port security on an access port, the following commands should be used:

1. **Set port to access mode**:
   ```bash
   Switch(config)# interface fa0/1
   Switch(config-if)# switchport mode access
   ```

2. **Enable port security**:
   ```bash
   Switch(config-if)# switchport port-security
   ```

   If the port is already dynamically configured, it must be set to access mode first.

### 11.1.4 Limit and Learn MAC Addresses
To set the maximum number of MAC addresses allowed:

- **Set maximum MAC addresses**:
  ```bash
  Switch(config-if)# switchport port-security maximum 2
  ```

You can configure MAC addresses in three ways:
1. **Manually configured**: Assign specific MAC addresses.
2. **Dynamically learned**: The switch learns the MAC addresses and secures them.
3. **Sticky MAC addresses**: The switch dynamically learns MAC addresses and saves them to the running configuration.

### 11.1.5 Port Security Aging
Aging allows for the automatic removal of old secure MAC addresses:

- **Set aging type and time**:
  ```bash
  Switch(config-if)# switchport port-security aging time 10
  Switch(config-if)# switchport port-security aging type inactivity
  ```

This configuration sets a 10-minute inactivity timeout for secure addresses.

### 11.1.6 Port Security Violation Modes
If a security violation occurs (e.g., when a port receives a MAC address that is not allowed), you can configure the violation mode. The available modes are:
- **Protect**: Discards frames from unauthorized MAC addresses.
- **Restrict**: Drops frames and generates a log message.
- **Shutdown** (default): Puts the port into an error-disabled state.

Example of configuring violation mode:
```bash
Switch(config-if)# switchport port-security violation restrict
```

### 11.1.7 Ports in Error-Disabled State
When the violation mode is set to `shutdown`, a security violation causes the port to be placed in an error-disabled state, preventing any traffic from passing through.

To recover from an error-disabled state:
- **Check logs for errors** (e.g., `%PORT_SECURITY-2-PSECURE_VIOLATION`).
- **Manually re-enable the port**:
  ```bash
  Switch(config-if)# shutdown
  Switch(config-if)# no shutdown
  ```

This procedure helps maintain control over ports and secure your network infrastructure.

This section outlines steps to mitigate VLAN hopping attacks. Here's a summary of the key points:

### 11.2.1 VLAN Attacks Review:
VLAN hopping attacks allow attackers to bypass VLAN isolation by gaining unauthorized access to different VLANs. These attacks can be carried out in the following ways:

1. **Spoofing DTP messages**: An attacker sends spoofed Dynamic Trunking Protocol (DTP) messages to trigger trunking on a switch, allowing them to send traffic tagged with a different VLAN.
2. **Introducing a rogue switch**: An attacker connects a rogue switch to the network and enables trunking to access all VLANs on the victim switch.
3. **Double-tagging (double-encapsulation)**: This method exploits the way switches process VLAN tags, allowing an attacker to send a double-tagged packet to access a different VLAN.

### 11.2.2 Steps to Mitigate VLAN Hopping Attacks:
The following steps can be implemented to protect against VLAN hopping attacks:

1. **Disable DTP on non-trunking ports**: Prevent automatic trunking negotiations on access ports by setting them to access mode with the `switchport mode access` command.
   
2. **Disable unused ports**: Shut down unused ports and assign them to a non-functional VLAN to avoid potential access points for attackers.

3. **Manually enable trunking on trunk ports**: Use the `switchport mode trunk` command to manually configure trunk ports and disable automatic trunk negotiation.

4. **Disable DTP on trunking ports**: Use the `switchport nonegotiate` command to disable DTP on trunk ports, preventing automatic trunk negotiation.

5. **Change the native VLAN**: Set the native VLAN to something other than VLAN 1 with the `switchport trunk native vlan vlan_number` command. This helps avoid attacks that exploit the default VLAN 1.

### Example Configuration:
```plaintext
S1(config)# interface range fa0/1 - 16
S1(config-if-range)# switchport mode access
S1(config-if-range)# exit
S1(config)#
S1(config)# interface range fa0/17 - 20
S1(config-if-range)# switchport mode access
S1(config-if-range)# switchport access vlan 1000
S1(config-if-range)# shutdown
S1(config-if-range)# exit
S1(config)#
S1(config)# interface range fa0/21 - 24
S1(config-if-range)# switchport mode trunk
S1(config-if-range)# switchport nonegotiate
S1(config-if-range)# switchport trunk native vlan 999
S1(config-if-range)# end
S1#
```

### Breakdown:
- **Ports 0/1 to 0/16**: These are set to access mode to ensure they don’t automatically form trunks.
- **Ports 0/17 to 0/20**: These are shut down and assigned to an unused VLAN (VLAN 1000).
- **Ports 0/21 to 0/24**: These are manually configured as trunk ports, DTP is disabled, and the native VLAN is set to VLAN 999 to avoid using the default VLAN 1.

These configurations help protect the network from VLAN hopping attacks by controlling port access, disabling unnecessary trunking negotiations, and changing default settings.

### 11.3 Mitigate DHCP Attacks

#### 11.3.1 DHCP Attack Review:
A **DHCP starvation attack** aims to deny service to legitimate clients by exhausting the DHCP pool of available IP addresses. This is often done with tools like **Gobbler**, which sends DHCP requests using unique source MAC addresses for each request, flooding the DHCP server.

To mitigate **DHCP starvation attacks**, **port security** can be employed because it prevents MAC address spoofing by limiting the number of MAC addresses that can be learned on a port. However, **DHCP spoofing attacks** (where a rogue DHCP server provides incorrect IP configurations to clients) cannot be mitigated by port security alone. In this case, the attacker can use a legitimate MAC address but spoof the DHCP payload.

To prevent **DHCP spoofing**, **DHCP snooping** is recommended. It helps by monitoring DHCP messages, distinguishing between trusted and untrusted sources, and preventing rogue DHCP servers from operating on the network.

#### 11.3.2 DHCP Snooping:
DHCP snooping works by differentiating **trusted** and **untrusted** ports based on the network topology. Trusted sources, such as legitimate DHCP servers and switches, are explicitly configured, while untrusted sources, such as client devices or rogue DHCP servers, are treated with caution.

- **Trusted Ports**: Typically used for connections to legitimate DHCP servers and trunk links.
- **Untrusted Ports**: Ports connected to client devices are typically considered untrusted, and any DHCP message from these ports is scrutinized.

The **DHCP snooping binding table** stores MAC-to-IP address mappings from untrusted ports, helping to prevent attacks where rogue DHCP servers provide false configurations to clients.

#### 11.3.3 Steps to Implement DHCP Snooping:
To implement DHCP snooping, follow these steps:

1. **Enable DHCP snooping**: Activate the feature globally using `ip dhcp snooping`.
2. **Configure trusted ports**: For ports connected to trusted DHCP servers or trunks, use `ip dhcp snooping trust`.
3. **Limit DHCP traffic on untrusted ports**: Use the `ip dhcp snooping limit rate` command to set the maximum number of DHCP discovery messages allowed per second.
4. **Enable DHCP snooping by VLAN**: You can enable DHCP snooping for specific VLANs using the `ip dhcp snooping vlan` command.

#### 11.3.4 DHCP Snooping Configuration Example:
Here’s an example of configuring DHCP snooping on **S1**:

1. Enable DHCP snooping globally:
   ```bash
   S1(config)# ip dhcp snooping
   ```

2. Mark the interface to the DHCP server as trusted:
   ```bash
   S1(config)# interface f0/1
   S1(config-if)# ip dhcp snooping trust
   S1(config-if)# exit
   ```

3. Configure a rate limit for DHCP messages on untrusted ports (e.g., ports F0/5 to F0/24):
   ```bash
   S1(config)# interface range f0/5 - 24
   S1(config-if-range)# ip dhcp snooping limit rate 6
   S1(config-if-range)# exit
   ```

4. Enable DHCP snooping for specific VLANs (e.g., VLANs 5, 10, 50-52):
   ```bash
   S1(config)# ip dhcp snooping vlan 5,10,50-52
   S1(config)# end
   ```

#### 11.3.5 Verifying DHCP Snooping Configuration:
To verify that DHCP snooping is working as expected, use the following commands:

- To check DHCP snooping status:
  ```bash
  S1# show ip dhcp snooping
  ```

- To view the binding table:
  ```bash
  S1# show ip dhcp snooping binding
  ```

**Example Output**:
```bash
S1# show ip dhcp snooping
Switch DHCP snooping is enabled
DHCP snooping is configured on following VLANs:
5,10,50-52
...
DHCP snooping trust/rate is configured on the following Interfaces:
Interface                  Trusted    Allow option    Rate limit (pps)
-----------------------    -------    ------------    ----------------  
FastEthernet0/1            yes        yes             unlimited
FastEthernet0/5            no         no              6        

S1# show ip dhcp snooping binding
MacAddress         IpAddress       Lease(sec) Type          VLAN Interface
------------------ --------------- ---------- ------------- ---- --------------------
00:03:47:B5:9F:AD  192.168.10.11   193185     dhcp-snooping 5    FastEthernet0/5
```

### Notes:
- **DHCP Snooping** is a critical part of network security and can be paired with **Dynamic ARP Inspection (DAI)**, which is discussed next, to further protect against attacks like **man-in-the-middle**.

- **11.4 Mitigate ARP Attacks**

**11.4.1 Dynamic ARP Inspection (DAI)**

In an ARP attack, a malicious actor can send unsolicited ARP requests to other hosts in the network, spoofing the MAC address of the attacker with the IP address of the default gateway. This can result in ARP poisoning, where traffic meant for the legitimate gateway is sent to the attacker. To prevent ARP spoofing and poisoning, switches must ensure that only valid ARP requests and replies are relayed.

Dynamic ARP Inspection (DAI) mitigates ARP attacks by:

- Blocking invalid or gratuitous ARP requests from being relayed across ports within the same VLAN.
- Intercepting all ARP requests and replies from untrusted ports.
- Verifying each intercepted ARP packet for a valid IP-to-MAC binding.
- Dropping and logging ARP requests from invalid sources to prevent ARP poisoning.
- Error-disabling the interface if the configured limit for ARP packets is exceeded.

**11.4.2 DAI Implementation Guidelines**

To mitigate ARP spoofing and poisoning, the following guidelines should be followed:

- **Enable DHCP snooping globally**: This is essential as DAI requires the DHCP snooping binding table to validate ARP packets.
- **Enable DHCP snooping on selected VLANs**: This ensures the binding table is available for ARP inspection.
- **Enable DAI on selected VLANs**: Enabling DAI specifically on VLANs that are at risk.
- **Configure trusted interfaces**: Interfaces that are directly connected to a DHCP server or router should be marked as trusted for both DHCP snooping and ARP inspection.
- **Default untrusted interfaces**: All access ports on switches should be treated as untrusted unless explicitly configured otherwise.

In the sample topology, trusted ports are generally those connecting switches to routers or other switches, while untrusted ports are typically the ports connecting end-user devices.

**11.4.3 DAI Configuration Example**

In the given example, **S1** is a switch connecting two PCs (PC-A and PC-B) on VLAN 10, and a router (R1) for upstream connectivity. DAI will be configured on **S1** to prevent ARP spoofing and poisoning attacks.

Steps for configuration:

1. Enable **DHCP snooping** globally and for VLAN 10:
   ```bash
   S1(config)# ip dhcp snooping
   S1(config)# ip dhcp snooping vlan 10
   ```

2. Enable **ARP Inspection** on VLAN 10:
   ```bash
   S1(config)# ip arp inspection vlan 10
   ```

3. Configure the uplink port (Fa0/24) to the router as trusted for both **DHCP snooping** and **ARP inspection**:
   ```bash
   S1(config)# interface fa0/24
   S1(config-if)# ip dhcp snooping trust
   S1(config-if)# ip arp inspection trust
   ```

4. To validate both source and destination MAC addresses, as well as IP addresses, enable DAI checks:
   ```bash
   S1(config)# ip arp inspection validate src-mac dst-mac ip
   ```

This command ensures that ARP packets are checked for consistency between the MAC addresses in the Ethernet header and the ARP body, as well as for invalid IP addresses (e.g., `0.0.0.0`, `255.255.255.255`, or multicast IPs).

To verify DAI configuration and operational status, you can use the following commands:

```bash
S1# show ip arp inspection
S1# show ip arp inspection statistics
```

This ensures that all ARP packets are inspected, invalid ones are dropped, and interfaces with excessive invalid ARP packets are error-disabled.

**DAI configuration helps secure the network against ARP poisoning by ensuring only legitimate ARP requests and replies are processed, reducing the risk of unauthorized traffic interception.**

**11.5 Mitigate STP Attacks**

**11.5.1 PortFast and BPDU Guard**

Spanning Tree Protocol (STP) is vulnerable to manipulation by attackers who may spoof the root bridge, which can cause network topology changes. To prevent such attacks, **PortFast** and **BPDU Guard** can be implemented.

- **PortFast**: This feature allows a port configured as an access port to immediately enter the forwarding state, bypassing the usual STP listening and learning states. PortFast should only be applied to ports connected to end-user devices (e.g., PCs, printers), as it speeds up the connection process for these devices.
  
- **BPDU Guard**: BPDU Guard disables a port if it receives a Bridge Protocol Data Unit (BPDU), which typically indicates a switch is connected to the port. This helps protect against accidental or malicious connection of switches or bridges to an access port.

In the network diagram, **PortFast** and **BPDU Guard** should be configured on access ports (such as ports F0/1 to F0/24) to prevent unnecessary delays and ensure that any unauthorized BPDUs result in the immediate shutdown of the port.

**11.5.2 Configure PortFast**

PortFast bypasses the STP listening and learning states, which helps minimize the time it takes for access ports to come online. However, it should only be enabled on ports connected to end devices because enabling it on a port connected to another switch could cause a **spanning-tree loop**.

- To enable **PortFast** on a specific port:
  ```bash
  S1(config)# interface fa0/1
  S1(config-if)# switchport mode access
  S1(config-if)# spanning-tree portfast
  ```
  A warning will appear, advising that PortFast should only be enabled on ports connected to a single host to avoid creating potential network loops.

- To enable **PortFast** globally on all access ports:
  ```bash
  S1(config)# spanning-tree portfast default
  ```
  Again, a warning will be issued to ensure that PortFast is not inadvertently enabled on ports connected to hubs, switches, or bridges.

- To verify **PortFast** settings:
  ```bash
  S1# show running-config | begin span
  ```
  This command shows the configuration related to **PortFast**.

**11.5.3 Configure BPDU Guard**

Even when **PortFast** is enabled, the interface will still listen for BPDUs. If a BPDU is received on a PortFast-enabled port, it could indicate an unauthorized switch connection. In such a case, the port will be placed into an **error-disabled** state.

- To enable **BPDU Guard** on an individual port:
  ```bash
  S1(config)# interface fa0/1
  S1(config-if)# spanning-tree bpduguard enable
  S1(config-if)# exit
  ```

- To enable **BPDU Guard** globally on all **PortFast**-enabled ports:
  ```bash
  S1(config)# spanning-tree portfast bpduguard default
  ```

- To check the status of **BPDU Guard** and other spanning tree settings:
  ```bash
  S1# show spanning-tree summary
  ```
  This command will show that **PortFast** and **BPDU Guard** are enabled by default.

By implementing **PortFast** and **BPDU Guard** on all access ports, you can protect the network from potential STP attacks and prevent unauthorized switches from affecting the topology.

**Key Notes**:
- Always enable **BPDU Guard** on all **PortFast**-enabled ports to avoid the risk of accidental switch connections.
- Ensure **PortFast** is used only on ports leading to end-user devices to avoid network loops when connecting to other network devices like switches or hubs.
