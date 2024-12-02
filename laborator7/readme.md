### **14.1 Path Determination**
Path determination is how a router decides the best way to send a packet to its destination.

---

### **14.1.1 Two Functions of a Router**
1. **Path Selection**: Routers find the best route for sending a packet by checking their routing table.
2. **Forwarding Packets**: Once the route is chosen, the router sends the packet toward the next stop, which might be another router or the final device.

---

### **14.1.2 Router Functions Example**
Routers use their routing tables like maps to decide the best path for a packet. For example:
- A packet travels from one computer in LAN1 through Router 1 (R1) and Router 2 (R2) to reach another computer in LAN2.
- Both routers look at the destination address, consult their routing tables, and forward the packet accordingly.

---

### **14.1.3 Best Path Equals Longest Match**
- **Longest Match**: Routers compare the packet's destination address with entries in their routing table.
- They select the route where the most initial bits match. This is called the "longest match" and ensures the most specific and accurate path is chosen.

---

### **14.1.4 IPv4 Address Longest Match Example**
- A packet destined for **172.16.0.10** matches multiple routes in the router’s table:
  - **172.16.0.0/12**  
  - **172.16.0.0/18**  
  - **172.16.0.0/26**
- The router picks **172.16.0.0/26** because it has the longest match (most matching bits).

---

### **14.1.5 IPv6 Address Longest Match Example**
- For a packet destined for **2001:db8:c000::99**:
  - Route **2001:db8:c000::/40** has 40 matching bits.
  - Route **2001:db8:c000::/48** has 48 matching bits (the longest match) and is chosen.
  - Route **2001:db8:c000:5555::/64** is ignored because it doesn’t match enough bits.

---

### **14.1.6 Build the Routing Table**
A router builds its routing table by learning about networks in three ways:

1. **Directly Connected Networks**: These are networks linked directly to the router's active interfaces (e.g., devices connected to the router's ports).
2. **Remote Networks**: These are networks reachable through other routers.
3. **Default Route**: If no specific route exists, the router uses a "catch-all" route, often pointing to the internet or a larger network.

---

### **Summary of Key Concepts in Packet Forwarding**

#### **1. Packet Forwarding Decision Process**
- **Ingress Interface:** A data link frame encapsulating an IP packet arrives at the router.
- **Destination Analysis:** The router checks the destination IP address in the packet header.
- **Routing Table Lookup:** It searches for the **longest matching prefix** in the IP routing table.
  - **Longest match** refers to the route entry with the most specific match for the destination IP.
- **Forwarding or Dropping:**
  - If a match is found:
    - The router encapsulates the packet into a data link frame and forwards it via the **egress interface.**
    - The destination can be:
      - A device on a directly connected network.
      - A **next-hop router**.
  - If no match (including no default route), the packet is dropped.

#### **2. Packet Delivery on Directly Connected Networks**
- **Ethernet Encapsulation for IPv4:**
  - Router checks the **ARP table** for the MAC address of the destination.
  - If no entry exists:
    - Sends an ARP request and receives an ARP reply.
  - The packet is then forwarded in an Ethernet frame with the correct MAC address.
- **Ethernet Encapsulation for IPv6:**
  - Router checks the **neighbor cache** for the MAC address.
  - If no entry exists:
    - Sends an ICMPv6 **Neighbor Solicitation (NS)** and receives a Neighbor Advertisement (NA).
  - The IPv6 packet is forwarded with the correct MAC address.

#### **3. End-to-End Packet Forwarding**
- **Dynamic Encapsulation at Each Hop:** 
  - The packet format changes as it traverses different networks and media types.
  - Example scenario:
    - **PC1 (192.168.1.10)** sends to **PC2 (192.168.4.10)** via routers R1, R2, and R3.
    - The packet's encapsulation changes at each hop based on the egress interface protocol.

#### **4. Packet Forwarding Mechanisms**
- **Process Switching:**
  - **Old method:** Each packet is individually processed by the CPU.
  - Inefficient, as the routing decision is recalculated for every packet.
- **Fast Switching:**
  - The first packet's routing decision is cached, and subsequent packets reuse this information.
  - More efficient than process switching.
- **Cisco Express Forwarding (CEF):**
  - Precomputes all potential routes and stores them in a structured table.
  - Highly efficient and used in modern networks.

