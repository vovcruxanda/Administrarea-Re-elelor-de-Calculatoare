# 8. SLAAC and DHCPv6

## 8.1 IPv6 GUA Assignment

### 8.1.1 IPv6 Host Configuration
To connect a device (like a router or computer) to an IPv6 network, it needs an address. This can be done:
1. **Manually**: You enter an IPv6 address and other settings yourself, which can be slow and prone to errors.
2. **Automatically**: Most devices are set to automatically get an address, using either SLAAC (Stateless Address Autoconfiguration) or DHCPv6.

### 8.1.2 IPv6 Host Link-Local Address
- **Link-Local Addresses** are automatically created for local network communication (between devices on the same network).
- Every device gets this address when it starts up and connects to a network, even if there’s no router providing additional network info.
- Sometimes, you’ll see a `%` and a number next to the address (e.g., `%21`). This is a **Zone ID** and helps the device know which specific interface the address belongs to.

### 8.1.3 IPv6 GUA (Global Unicast Address) Assignment
IPv6 makes it easy for devices to get a unique, globally accessible address without needing manual configuration. This is done by routers automatically broadcasting their IPv6 settings to connected devices. There are two main types:

1. **Stateless** (no device tracks which address each device has):
   - **SLAAC Only**: The router gives all the info needed, and the device creates its own unique address.
   - **SLAAC with DHCPv6 Server**: The router provides some info, and a DHCPv6 server provides extra settings (like DNS info).
2. **Stateful** (a DHCPv6 server manages the address assignment):
   - **Stateful DHCPv6**: The device gets all its network info from the DHCPv6 server, except for the gateway, which it gets from the router.

### 8.1.4 Three RA (Router Advertisement) Message Flags
Routers send out messages with flags that tell devices how to get their IPv6 addresses. There are three flags:

1. **A (Address Autoconfiguration)**: Use SLAAC to get an IPv6 address.
2. **O (Other Configuration)**: Get extra settings from a DHCPv6 server.
3. **M (Managed Address Configuration)**: Get a full IPv6 address and other info from a DHCPv6 server.

These flags can be combined in various ways to help devices know how to get their IPv6 addresses and other settings.

---

## 8.2 SLAAC

### **8.2.1 SLAAC Overview**
- **Purpose**: SLAAC (Stateless Address Autoconfiguration) allows devices to self-generate unique IPv6 addresses without needing a DHCPv6 server.
- **Stateless**: No server tracks addresses; devices use their own logic.
- **How It Works**: Routers send periodic messages (RA - Router Advertisement) with necessary info. Devices use these to configure their IPv6 addresses.
- **Initiation**: Devices can request these messages by sending RS (Router Solicitation).


### **8.2.2 Enabling SLAAC**
- **Network Setup**: A router (R1) sends address info to connected devices (e.g., PC1) via RA messages.
- **Router Configuration**: IPv6 routing must be enabled on the router, which then advertises its network prefix (e.g., `2001:db8:acad:1::/64`).
- **Result**: Devices automatically configure themselves using the advertised prefix.

### **8.2.3 SLAAC Only Method**
- **Default Behavior**: When IPv6 routing is enabled, routers broadcast RA messages.
- **Flags in RA**:  
  - `A=1`: Devices can auto-configure their own IPv6 addresses.  
  - `O=0, M=0`: No DHCP server is needed; all info comes from RA.
- **How Devices Respond**: Devices create their own unique IPv6 addresses using the prefix from RA and a generated interface ID.

### **8.2.4 ICMPv6 RS Messages**
- **Communication**:  
  - Devices send an RS (Router Solicitation) message to request configuration info.  
  - Routers reply with RA (Router Advertisement) messages, providing the prefix and other settings.
- **Process**: When a device starts, it sends an RS to `ff02::2` (all routers). The router replies with an RA containing the prefix.

### **8.2.5 Host Process to Generate Interface ID**
- **What Happens**:  
  - Devices generate a unique interface ID (second part of the IPv6 address) using one of two methods:
    1. **Randomly Generated**: Ensures privacy and uniqueness.
    2. **EUI-64 Method**: Modifies the device’s MAC address to form the ID.
