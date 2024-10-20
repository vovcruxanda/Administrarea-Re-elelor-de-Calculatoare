# 4. Inter-VLAN Routing

## 4.1 Inter-VLAN Routing Operation

### 4.1.1 What is Inter-VLAN Routing?
- **VLANs** are used to divide a network into smaller segments.
- Devices in one VLAN cannot communicate with devices in another VLAN unless there's a router or Layer 3 switch that can manage the communication.
- **Inter-VLAN routing** allows data to travel from one VLAN to another.

There are three main ways to do inter-VLAN routing:
1. **Legacy Inter-VLAN routing**: Old method, not scalable.
2. **Router-on-a-Stick**: Good for small to medium-sized networks.
3. **Layer 3 switch with SVIs**: Best for medium to large networks.

### 4.1.2 Legacy Inter-VLAN Routing
- This method uses a router with multiple Ethernet ports, each connected to different VLANs.
- Each VLAN needs its own router interface, which acts as its gateway.
- This approach has a major drawback: routers have a limited number of ports, so it doesn't work well for networks with many VLANs.

### 4.1.3 Router-on-a-Stick Inter-VLAN Routing
- This method uses one physical router interface but creates virtual interfaces called **subinterfaces** for each VLAN.
- The router interface is set up as a **trunk** that handles traffic for multiple VLANs.
- This setup allows the router to handle multiple VLANs using one interface, making it more efficient.
- However, it’s only suitable for networks with up to 50 VLANs.

### 4.1.4 Inter-VLAN Routing on a Layer 3 Switch
- The modern and most efficient way to perform inter-VLAN routing is by using a **Layer 3 switch**.
- Layer 3 switches use **Switched Virtual Interfaces (SVIs)**, which act like virtual routers for each VLAN.
- Advantages of using a Layer 3 switch:
  - **Faster**: Uses hardware-based switching for quick communication.
  - No need for extra links to external routers.
  - More **scalable** and can handle more VLANs.
  - **Lower latency** because data doesn't leave the switch.

- The downside is that Layer 3 switches tend to be more expensive than routers.

## 4.2 Router-on-a-Stick Inter-VLAN Routing
"Router-on-a-stick" is a method used to allow communication between different VLANs (Virtual Local Area Networks) using a single physical router interface, which is divided into multiple virtual interfaces called subinterfaces.

### 4.2.1 Router-on-a-Stick Scenario
- You have multiple VLANs on a switch, and you need to route traffic between them.
- A router's interface is connected to a switch using a trunk link (a link that can carry traffic for multiple VLANs).
- The router's single physical interface is divided into subinterfaces, each assigned to a specific VLAN.