### **Network Path Example**
For PC1 (192.168.1.10) to send data to PC2 (192.168.4.10):
1. PC1 checks ARP cache for R1's MAC (default gateway).
2. R1 determines the next hop (R2) using its routing table.
3. At R2, the next hop is determined as R3.
4. At R3, the packet is forwarded directly to PC2 (on a directly connected network).

#### **Encapsulation Changes:**
- On Ethernet links: Encapsulated in Ethernet frames.
- On Serial links: Encapsulated with PPP, HDLC, or another Layer 2 protocol.

#### **Analogy for Packet Forwarding Mechanisms:**
- **Process Switching:** Solve math problems from scratch every time.
- **Fast Switching:** Solve once, then reuse the answer.
- **CEF:** Prepare all answers beforehand in a table.

---

Here’s a simplified explanation of each point from the section "14.3 Basic Router Configuration Review":

---

### **14.3.1 Topology**
1. **Purpose of a Router**: A router decides where to forward data (packets) by creating a routing table.
2. **Network Layout**: The topology includes 3 routers (R1, R2, R3) connected to 4 switches (S1 to S4). 
   - Each switch connects to PCs (PC1 to PC4) in separate networks.
   - R1 connects to switches S1 and S2, R2 connects to switches S3 and S4.
   - Serial connections link R1 to R2 and R2 to an ISP.

3. **Addressing**:
   - IPv4 and IPv6 dual-stack addresses are used (e.g., 10.0.1.0/24 and 2001:db8:acad:1::/64).
   - Gateways for each subnet are assigned with `.1` and `::1`.
4. **Key Connections**:
   - R1's connection to R2 uses serial ports.
   - The ISP connection also uses a serial interface, with specific IPv4 and IPv6 addresses.

---

### **14.3.2 Configuration Commands**
1. **Router Configuration Basics**:
   - Enter privileged mode (`enable`) and configuration mode (`configure terminal`).
   - Assign a hostname (`hostname R1`).
   - Set passwords for console and remote access (e.g., `password cisco`).
   - Enable encryption of passwords (`service password-encryption`).
   - Set a banner message to warn unauthorized users.
2. **Interface Configuration**:
   - Configure each interface with an IP address (IPv4 and IPv6), description, and enable the interface (`no shutdown`).
   - Example for LAN1 (GigabitEthernet 0/0/0):
     - IPv4: `10.0.1.1 255.255.255.0`
     - IPv6: `2001:db8:acad:1::1/64`
   - Use link-local addresses (e.g., `fe80::1:a`).
3. **Save Configuration**:
   - Save settings to startup configuration using `copy running-config startup-config`.

---

### **14.3.3 Verification Commands**
1. **Common Commands**:
   - **`show ip interface brief`**: Quick overview of interface statuses and IPs.
   - **`show running-config interface`**: View the active configuration for an interface.
   - **`show ip route`**: Displays the routing table.
   - **`ping`**: Tests connectivity to other devices.
   - IPv6 versions of commands replace `ip` with `ipv6`.
2. **Usage**:
   - Helps to verify setup, ensure interfaces are up, and troubleshoot issues.

---

### **14.3.4 Filter Command Output**
1. **Output Filtering**:
   - Filters help focus on specific information in command outputs.
2. **Parameters**:
   - **`section`**: Shows sections starting with a keyword.
   - **`include`**: Displays lines containing a specific keyword.
   - **`exclude`**: Hides lines with a keyword.
   - **`begin`**: Shows output starting from a matching line.
3. **Example**:
   - `show ip interface brief | include Gigabit` filters lines containing “Gigabit”.

---

### Overview of Routing Concepts in the Provided Context

The discussion elaborates on how routers handle the task of forwarding packets using **routing tables**. Below is a breakdown of the key concepts and practical implications:

---

#### **1. Routing Table Sources**
A router builds its routing table using:
1. **Directly connected networks**: Interfaces with assigned IP addresses and activated.
2. **Static routes**: Manually configured paths between devices.
3. **Dynamic routing protocols**: Automatically shared routes using protocols like OSPF.

The topology includes routers (R1, R2) and multiple switches (S1-S4), each representing subnetworks connected via Gigabit or Serial interfaces. The use of IPv4 and IPv6 addresses highlights a dual-stack configuration.

---

#### **2. Routing Table Codes**
Routing table entries use codes to indicate the route source:
- **L (Local)**: IP address assigned to the router's interface.
- **C (Connected)**: Networks directly accessible through the router's interfaces.
- **O (OSPF)**: Dynamically learned routes using the OSPF protocol.
- **S (Static)**: Manually configured static routes.