- **Why Random**: Modern systems prefer random IDs to avoid exposing the MAC address, enhancing privacy.

### **8.2.6 Duplicate Address Detection (DAD)**
- **Purpose**: Ensures no two devices on the network use the same IPv6 address.
- **How It Works**:  
  - Device sends a special message (NS - Neighbor Solicitation) to check if its address is already in use.
  - If no response, the address is unique and usable.  
  - If there’s a response, the device picks a new ID and tries again.
- **Importance**: While unlikely, duplication is checked to avoid conflicts.

--- 


## 8.3 DHCPv6

### **8.3.1 DHCPv6 Operation Steps**
1. **Host Sends RS (Router Solicitation):**  
   - The host sends a multicast RS message to IPv6-enabled routers to discover network configuration information.

2. **Router Sends RA (Router Advertisement):**  
   - The router responds with an RA message containing flags and configuration instructions (e.g., SLAAC or DHCPv6).

3. **Host Sends DHCPv6 SOLICIT Message:**  
   - The host solicits a DHCPv6 server for additional configuration.

4. **Server Responds with ADVERTISE Message:**  
   - The DHCPv6 server advertises its availability.

5. **Host Sends REQUEST (or CONFIRM) Message:**  
   - The host formally requests the configuration.

6. **Server Sends REPLY:**  
   - The DHCPv6 server sends the requested configuration details (e.g., addresses, DNS information).

### **8.3.2 Stateless DHCPv6**
- **Definition:**  
  Provides supplementary network configuration (e.g., DNS server) without assigning IP addresses.
- **Key Flags in RA:**  
  - **A=1:** Enables SLAAC for address autoconfiguration.  
  - **O=1:** Indicates that other configuration (e.g., DNS) is available from a DHCPv6 server.  
- **Configuration Command:**  
  ```bash
  R1(config-if)# ipv6 nd other-config-flag
  ```

### **8.3.3 Enable Stateless DHCPv6**
- **Steps:**  
  - Set the **O flag to 1** to notify clients about available configuration from a stateless DHCPv6 server.
  - Use the `no ipv6 nd other-config-flag` command to revert to default SLAAC-only operation.


### **8.3.4 Stateful DHCPv6**
- **Definition:**  
  Provides all addressing and configuration details, including IPv6 addresses, managed by the DHCPv6 server.  
  The client does not rely on SLAAC.
- **Key Flags in RA:**  
  - **M=1:** Directs clients to use stateful DHCPv6 for all configuration, including addresses.  
  - **A=0:** Disables SLAAC.
- **Configuration Command:**  
  ```bash
  R1(config-if)# ipv6 nd managed-config-flag
  R1(config-if)# ipv6 nd prefix default no-autoconfig
  ```


### **8.3.5 Enable Stateful DHCPv6**
- **Steps:**  
  1. Set the **M flag to 1** using `ipv6 nd managed-config-flag`.
  2. Disable SLAAC by setting **A flag to 0** using `ipv6 nd prefix default no-autoconfig`.


### **Comparison: Stateless vs. Stateful DHCPv6**
| Feature                | Stateless DHCPv6         | Stateful DHCPv6         |
|------------------------|--------------------------|--------------------------|
| **IP Address Management** | Not provided (uses SLAAC) | Fully managed by DHCPv6 server |
| **RA Flags**            | A=1, O=1                | A=0, M=1                |
| **Use Case**            | Supplementary info (e.g., DNS) | Complete address and config management |
| **Server State Tracking** | No                     | Yes                     |

These features allow network administrators to optimize IPv6 network configuration for diverse scenarios, ensuring flexibility and efficiency.



## 8.4 Configure DHCPv6 Server

Configuring DHCPv6 on Cisco routers involves enabling the router to act as a **server**, **client**, or **relay agent**. Here's a breakdown of the process based on the provided content:


### **1. Configuring a Stateless DHCPv6 Server**
A **stateless DHCPv6 server** provides additional configuration parameters (like DNS server or domain name) while relying on **SLAAC (Stateless Address Autoconfiguration)** for IP addressing.