![image](https://github.com/user-attachments/assets/11cc62fa-0f59-4ef2-abf5-453390df0b62)


### 4.2.2 S1 VLAN and Trunking Configuration
The switches (S1 and S2) need to be configured to work with VLANs and trunking:

1. **Create & name VLANs**: Define the VLANs that will be used on the switches.
   ```plaintext
   S1(config)# vlan 10
   S1(config-vlan)# name LAN10
   S1(config-vlan)# exit
   S1(config)# vlan 20
   S1(config-vlan)# name LAN20
   S1(config-vlan)# exit
   S1(config)# vlan 99
   S1(config-vlan)# name Management
   S1(config-vlan)# exit
   ```
   
2. **Create the management interface**:  Management interface is created on VLAN 99 along with the default gateway of R1.
   ```plaintext
    S1(config)# interface vlan 99
    S1(config-if)# ip add 192.168.99.2 255.255.255.0
    S1(config-if)# no shut
    S1(config-if)# exit
    S1(config)# ip default-gateway 192.168.99.1
    S1(config)#
   ```
   
3. **Configure access ports**:  Next, port Fa0/6 connecting to PC1 is configured as an access port in VLAN 10. Assume PC1 has been configured with the correct IP address and default gateway.
   ```plaintext
    S1(config)# interface fa0/6
    S1(config-if)# switchport mode access
    S1(config-if)# switchport access vlan 10
    S1(config-if)# no shut
    S1(config-if)# exit
   ```

4. **Configure trunking ports**: Finally, ports Fa0/1 connecting to S2 and Fa05 connecting to R1 are configured as trunk ports.
  ```plaintext
    S1(config)# interface fa0/1
    S1(config-if)# switchport mode trunk
    S1(config-if)# no shut
    S1(config-if)# exit
    S1(config)# interface fa0/5
    S1(config-if)# switchport mode trunk
    S1(config-if)# no shut
    S1(config-if)# end
    *Mar  1 00:23:43.093: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
    *Mar  1 00:23:44.511: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/5, changed state to up
   ```

### 4.2.3 S2 VLAN and Trunking Configuration

The configuration for S2 is similar to S1.
  ```plaintext
    S2(config)# vlan 10
    S2(config-vlan)# name LAN10
    S2(config-vlan)# exit
    S2(config)# vlan 20
    S2(config-vlan)# name LAN20
    S2(config-vlan)# exit
    S2(config)# vlan 99
    S2(config-vlan)# name Management
    S2(config-vlan)# exit
    S2(config)#
    S2(config)# interface vlan 99
    S2(config-if)# ip add 192.168.99.3 255.255.255.0
    S2(config-if)# no shut
    S2(config-if)# exit
    S2(config)# ip default-gateway 192.168.99.1
    S2(config)# interface fa0/18
    S2(config-if)# switchport mode access
    S2(config-if)# switchport access vlan 20
    S2(config-if)# no shut
    S2(config-if)# exit
    S2(config)# interface fa0/1
    S2(config-if)# switchport mode trunk
    S2(config-if)# no shut
    S2(config-if)# exit
    S2(config-if)# end
    *Mar  1 00:23:52.137: %LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/1, changed state to up
   ```

### R1 Subinterface Configuration
The router (R1) is set up to handle traffic from multiple VLANs using subinterfaces:

1. **Create subinterfaces** for each VLAN on the router's main interface (e.g., `GigabitEthernet0/0/1`):
   ```plaintext
   R1(config)# interface G0/0/1.10
   R1(config-subif)# encapsulation dot1Q 10 #his command configures the subinterface to respond to 802.1Q encapsulated traffic from the specified vlan-id. 
   R1(config-subif)# ip address 192.168.10.1 255.255.255.0 #This command configures the IPv4 address of the subinterface. This address typically serves as the default gateway for the identified VLAN.
   ```

   Repeat the process for each VLAN (e.g., VLAN 20, VLAN 99).
```plaintext
   R1(config)# interface G0/0/1.20
R1(config-subif)# encapsulation dot1Q 20
R1(config-subif)# ip add 192.168.20.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
R1(config)# interface G0/0/1.99
R1(config-subif)# encapsulation dot1Q 99
R1(config-subif)# ip add 192.168.99.1 255.255.255.0
R1(config-subif)# exit
R1(config)#
```

2. **Enable the physical interface** to ensure it is active:
   ```plaintext
   R1(config)# interface G0/0/1
   R1(config-if)# no shutdown
   ```

### Step 4: Verify Connectivity
- **Ping between devices** in different VLANs to confirm that the router is correctly routing traffic.
- Use commands like `show ip route` to check that the subinterfaces are set up correctly.

### Key Points
- **Router-on-a-stick** uses one physical router interface with multiple subinterfaces to route traffic between VLANs.
- **Trunk links** are essential to carry VLAN traffic between the switch and the router.
- Each VLAN needs its own subinterface with an IP address, serving as the gateway for that VLAN.

## 4.3 Inter-VLAN Routing using Layer 3 Switches

### 4.3.1 Layer 3 Switch Inter-VLAN Routing

In modern enterprise networks, inter-VLAN routing is typically achieved using Layer 3 switches rather than the router-on-a-stick method. Layer 3 switches offer a scalable solution for large networks, providing faster packet processing through hardware-based switching compared to traditional routers. They are commonly used in enterprise distribution layer wiring closets.

**Key Capabilities of a Layer 3 Switch:**
1. **Routing between VLANs** using multiple Switched Virtual Interfaces (SVIs).
2. **Converting Layer 2 switchports to Layer 3 interfaces** (routed ports), similar to physical interfaces on routers.

To enable inter-VLAN routing, a Layer 3 switch must have an SVI for each VLAN that needs to be routable.

#### 4.3.2 Layer 3 Switch Scenario

Consider a scenario where a Layer 3 switch (D1) is connected to two hosts on different VLANs:
- **PC1 is in VLAN 10**
- **PC2 is in VLAN 20**

The Layer 3 switch will provide inter-VLAN routing services to these hosts.

#### 4.3.3 Layer 3 Switch Configuration

To configure the Layer 3 switch for VLANs and trunking, follow these steps:

1. **Create the VLANs:**
   ```plaintext
   D1(config)# vlan 10
   D1(config-vlan)# name LAN10
   D1(config-vlan)# vlan 20
   D1(config-vlan)# name LAN20
   D1(config-vlan)# exit
   ```

2. **Create the SVI VLAN interfaces.**

3. **Configure access ports.**

4. **Enable IP routing** on the switch.

#### 4.3.4 Layer 3 Switch Inter-VLAN Routing Verification

After configuring the Layer 3 switch, verify inter-VLAN connectivity by testing the communication between hosts in different VLANs. For example, you can use the `ping` command to check connectivity.

Example output:
```plaintext
C:\Users\PC1> ping 192.168.20.10
Pinging 192.168.20.10 with 32 bytes of data:
Reply from 192.168.20.10: bytes=32 time<1ms TTL=127
Reply from 192.168.20.10: bytes=32 time<1ms TTL=127
Reply from 192.168.20.10: bytes=32 time<1ms TTL=127
Reply from 192.168.20.10: bytes=32 time<1ms TTL=127
```
This output confirms that inter-VLAN routing is functioning correctly.

#### 4.3.5 Routing on a Layer 3 Switch

For VLANs to be reachable by other Layer 3 devices, routing must be enabled using either static or dynamic routing. A **routed port** on the Layer 3 switch can be configured by disabling the switchport feature on a Layer 2 port using the `no switchport` command.

#### 4.3.6 Routing Scenario on a Layer 3 Switch

Imagine a scenario where the Layer 3 switch (D1) is connected to a router (R1) using the Open Shortest Path First (OSPF) routing protocol. The G0/0/1 interface of R1 is configured and participates in OSPF routing, allowing communication between multiple networks.

#### 4.3.7 Routing Configuration on a Layer 3 Switch

Follow these steps to configure routing on the Layer 3 switch:

1. **Configure the Routed Port**:
   ```plaintext
   D1(config)# interface GigabitEthernet0/0/1
   D1(config-if)# description routed Port Link to R1
   D1(config-if)# no switchport
   D1(config-if)# ip address 10.10.10.2 255.255.255.0
   D1(config-if)# no shut
   D1(config-if)# exit
   ```

2. **Enable Routing** on the switch.

3. **Configure Routing Protocols** (if required).

4. **Verify Routing** to ensure that the Layer 3 switch correctly routes traffic between VLANs.

5. **Verify Connectivity** between devices in different VLANs and on different networks.

By following these steps, you can set up inter-VLAN routing on a Layer 3 switch, ensuring efficient communication between different VLANs in your network.