**Example from R1's Table**:
- `C 10.0.1.0/24` → Directly connected to `GigabitEthernet0/0/0`.
- `O 10.0.4.0/24` → Dynamically learned via OSPF from `R2`.

---

#### **3. Static vs. Dynamic Routing**
- **Static Routing**:
  - Advantages: Enhanced security, minimal resource use.
  - Disadvantages: No automatic adaptation to topology changes.
- **Dynamic Routing**:
  - Adapts automatically but consumes more resources (CPU and bandwidth).

---

#### **4. Stub Networks**
A **stub network** is reachable via a single path, often through a stub router. For example:
- `R1` is a stub router for `10.0.1.0/24` and `10.0.2.0/24`, with traffic routed via `R2`.

---

#### **5. Routing Table Principles**
To ensure effective communication:
1. Routes must be shared correctly between routers.
2. Paths must have consistent configurations.
3. Destination devices need accurate IP routing.

---

#### **6. Interpreting Routing Table Entries**
For IPv4, an OSPF route entry like:
```
O 10.0.4.0/24 [110/50] via 10.0.3.2, 00:24:22, Serial0/1/1
```
- **O**: Learned via OSPF.
- **10.0.4.0/24**: Destination network.
- **[110/50]**: Administrative distance (110) and metric (50).
- **10.0.3.2**: Next-hop address.
- **Serial0/1/1**: Exit interface.

Similarly, IPv6 entries include similar parameters, adjusted for the IPv6 format.

---

#### **Practical Application**
- **For Network Administrators**:
  - Use routing table insights to diagnose connectivity issues.
  - Understand OSPF's operation, including metrics and administrative distances.
- **In the Given Topology**:
  - R1 and R2 must exchange OSPF updates to ensure all routes (e.g., to `10.0.4.0/24`) are accessible.
  - Static routes can be used for backup paths or default routes (e.g., R2 to ISP).

---

### Summary of Dynamic Routing Protocols

#### **Dynamic Routing Protocol Overview**
Dynamic routing protocols enable routers to automatically share network information and maintain routing tables. They perform tasks such as:
1. **Network Discovery**: Automatically discovering other routers' networks using the same protocol.
2. **Topology Updates**: Dynamically updating routing tables to reflect changes in the network topology.

Advantages:
- Automatically discovers the best path.
- Adjusts routes when topology changes.

**Network Discovery Example:**
Routers R1 and R2 communicate using a dynamic routing protocol (e.g., OSPF), exchanging information about their respective networks. For example:
- R1 learns about networks connected to R2 (e.g., `10.0.4.0/24`, `10.0.5.0/24`).
- Similarly, R2 learns about R1's connected networks.

#### **Dynamic Routing Table Entries**
Using OSPF, routing tables dynamically update with new network paths:
- IPv4 example:
  ```
  O 10.0.4.0/24 [110/50] via 10.0.3.2, Serial0/1/1
  O 10.0.5.0/24 [110/50] via 10.0.3.2, Serial0/1/1
  ```
- IPv6 example:
  ```
  O 2001:DB8:ACAD:4::/64 [110/50] via FE80::2:C, Serial0/1/1
  O 2001:DB8:ACAD:5::/64 [110/50] via FE80::2:C, Serial0/1/1
  ```

#### **Default Routes**
A **default route** acts as a catch-all route for traffic destined to unknown networks. 
- Configured as `0.0.0.0/0` for IPv4 or `::/0` for IPv6.
- Can be static or dynamically learned.

**Example:**
R2 advertises its static default route (`0.0.0.0/0`) to R1 using OSPF. This allows R1 to forward traffic for unknown networks to R2.

- R2 routing table example:
  ```
  S* 0.0.0.0/0 [1/0] via 209.165.200.226
  S  ::/0 [1/0] via 2001:DB8:FEED:224::2
  ```

#### **IPv4 Routing Table Structure**
- **Classful Organization**: Routing tables follow a structure from early IPv4 classful addressing.
- Entries are formatted based on longest-prefix matching.
  
**Example:**
```
192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C    192.168.1.0/24 is directly connected, GigabitEthernet0/0
L    192.168.1.1/32 is directly connected, GigabitEthernet0/0
O    192.168.2.0/24 [110/65] via 192.168.12.2, Serial0/0/0
```