#### Steps:
1. **Enable IPv6 Routing**:  
   ```plaintext
   R1(config)# ipv6 unicast-routing
   ```

2. **Define a DHCPv6 Pool Name**:  
   ```plaintext
   R1(config)# ipv6 dhcp pool DHCP-STATELESS
   ```

3. **Configure the DHCPv6 Pool** (e.g., DNS server and domain name):  
   ```plaintext
   R1(config-dhcpv6)# dns-server 2001:4860:4860::8888
   R1(config-dhcpv6)# domain-name example.com
   ```

4. **Bind the Pool to an Interface**:  
   Assign the DHCPv6 pool and enable RA messages.
   ```plaintext
   R1(config)# interface g0/0/1
   R1(config-if)# ipv6 nd other-config-flag
   R1(config-if)# ipv6 dhcp server DHCP-STATELESS
   ```

5. **Verify Configuration**:
   ```plaintext
   R1# show ipv6 dhcp pool
   R1# show ipv6 interface g0/0/1
   ```


### **2. Configuring a Stateless DHCPv6 Client**
A **stateless DHCPv6 client** receives additional parameters from the server but uses SLAAC for its IP.

#### Steps:
1. **Enable IPv6 Routing**:  
   ```plaintext
   R3(config)# ipv6 unicast-routing
   ```

2. **Configure the LLA (Link-Local Address)**:  
   ```plaintext
   R3(config-if)# ipv6 address fe80::3 link-local
   ```

3. **Enable SLAAC**:  
   ```plaintext
   R3(config-if)# ipv6 address autoconfig
   ```

4. **Verify SLAAC and Additional Configuration**:  
   ```plaintext
   R3# show ipv6 interface g0/0/1
   R3# show ipv6 dhcp binding
   ```



### **3. Configuring a Stateful DHCPv6 Server**
A **stateful DHCPv6 server** assigns both IPv6 addressing and additional parameters.

#### Steps:
1. **Enable IPv6 Routing**:  
   ```plaintext
   R1(config)# ipv6 unicast-routing
   ```

2. **Define a DHCPv6 Pool**:
   ```plaintext
   R1(config)# ipv6 dhcp pool DHCP-STATEFUL
   ```

3. **Configure the DHCPv6 Pool** (e.g., address prefix, DNS, and domain):  
   ```plaintext
   R1(config-dhcpv6)# address prefix 2001:db8:acad:1::/64
   R1(config-dhcpv6)# dns-server 2001:4860:4860::8888
   R1(config-dhcpv6)# domain-name example.com
   ```

4. **Bind the Pool to an Interface**:
   ```plaintext
   R1(config)# interface g0/0/1
   R1(config-if)# ipv6 dhcp server DHCP-STATEFUL
   ```

5. **Verify Configuration**:
   ```plaintext
   R1# show ipv6 dhcp pool
   R1# show ipv6 dhcp binding
   ```


### **4. Configuring a Stateful DHCPv6 Client**
A **stateful DHCPv6 client** receives both an IPv6 address and additional configuration from the server.

#### Steps:
1. **Enable IPv6 Routing**:  
   ```plaintext
   R3(config)# ipv6 unicast-routing
   ```

2. **Configure the LLA**:  
   ```plaintext
   R3(config-if)# ipv6 address fe80::3 link-local
   ```

3. **Enable DHCPv6**:  
   ```plaintext
   R3(config-if)# ipv6 address dhcp
   ```

4. **Verify the Address and Additional Configuration**:  
   ```plaintext
   R3# show ipv6 interface g0/0/1
   R3# show ipv6 dhcp binding
   ```


### **5. Configuring a DHCPv6 Relay Agent**
A **DHCPv6 relay agent** forwards requests from clients to a remote DHCPv6 server.

#### Steps:
1. **Configure the Relay Agent**:  
   ```plaintext
   R1(config)# interface g0/0/1
   R1(config-if)# ipv6 dhcp relay destination 2001:db8:acad:1::2 G0/0/0
   ```

2. **Verify Configuration**:
   ```plaintext
   R1# show ipv6 dhcp interface
   R1# show ipv6 dhcp binding
   ```