### Key Takeaways
1. **Dynamic Routing Protocols** like OSPF automate route sharing and update routing tables based on network changes.
2. **Default Routes** simplify routing for unknown destinations, particularly in networks with a single exit point.
3. IPv4 and IPv6 use distinct methods for route determination, with IPv6 relying on link-local addresses for next-hop determination.

---

**14.5 Static and Dynamic Routing Overview**

### **14.5.1 Static or Dynamic Routing**
- **Static Routing**:
  - Manually configured by network administrators.
  - Ideal for specific scenarios:
    - Default route to an ISP.
    - Isolated routes outside dynamic protocols.
    - Explicitly defined paths for security or routing control.
    - Stub networks with only one path to an external network.
  - Suitable for smaller, controlled networks.

- **Dynamic Routing**:
  - Uses algorithms to manage routes automatically.
  - Benefits include:
    - Scalability for large networks.
    - Automatic adaptation to topology changes.
    - Efficiency in networks with multiple routers.
  - Common in diverse and evolving networks.

| Feature | Static Routing | Dynamic Routing |
|---------|----------------|-----------------|
| Configuration | Manual | Automatic |
| Adaptability | Fixed paths | Adjusts to topology changes |
| Use Case | Small/stub networks | Large/scalable networks |

---

### **14.5.2 Evolution of Dynamic Routing Protocols**
- Dynamic routing protocols began in the 1980s with **RIP (RIPv1)**, evolving through protocols like **OSPF**, **IS-IS**, and **EIGRP**.
- **BGP** serves as the primary protocol for inter-domain routing between ISPs.
- Timeline highlights the progression from early protocols (EGP, RIP) to modern IPv6-compatible versions (OSPFv3, IS-ISv6).

| Protocol | Year Introduced | Use Case |
|----------|-----------------|----------|
| RIPv1 | 1988 | Small networks |
| OSPFv2 | 1991 | Interior Gateway |
| BGP | 1995 | Internet routing |
| OSPFv3 | 1999 | IPv6 support |

---

### **14.5.3 Dynamic Routing Protocol Concepts**
Dynamic protocols manage:
- **Remote network discovery**.
- **Routing table updates** for path optimization.
- **Best path selection** based on metrics.
- Automatic re-routing when a path fails.

Core components:
1. **Data Structures**: Tables or databases in RAM.
2. **Messages**: Protocol communication for updates and maintenance.
3. **Algorithm**: Calculates best paths based on metrics.

---

### **14.5.4 Best Path Selection**
- Dynamic protocols calculate the **best path** using **metrics** (e.g., hop count, bandwidth).
- **Lowest metric** value determines the preferred route.
- Multiple metrics can be combined for complex networks.

| Protocol | Metric Used |
|----------|-------------|
| RIP | Hop count |
| OSPF | Bandwidth |
| EIGRP | Composite (bandwidth, delay) |

If the primary path fails, the protocol selects an alternative.

---

### **14.5.5 Load Balancing**
- **Equal Cost Load Balancing**:
  - Distributes traffic across multiple paths with identical metrics.
  - Increases performance and reliability.
- **Unequal Cost Load Balancing**:
  - Supported only by **EIGRP** for specific use cases.

Animation example:
- PC1 to PC2 traffic routes through multiple equal-cost paths, demonstrating improved network efficiency.

---

### Overview of Static Routing Configuration

Static routing is used to manually define paths in a network for traffic flow. Below is an explanation and step-by-step guide based on the topology and details provided.

---

#### **Types of Static Routes**
1. **Standard Static Route**: Routes a specific destination.
2. **Default Static Route**: Directs packets when no other route is available.
3. **Floating Static Route**: A backup route with a higher administrative distance.
4. **Summary Static Route**: A single route summarizing multiple networks.

---

#### **Configuring IPv4 Static Routes**
1. **Command Syntax**:
   ```bash
   ip route <network-address> <subnet-mask> {<next-hop-ip> | <exit-interface>} [distance]
   ```
2. **Next-Hop Options**:
   - **Next-Hop Route**: Specify only the IP of the next router (e.g., `ip route 192.168.2.0 255.255.255.0 172.16.2.2`).
   - **Directly Connected Route**: Specify only the exit interface (e.g., `ip route 192.168.2.0 255.255.255.0 Serial0/1/0`).
   - **Fully Specified Route**: Specify both IP and interface (e.g., `ip route 192.168.2.0 255.255.255.0 Serial0/1/0 172.16.2.2`).

3. **Example for R1 to R3**:
   To route from R1 to R3's LAN (192.168.2.0/24):
   ```bash
   ip route 192.168.2.0 255.255.255.0 172.16.2.2
   ```

---

#### **Configuring IPv6 Static Routes**
1. **Command Syntax**:
   ```bash
   ipv6 route <ipv6-prefix/prefix-length> {<next-hop-ipv6> | <exit-interface>} [distance]
   ```
2. **Enable IPv6 Forwarding**:
   ```bash
   ipv6 unicast-routing
   ```

3. **Example for R1 to R3**:
   To route from R1 to R3's LAN (2001:db8:cafe:2::/64):
   ```bash
   ipv6 route 2001:db8:cafe:2::/64 2001:db8:acad:2::2
   ```

---

#### **Dual-Stack Topology Implementation**
1. **IPv4**:
   - R1 needs a route to R3's LAN via R2.
   - Add static routes on R1, R2, and R3 to ensure full connectivity.

2. **IPv6**:
   - Repeat similar steps for IPv6, ensuring all routers have routes to each other's LANs.

---

#### **Starting Routing Tables**
- Initially, routers only have directly connected networks in their routing tables. Static routes must be added for end-to-end communication.

---

#### **Testing**
1. **Verify Connectivity**:
   Use `ping` to test communication between PCs.
   - Example:
     ```bash
     ping 192.168.2.1
     ```

2. **Verify Routing Tables**:
   Use the following commands to confirm routes:
   - IPv4:
     ```bash
     show ip route
     ```
   - IPv6:
     ```bash
     show ipv6 route
     ```

---

#### **Ping Observations**
- **Success**: R1 can ping R2.
- **Failure**: R1 cannot ping R3 because static routes are not configured yet.

After configuring static routes as described, R1 should successfully ping R3's LAN.

---

Sure! Here’s a simplified explanation of each point from the text you sent:

### 15.2 Configure IP Static Routes
This section discusses how to set up static routes on routers to enable communication between different networks. Static routes are manually configured and tell the router where to send packets for specific networks.

---

### 15.2.1 IPv4 Next-Hop Static Route
- **Next-hop static route**: This type of static route only specifies the next-hop IP address (the IP address of the router to which the packet should be forwarded). The router determines which interface to use based on this IP address.
- **Example**: On router R1, three static routes are configured using the next-hop IP address of R2 (172.16.2.2). These routes direct traffic for three networks (172.16.1.0/24, 192.168.1.0/24, and 192.168.2.0/24) to R2.
  - **Commands**:
    ```
    R1(config)# ip route 172.16.1.0 255.255.255.0 172.16.2.2
    R1(config)# ip route 192.168.1.0 255.255.255.0 172.16.2.2
    R1(config)# ip route 192.168.2.0 255.255.255.0 172.16.2.2
    ```
- **Routing Table**: After configuration, the router's routing table shows that these routes go through the next hop at 172.16.2.2.

---

### 15.2.2 IPv6 Next-Hop Static Route
- Similar to the IPv4 next-hop route, this type of route is configured for IPv6 addresses. The router forwards packets based on the next-hop IPv6 address.
- **Example**: On R1, the commands configure IPv6 static routes to three networks (2001:db8:acad:1::/64, 2001:db8:cafe:1::/64, and 2001:db8:cafe:2::/64), specifying the next-hop address of R2 (2001:db8:acad:2::2).
  - **Commands**:
    ```
    R1(config)# ipv6 unicast-routing
    R1(config)# ipv6 route 2001:db8:acad:1::/64 2001:db8:acad:2::2
    R1(config)# ipv6 route 2001:db8:cafe:1::/64 2001:db8:acad:2::2
    R1(config)# ipv6 route 2001:db8:cafe:2::/64 2001:db8:acad:2::2
    ```
- **Routing Table**: After configuring, the IPv6 routing table on R1 shows routes to these three networks, each forwarding to the next hop at 2001:db8:acad:2::2.

---

### 15.2.3 IPv4 Directly Connected Static Route
- **Directly connected static route**: Instead of specifying the next-hop address, this type of route uses the interface that connects directly to the destination network. 
- **Example**: On R1, three static routes are configured using the exit interface (Serial0/1/0). These routes point to the directly connected networks.
  - **Commands**:
    ```
    R1(config)# ip route 172.16.1.0 255.255.255.0 s0/1/0
    R1(config)# ip route 192.168.1.0 255.255.255.0 s0/1/0
    R1(config)# ip route 192.168.2.0 255.255.255.0 s0/1/0
    ```
- **Note**: This method is mostly used for point-to-point connections, like serial links, not for general network routing.

---

### 15.2.4 IPv6 Directly Connected Static Route
- Similar to the IPv4 directly connected static route, this type is used for IPv6 addresses. It specifies the exit interface to route packets to directly connected networks.
- **Example**: On R1, three IPv6 static routes are configured using the exit interface (Serial0/1/0).
  - **Commands**:
    ```
    R1(config)# ipv6 route 2001:db8:acad:1::/64 s0/1/0
    R1(config)# ipv6 route 2001:db8:cafe:1::/64 s0/1/0
    R1(config)# ipv6 route 2001:db8:cafe:2::/64 s0/1/0
    ```
- **Note**: As with IPv4, using directly connected routes is best suited for point-to-point interfaces.

---

### Key Takeaways:
- **Next-hop route**: Specifies the IP address of the next router, and the router chooses the exit interface.
- **Directly connected route**: Specifies the router’s interface through which the destination network is directly reachable.
- Both IPv4 and IPv6 support these static routing methods, and commands are similar for each protocol, but with some differences in syntax.

---

Here's a simpler explanation of the points you mentioned:

### 15.3.1 Default Static Route
- **Default Route**: A default route is a special route in a router's routing table that catches any traffic that doesn't match other, more specific routes.
- **Usage**: It helps reduce the need for a router to store routes for every possible network. Instead, it can use one default route that covers all unknown destinations.
- **When it's needed**: It's useful when a router only connects to one other router (like a "stub router") or is connected to an internet service provider. It’s called the "Gateway of Last Resort" because it only applies when no other routes match.

**Example**: If Router 1 (R1) needs to send packets to unknown destinations, it can send them to Router 2 (R2) using a default route.

- **IPv4**: The default route in IPv4 uses `0.0.0.0 0.0.0.0`, which matches any destination IP.
- **IPv6**: In IPv6, the default route is `::/0`, which similarly matches any destination.

### 15.3.2 Configure a Default Static Route
- **Simplified Routing**: Instead of configuring multiple routes to different networks, a single default route can be configured, which points to R2 for all unknown destinations.
- **Example for IPv4**: To set a default route on R1 to send traffic to R2, use this command:  
  `ip route 0.0.0.0 0.0.0.0 172.16.2.2`
- **Example for IPv6**: For IPv6, the command would be:  
  `ipv6 route ::/0 2001:db8:acad:2::2`

### 15.3.3 Verify a Default Static Route
- **Verify Routing Table**: After configuring the default route, you can check the routing table to ensure it's there.
- **IPv4 Verification**: Use `show ip route static` to see if the default route is listed with a `*` symbol, which marks it as the "Gateway of Last Resort".
- **IPv6 Verification**: Use `show ipv6 route static` to verify the IPv6 default route.

In both IPv4 and IPv6, the default static route matches any traffic that doesn't have a more specific route in the table, ensuring the router knows where to send such packets.

---

### Floating Static Routes Overview

A **floating static route** is a static route that is used as a backup when the primary dynamic route fails. It is configured with a higher administrative distance than the primary route. The administrative distance (AD) determines the trustworthiness of the route, and a route with a lower AD is preferred over a route with a higher AD. The floating static route is not used unless the primary route fails.

For instance, if an **EIGRP-learned route** has an AD of 90, a floating static route would be configured with a higher AD (e.g., 95). The router will prefer the EIGRP route under normal circumstances. However, if the EIGRP route is unavailable (e.g., a link failure), the router will switch to the floating static route as the backup path.

### Example Scenario

Consider a network with a **Branch** router, an **HQ** router, and a backup **ISP** router for internet connectivity. The Branch router typically forwards traffic to the HQ router over a private WAN link using EIGRP, but if the WAN link fails, a floating static route is configured to allow communication to HQ via the internet. 

The configuration ensures that:
- The **primary route** uses EIGRP with a low AD.
- The **backup route** uses a floating static route with a higher AD (e.g., 95).
  
This configuration ensures that if the primary route is lost (e.g., due to a WAN link failure), the floating static route becomes active.

### Configuration Steps

1. **Configure the primary static route** with the default AD (1).
   Example (IPv4):
   ```
   ip route 0.0.0.0 0.0.0.0 <Primary next-hop IP>
   ```
   Example (IPv6):
   ```
   ipv6 route ::/0 <Primary next-hop IPv6>
   ```

2. **Configure the floating static route** with a higher AD (e.g., 5).
   Example (IPv4):
   ```
   ip route 0.0.0.0 0.0.0.0 <Backup next-hop IP> 5
   ```
   Example (IPv6):
   ```
   ipv6 route ::/0 <Backup next-hop IPv6> 5
   ```

3. **Verify the routing table** using the `show ip route` or `show ipv6 route` commands:
   ```
   show ip route static
   show ipv6 route static
   ```

4. **Simulate a failure** by shutting down the primary interface on the router:
   ```
   interface s0/1/0
   shutdown
   ```

5. **Check the routing table again** to verify that the floating static route has taken over as the active route.

### Example Configuration

```bash
R1(config)# ip route 0.0.0.0 0.0.0.0 172.16.2.2   # Primary static route
R1(config)# ip route 0.0.0.0 0.0.0.0 10.10.10.2 5  # Floating static route
R1(config)# ipv6 route ::/0 2001:db8:acad:2::2   # Primary static route (IPv6)
R1(config)# ipv6 route ::/0 2001:db8:feed:10::2 5  # Floating static route (IPv6)
```

### Verification

- **Check routing table** (before failure):
   ```
   R1# show ip route static
   Gateway of last resort is 172.16.2.2 to network 0.0.0.0
   S*   0.0.0.0/0 [1/0] via 172.16.2.2
   ```

- **Check routing table** (after primary route fails):
   ```
   R1# show ip route static
   Gateway of last resort is 10.10.10.2 to network 0.0.0.0
   S*   0.0.0.0/0 [5/0] via 10.10.10.2
   ```

This verifies that the floating static route becomes active when the primary route (via R2) is no longer available.

---

### 15.5 Configure Static Host Routes

#### 15.5.1 Host Routes
A host route is a route to a specific IPv4 or IPv6 address with a subnet mask of 32 bits for IPv4 or 128 bits for IPv6. Host routes can be added to a router's routing table in three ways:
1. **Automatically Installed:** When an IP address is assigned to an interface, the router automatically adds a host route.
2. **Static Host Route:** Manually configured by the user.
3. **Automatically Obtained:** Through other routing methods, which are covered in other courses.

#### 15.5.2 Automatically Installed Host Routes
When an interface on a Cisco router is configured with an IP address, a **local host route** is automatically installed. These routes are used for packets directed to the router itself, improving efficiency. The routing table will show these routes with an "L" entry. For example, when the Branch router's interface is assigned an IP address, the system automatically installs the following routes:

**Example IPv4 Route:**
```
C        198.51.100.0/30 is directly connected, Serial0/1/0
L        198.51.100.1/32 is directly connected, Serial0/1/0
```

**Example IPv6 Route:**
```
C   2001:DB8:ACAD:1::/64 [0/0]
     via Serial0/1/0, directly connected
L   2001:DB8:ACAD:1::1/128 [0/0]
     via Serial0/1/0, receive
```

#### 15.5.3 Static Host Routes
A static host route is manually configured to direct traffic to a specific destination. These routes use a `/32` subnet mask for IPv4 addresses and `/128` for IPv6 addresses. Static host routes are helpful when routing to a specific device like a server.

**Example Configuration:**
- **IPv4 Static Route**: `ip route 209.165.200.238 255.255.255.255 198.51.100.2`
- **IPv6 Static Route**: `ipv6 route 2001:db8:acad:2::238/128 2001:db8:acad:1::2`

#### 15.5.4 Configure Static Host Routes
To configure a static host route, you can use the following commands in the router's configuration mode. This directs traffic for specific destination addresses to the correct next-hop address.

**IPv4 Static Route:**
```
Branch(config)# ip route 209.165.200.238 255.255.255.255 198.51.100.2
```

**IPv6 Static Route:**
```
Branch(config)# ipv6 route 2001:db8:acad:2::238/128 2001:db8:acad:1::2
```

#### 15.5.5 Verify Static Host Routes
After configuring the static host routes, you can verify their presence and status in the routing table using the following commands:

**For IPv4:**
```
Branch# show ip route | begin Gateway
198.51.100.0/24 is variably subnetted, 2 subnets, 2 masks
C        198.51.100.0/30 is directly connected, Serial0/1/0
L        198.51.100.1/32 is directly connected, Serial0/1/0
S        209.165.200.238 [1/0] via 198.51.100.2
```

**For IPv6:**
```
Branch# show ipv6 route
S   2001:DB8:ACAD:2::238/128 [1/0]
     via 2001:DB8:ACAD:1::2
```

#### 15.5.6 Configure IPv6 Static Host Route with Link-Local Next-Hop
IPv6 static routes can use a link-local address as the next-hop address. When doing so, the interface type and number must be specified. Here is how to configure the IPv6 static route with a link-local next-hop:

**Remove the previous IPv6 route:**
```
Branch(config)# no ipv6 route 2001:db8:acad:2::238/128 2001:db8:acad:1::2
```

**Configure with Link-Local Next-Hop:**
```
Branch(config)# ipv6 route 2001:db8:acad:2::238/128 serial 0/1/0 fe80::2
```

**Verify the configuration:**
```
Branch# show ipv6 route | begin ::
S   2001:DB8:ACAD:2::238/128 [1/0]
     via FE80::2, Serial0/1/0
```

---

Static routes help determine how packets are forwarded across different networks when there is no automatic routing decision. Here's a simplified breakdown of how a packet moves through a network with static routes:

1. **PC1 sends a packet** to PC3.
2. **Router R1** receives the packet but doesn't have a direct route to PC3's network. So, it uses a **default static route**.
3. **R1 forwards the packet** to R2 over a point-to-point connection. It encapsulates the packet in a frame with a special address for point-to-point links.
4. **R2 receives the packet** and looks for a route to PC3's network. It finds a static route to PC3 through a different interface (Serial 0/1/1) and forwards the packet accordingly.
5. **R3 receives the packet** from R2, de-encapsulates it, and finds the route to PC3’s network. If the MAC address for PC3 is not in R3’s table, it sends an **ARP request** to discover the MAC address.
6. Once **R3 knows PC3's MAC address**, it encapsulates the packet into a new frame and forwards it to **PC3**.

So, static routes manually define how packets move from one router to another, ensuring the data reaches the destination, even if there is no automatic routing decision made.

---

### 16.2 Troubleshooting IPv4 Static and Default Route Configuration

#### 16.2.1 Network Changes
Networks are dynamic, and various factors like interface failures, misconfigurations, or connection issues can disrupt connectivity. A network administrator needs to quickly identify and solve problems, which requires familiarity with diagnostic tools.

#### 16.2.2 Common Troubleshooting Commands
To troubleshoot network issues, administrators commonly use these commands:
- **ping**: Tests connectivity to a specific device.
- **traceroute**: Traces the path packets take to reach a destination.
- **show ip route**: Displays the current routing table.
- **show ip interface brief**: Provides information about the status of interfaces.
- **show cdp neighbors detail**: Shows detailed information about neighboring devices (using Cisco Discovery Protocol).

#### 16.2.3 Solving a Connectivity Problem
If a user, like one on **PC1**, cannot access resources on another network, such as the **R3 LAN**, administrators can follow these steps to troubleshoot:

1. **Ping the Remote LAN**: From **R1**, try to ping **R3**'s LAN interface to see if there is connectivity. If it fails, it indicates a routing problem.

    Example:
    ```
    R1# ping 192.168.2.1 source g0/0/0
    Success rate is 0 percent (0/5)
    ```
    This shows no connectivity, suggesting a problem in the route between **R1** and **R3**.

2. **Ping the Next-Hop Router**: Check if **R1** can ping the next router, **R2**, to ensure connectivity is not blocked at this point.

3. **Verify the Routing Table**: Ensure that **R2** has the correct static route to reach **R3**'s network. If the route is missing or incorrect, traffic can't reach its destination.

4. **Correct the Static Route Configuration**: If **R2** lacks a proper static route, it should be configured. For example, R2 might need a static route to **192.168.2.0/24** through its **S0/1/1** interface.

5. **Verify the New Route**: After fixing the route, verify that **R2** has correctly installed the new static route.

6. **Ping Again**: After making corrections, ping the remote LAN again to ensure connectivity is restored.

By using these tools and steps systematically, administrators can identify and resolve routing problems effectively.

---
