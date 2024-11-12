# Basic Device Configuration

## 1.1 Configure a Switch with Initial Settings

### 1.1.1 Switch Boot Sequence
To configure a Cisco switch, you must power it on and let it complete the five-step boot sequence:
1. **Power-on self-test (POST):** POST checks the CPU subsystem, DRAM, and flash memory. It is stored in ROM.
2. **Boot loader:** This software initializes the CPU and prepares the flash file system.
3. **CPU initialization:** The boot loader sets up the CPU registers(it controls where physical memory is mapped), the quantity of memory, and its speed.
4. **Flash File System Initialization:** The flash file system is prepared for use.
5. **IOS Loading:** The boot loader locates and loads the default IOS image, transferring control to the IOS.

### 1.1.2 The `boot system` Command
The switch uses the **BOOT** environment variable to find and load the IOS. If the variable is not set, the switch defaults to the first available executable. The `boot system` command specifies the IOS image location in flash memory.
```shell
S1(config)# boot system flash:/c2960-lanbasek9-mz.150-2.SE/c2960-lanbasek9-mz.150-2.SE.bin
```
Use the `show boot` command to verify the current boot configuration.

| Command                         | Definition             |
|---------------------------------|------------------------|
| boot system                     | The main command       |
| flash:                          | The storage device     |
| c2960-lanbasek9-mz.150-2.SE     | The path to the system |
| c2960-lanbasek9-mz.150-2.SE.bin | The IOS file name      |

### 1.1.3 Switch LED Indicators
Cisco Catalyst switches have LED indicators for monitoring the switch's status(activity & performance):
- **SYST (System):** Power and operation status.

                     LED off - system is not powered on.
                     LED green - system operates normally.
                     LED amber - system is not operating normally.
  
- **RPS (Redundant Power System):** Status of the redundant power system.

                     LED off - RPS off or is not properly connected.
                     LED green - RPS connected, ready to provide backup power.
                     LED blinkig green - RPS connected, but is unavailable because it is providing power to another device.
                     LED amber - RPS is in standby mode, or in a fault condition.
                     LED blinking amber - the internal power supply in the switch has failed, and the RPS is providing power.
                     
- **STAT (Status):** Port status.

                     LED off - there is no link, or the port was administratively shut down.
                     LED green - port status mode is selected, a link is present.
                     LED blinking green - there is activity and the port is sending or receiving data.
                     LED alternating green-amber - there is a link fault.
                     LED amber - the port is blocked to ensure that a loop does not exist in the forwarding domain and is not forwarding data (typically, ports will remain in this state for the first 30 seconds after being activated).
                     LED blinking amber - the port is blocked to prevent a possible loop in the forwarding domain.

- **DUPLX (Duplex):** indicates that duplex mode is selected.

                     LED off - half-duplex mode.
                     LED green - full-duplex mode.

- **SPEED:** indicates speed mode is selected.

                     LED off - port is operating at 10 Mbps.
                     LED green - port is operating at 100 Mbps.
                     LED blinking green - port is operating at 1000 Mbps.

- **PoE:** Power over Ethernet status (if supported).

                     LED off - PoE mode off and that none of the ports have been denied power or placed in a fault condition.
                     LED green - PoE mode on.
                     LED alternating green-amber - PoE is denied because providing power to the powered device will exceed the switch power capacity.
                     LED amber - PoE for the port has been disabled.
                     LED blinking amber - PoE is off because of a fault. 

![image](https://github.com/user-attachments/assets/181acb14-c6e4-450a-bee8-c14680a87b6d)

The **Mode** button toggles between different LED modes to show port status, duplex, speed, and PoE(Power over Ethernet).

### 1.1.4 Recovering from a System Crash
If the operating system is missing or damaged, you can access the switch through the boot loader via the console:
1. Connect a PC to the switch via console. Configure terminal emulation software to connect to the switch.
2. Unplug the switch power cord.
3. Reconnect the power cord to the switch and, within 15 seconds, press and hold down the Mode button while the System LED is still flashing green.
4. Continue pressing the Mode button until the System LED turns briefly amber and then solid green; then release the Mode button.
5. The boot loader switch: prompt appears in the terminal emulation software on the PC.

            switch: set #view the path of the switch BOOT environment variable type
            switch: flash_init #initialize the flash file system to view the current files in flash

            switch: dir flash: #view the directories and files in flash
            Directory of flash:/
                2  -rwx  11834846  <date>               c2960-lanbasek9-mz.150-2.SE8.bin
                3  -rwx  2072      <date>               multiple-fs
   
            switch: BOOT=flash:c2960-lanbasek9-mz.150-2.SE8.bin #to change the BOOT environment variable path the switch uses to load the new IOS in flash
            switch: set #verify the new BOOT environment variable path
            BOOT=flash:c2960-lanbasek9-mz.150-2.SE8.bin
            (output omitted)
            switch: boot #load the new IOS type

### 1.1.5 Switch Management Access
To enable remote management, configure a **Switch Virtual Interface (SVI)** with an IP address and subnet mask. The SVI is a virtual interface, not a physical port on the switch. A default gateway is needed to manage the switch from a different network.

### 1.1.6 Switch SVI Configuration Example
By default, VLAN 1 is the management interface, but best practices suggest using a different VLAN, such as VLAN 99.

**Step1** Configure the Management Interface

From VLAN interface configuration mode, an IPv4 address and subnet mask is applied to the management SVI of the switch. Specifically, SVI VLAN 99 will be assigned the 172.17.99.11/24 IPv4 address and the 2001:db8:acad:99::1/64 IPv6 address as shown.

| Task                                            | IOS Commands            |
|-------------------------------------------------|------------------------|
| Enter global congifuration mode                 | S1# conf t    |
| Enter infterface configuration mode for the SVI | S1(config)# int vlan 99 |
| Configure the management interface IPv4 address | S1(config-if)# ip address 172.17.99.11 255.255.255.0 |
| Configure the management interface IPv6 address | S1(config-if)# ipv6 address 2001:db8:acad:99::11/64 |
| Enable the management interface                 | S1(config-if)# no shutdown    |
| Return to the privileged EXEC mode | S1(config-if)# end |
| Save the running config to the startup config | S1# copy running-config stratup-cofig |

**Step2** Configure the Default Gateway

The switch should be configured with a default gateway if it will be managed remotely from networks that are not directly connected.

Because, it will receive its default gateway information from a router advertisement (RA) message, the switch does not require an IPv6 default gateway.

| Task                                            | IOS Commands            |
|-------------------------------------------------|------------------------|
| Enter global congifuration mode                 | S1# conf t    |
| Enter infterface configuration mode for the SVI | S1(config)# ip default-gateway 172.17.99.1 |
| Return to the privileged EXEC mode | S1(config-if)# end |
| Save the running config to the startup config | S1# copy running-config stratup-cofig |

**Step3** Verify Configuration

The show ip interface brief and show ipv6 interface brief commands are useful for determining the status of both physical and virtual interfaces. The output shown confirms that interface VLAN 99 has been configured with an IPv4 and IPv6 address.

An IP address applied to the SVI is only for remote management access to the switch; this does not allow the switch to route Layer 3 packets.

```shell
S1# show ip interface brief
Interface     IP-Address     OK? Method   Status    Protocol
Vlan99        172.17.99.11   YES manual   down      down
(output omitted)
S1# show ipv6 interface brief
Vlan99                 [down/down]
    FE80::C27B:BCFF:FEC4:A9C1
    2001:DB8:ACAD:99::11
(output omitted)
```

### 1.1.7 Lab - Basic Switch Configuration
![image](https://github.com/user-attachments/assets/d56d7cd0-aa7d-48b6-87d6-a4be8abf0b6a)


## 1.2 Switch Ports Configuration
### 1.2.1 Duplex Communication
- **Full-Duplex**: Bidirectional, allows devices to transmit and receive simultaneously (bandwidth efficiency), no collision domain.

It requires microsegmentation. A microsegmented LAN is created when a switch port has only one device connected and is operating in full-duplex mode. There is no collision domain associated with a switch port operating in full-duplex mode.

- **Half-Duplex**: Unidirectional, data flows in one direction at a time, can result in collisions.

Half-duplex connections are typically seen in older hardware, such as hubs. Half-duplex hubs have been replaced by switches that use full-duplex communications by default.

### 1.2.2 Configure Switch Ports at the Physical Layer
- Switch ports can be manually configured for specific **duplex** and **speed** settings:
  - Use the command `duplex` to set the duplex mode.
  - Use the command `speed` to set the speed (e.g., `100 Mbps`, `1000 Mbps`).
    
![image](https://github.com/user-attachments/assets/358701c1-c7e5-4d25-8d8d-db1b0deafbc0)

The script shows the commands for S1. The same commands can be applied to S2.

#### Example of configuration on Switch S1:
```bash
S1# conf t
S1(config)# interface FastEthernet0/1  #enter interface conf mode
S1(config-if)# duplex full  #configure the interface duplex
S1(config-if)# speed 100   #configure the interface speed
S1(config-if)# end   # return to privileged EXEC mode
S1# copy running-config startup-config
```

The **default duplex and speed** settings for Cisco Catalyst 2960 and 3560 switch ports are set to "auto." Ports operating at 10 or 100 Mbps can be either half- or full-duplex, while 1000 Mbps (1 Gbps) ports are always full-duplex. 

Autonegotiation is useful when connecting to devices with unknown or changing speed and duplex settings, but for known devices like servers, it is recommended to set these manually to avoid connectivity issues. Mismatched settings due to autonegotiation failure can cause problems. 

Fiber-optic ports, such as 1000BASE-SX, always operate at a fixed speed and in full-duplex mode.

### 1.2.3 Auto-MDIX (automatic medium-dependent interface crossover)
- **Auto-MDIX**: Automatically detects the required cable type (straight-through or crossover).
  - Command to enable auto-MDIX:
    ```bash
    S1(config-if)# mdix auto
    ```

When connecting to switches without the auto-MDIX feature, straight-through cables must be used to connect to devices such as servers, workstations, or routers. Crossover cables must be used to connect to other switches or repeaters.

To examine the auto-MDIX setting for a specific interface, use the show controllers ethernet-controller command with the phy keyword. To limit the output to lines referencing auto-MDIX, use the include MDIX filter. As shown the output indicates On or Off for the feature.

```bash
S1# show controllers ethernet-controller fa0/1 phy | include MDIX
Auto-MDIX           :  On   [AdminState=1   Flags=0x00052248]
```

### 1.2.4 Switch Verification Commands
- Common commands to verify switch configuration:
  - `show startup-config`: Display current startup configuration.
  - `show running-config`: Displays the current configuration.
  - `show interfaces [interface-id]`: Shows detailed interface statistics and status.
  - `show flash`: Display information about flash file system.
  - `show version`: Display system hardware and software status.
  - `show history`: Display history of command entered.
  - `show ip interface [interface-id]` or `show ipv6 interface [interface-id]`: Display IP information about an interface
  - `show mac-address-table` or `show mac address-table`: Display the MAC address table

#### Example:
```bash
S1# show interfaces fastEthernet 0/18
```

This command outputs detailed information about the interface, such as its speed, duplex mode, and any input/output errors.

### 1.2.5 Verify Switch Port Configuration

The **`show running-config`** command is used to verify the switch's current configuration. It provides details like:

- Fast Ethernet 0/18 interface is assigned to VLAN 99.
- VLAN 99 is configured with the IPv4 address `172.17.99.11` and a default gateway `172.17.99.1`.

The **`show interfaces`** command gives the status and statistics of network interfaces. For example:

- **FastEthernet0/18** is operational (up/up).
- Speed is set to **100 Mbps**, and duplex is **full-duplex**.

### 1.2.6 Network Access Layer Issues

The **`show interfaces`** command is essential for identifying media issues. Key points include line and data link protocol status:

- **Interface up, line protocol up**: Both hardware and data link layers are functional.
- **Interface up, line protocol down**: Possible encapsulation type mismatch or hardware problem.
- **Both down**: Likely cable or interface issue.

The table explains some of these common errors which can be detected using the show interfaces command.

![image](https://github.com/user-attachments/assets/94468bac-e6ef-4858-9e28-a0de76c2face)

### 1.2.7 Interface Input and Output Errors

#### **Input Errors**  
Input errors refer to issues with incoming data packets on a network interface. These include:

- **Runt Frames**: Ethernet frames smaller than the minimum size of 64 bytes. Usually caused by malfunctioning network interface cards (NICs) or collisions.
- **Giants**: Ethernet frames exceeding the maximum size allowed. These can result from misconfigurations or faulty equipment.
- **CRC Errors**: Caused by media or cable issues, such as electrical interference or loose connections. A high number of CRC errors suggests excessive noise on the link, requiring an inspection of the cable and elimination of noise sources.

#### **Output Errors**  
Output errors occur when the interface is unable to send data packets properly. These include:

- **Collisions**: In half-duplex mode, collisions are expected. However, in full-duplex mode, collisions should not occur. If they do, it's an indication of configuration issues.
- **Late Collisions**: These happen after more than 512 bits of a frame have been transmitted. They are often caused by duplex mismatches or cable lengths exceeding the specification. To avoid this, ensure both ends of the connection have the same duplex setting and that cable lengths are within acceptable limits.

### 1.2.8 Troubleshooting Network Access Layer Issues

![image](https://github.com/user-attachments/assets/dd6a796f-d0a5-462d-b9a1-108078dfea8a)

To troubleshoot switch connectivity issues:

1. **Check the interface status** using the **`show interfaces`** command.
2. If the interface is down:
   - Verify cables and connectors.
   - Check speed and duplex settings on both ends.
3. If the interface is up but there are issues:
   - Look for signs of noise (runts, giants, CRC errors).
   - Verify duplex and speed settings to avoid mismatches.

## 1.3 Secure Remote Access

### 1.3.1 Telnet Operation
Telnet is a protocol that provides remote access to network devices using TCP port 23. It is not secure, as it transmits data, including usernames and passwords, in plaintext. This makes it vulnerable to attacks where a threat actor can easily capture sensitive information using tools like Wireshark.

### 1.3.2 SSH Operation
SSH (Secure Shell) is a secure protocol that operates on TCP port 22. Unlike Telnet, SSH encrypts both the login credentials and the data transmitted between devices. This ensures that even if a threat actor intercepts the session, the data, including the username and password, is encrypted and secure.

### 1.3.3 Verify the Switch Supports SSH
To enable SSH on a Catalyst 2960 switch, the switch must support cryptographic features. You can verify this by running the `show version` command. If the IOS filename includes "k9," then the switch supports encrypted communication. For example:
```
S1# show version
Cisco IOS Software, C2960 Software (C2960-LANBASEK9-M), Version 15.0(2)SE7, RELEASE SOFTWARE (fc1)
```

### 1.3.4 Configure SSH
To configure SSH on a switch, follow these steps:

1. **Verify SSH Support**: Use the `show ip ssh` command to check if SSH is enabled. If the IOS doesn't support encryption, this command will not be recognized.
   ```
   S1# show ip ssh
   ```

2. **Configure the IP domain**: Configure the IP domain name of the network using the `ip domain-name [domain-name]` global configuration mode command. In the figure, the domain-name value is cisco.com.
   ```
   S1(config)# ip domain-name cisco.com
   ```

3. **Generate RSA key pairs**: Use the crypto key generate rsa global configuration mode command to enable the SSH server on the switch and generate an RSA key pair. When generating RSA keys, the administrator is prompted to enter a modulus length. The sample configuration in the figure uses a modulus size of 1,024 bits. A longer modulus length is more secure, but it takes longer to generate and to use.

   ```
   S1(config)# crypto key generate rsa
   How many bits in the modulus [512]: 1024
   ```

To delete the RSA key pair, use the crypto key zeroize rsa global configuration mode command. After the RSA key pair is deleted, the SSH server is automatically disabled.

4. **Configure user authentication**: The SSH server can authenticate users locally or using an authentication server. 
   ```
   S1(config)# username admin secret ccna
   ```

5. **Configure the vty lines**:
   ```
   S1(config)# line vty 0 15        //The Catalyst 2960 has vty lines ranging from 0 to 15. This configuration prevents non-SSH (such as Telnet) connections and limits the switch to accept only SSH connections.
   S1(config-line)# transport input ssh   //Enable the SSH protocol on the vty lines
   S1(config-line)# login local   //to require local authentication for SSH connections from the local username database.
   S1(config-line)# exit
   ```

2. **Enablr SSH version 2**: By default, SSH supports both versions 1 and 2. When supporting both versions, this is shown in the show ip ssh output as supporting version 2. Enable SSH version using the ip ssh version 2 global configuration command.
   ```
   S1(config)# ip ssh version 2
   ```

### 1.3.5 Verify SSH is Operational
To verify that SSH is working, use an SSH client like PuTTY on a PC. Enter the switch’s IP address, choose SSH as the connection type, and use the default port 22.

![image](https://github.com/user-attachments/assets/f1cc959b-1a25-46d4-b3dd-2440d6283f2b)

The switch will prompt for a username and password.
```
Login as: admin
Using keyboard-interactive
Authentication.
Password:
S1> enable
Password:
S1#
```

Once connected, you can use the `show ip ssh` command to verify SSH configuration and the `show ssh` command to check active SSH sessions:
```
S1# show ip ssh
SSH Enabled - version 2.0
Authentication timeout: 120 secs; Authentication retries: 3

S1# show ssh
%No SSHv1 server connections running.
Connection Version Mode Encryption  Hmac      State       Username
0          2.0     IN   aes256-cbc  hmac-sha1 Session started admin
0          2.0     OUT  aes256-cbc  hmac-sha1 Session started admin
```

### 1.3.6 Packet Tracer - Configure SSH
In Packet Tracer, you can simulate SSH configuration and remote access to a switch. SSH is used to replace Telnet because of its encrypted communication, ensuring secure remote management of the network.

## 1.4 Basic Router Configuration Overview

### 1.4.1 Configure Basic Router Settings
Just like switches, Cisco routers require basic configuration. Some common tasks include:

1. **Set a hostname** to identify the router:
   ```
   Router(config)# hostname R1
   ```

2. **Configure passwords** to secure access:
   ```
   R1(config)# enable secret class
   R1(config)# line console 0
   R1(config-line)# password cisco
   R1(config-line)# login
   R1(config-line)# exit
   ```

3. **Configure VTY (Virtual Terminal) lines** for remote access:
   ```
   R1(config)# line vty 0 4
   R1(config-line)# password cisco
   R1(config-line)# login
   R1(config-line)# exit
   ```

4. **Enable password encryption** to secure all passwords stored in the configuration file:
   ```
   R1(config)# service password-encryption
   ```

5. **Configure a banner** to notify unauthorized users:
   ```
   R1(config)# banner motd #Authorized Access Only!#
   ```

6. **Save the configuration** to make changes persistent across reboots:
   ```
   R1# copy running-config startup-config
   ```

### 1.4.3 Dual Stack Topology
Routers handle both IPv4 and IPv6 addressing. A dual-stack topology allows hosts and devices to use both protocols. You configure routers to manage different subnets and IP versions for each interface.

### 1.4.4 Configure Router Interfaces

![image](https://github.com/user-attachments/assets/606b96c9-de59-4300-ac58-9040dbc4fac6)

Routers support different types of interfaces, including LAN and WAN connections. Interfaces need to be:

- **Configured with an IP address**:
  ```
  R1(config)# interface gigabitethernet 0/0/0
  
  R1(config-if)# ip address 192.168.10.1 255.255.255.0
  R1(config-if)# ipv6 address 2001:db8:acad:1::1/64
  ```

- **Activated** using the `no shutdown` command:
  ```
  R1(config-if)# no shutdown
  ```

- **Optionally described** for clarity:
  ```
  R1(config-if)# description Link to LAN 1
  ```

The remaining part of configuration for R1
```
R1(config-if)# exit
R1(config)# interface gigabitethernet 0/0/1
R1(config-if)# ip address 192.168.11.1 255.255.255.0
R1(config-if)# ipv6 address 2001:db8:acad:2::1/64
R1(config-if)# description Link to LAN 2
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)# interface serial 0/0/0
R1(config-if)# ip address 209.165.200.225 255.255.255.252
R1(config-if)# ipv6 address 2001:db8:acad:3::225/64
R1(config-if)# description Link to R2
R1(config-if)# no shutdown
R1(config-if)# exit
R1(config)#
```

### 1.4.6 IPv4 Loopback Interfaces
Loopback interfaces are logical, internal interfaces used for testing or routing processes. They stay "up" as long as the router is functional. To configure a loopback interface:
```
R1(config)# interface loopback 0
R1(config-if)# ip address 10.0.0.1 255.255.255.0
```

These configurations help ensure that routers can effectively manage both IPv4 and IPv6 traffic, secure access, and be properly monitored.

## 1.5 Verifying Directly Connected Networks

### 1.5.1 Interface Verification Commands
Before configuring a router, it's essential to verify its configuration and connectivity. Several useful commands for checking the status of interfaces are:

- **`show ip interface brief` / `show ipv6 interface brief`**: Summarizes interface status, including IP addresses and operational status.
- **`show running-config interface`**: Displays the current configuration of a specified interface.
- **`show ip route` / `show ipv6 route`**: Shows the IPv4 or IPv6 routing table, indicating which networks are directly connected. In Cisco IOS 15, active interfaces should appear in the routing table with two related entries identified by the code ‘C’ (Connected) or ‘L’ (Local).

### 1.5.2 Verify Interface Status
These commands reveal the operational status of all interfaces. An interface is functional if both Status and Protocol are "up." Example:

```
R1# show ip interface brief
GigabitEthernet0/0/0   192.168.10.1  up   up
GigabitEthernet0/0/1   192.168.11.1  up   up
Serial0/1/1            unassigned    down down
```

### 1.5.3 Verify IPv6 Link-Local and Multicast Addresses
IPv6 interfaces automatically have a **link-local** address starting with `FE80`. Multicast addresses, such as those beginning with `FF02`, are also visible with the `show ipv6 interface` command.

```
R1# show ipv6 interface gigabitethernet 0/0/0
GigabitEthernet0/0/0 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::7279:B3FF:FE92:3130
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:ACAD:1::1, subnet is 2001:DB8:ACAD:1::/64
  Joined group address(es):
    FF02::1
    FF02::1:FF00:1
    FF02::1:FF92:3130
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds (using 30000)
  ND advertised reachable time is 0 (unspecified)
  ND advertised retransmit interval is 0 (unspecified)
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
```

### 1.5.4 Verify Interface Configuration
To view the full configuration of a specific interface, use `show running-config interface`. Example:

```
R1# show running-config interface g0/0/0
Building configuration...
Current configuration : 158 bytes
!
interface GigabitEthernet0/0/0
    description Link to LAN 1
    ip address 192.168.10.1 255.255.255.0
    negotiation auto
    ipv6 address 2001:DB8:ACAD:1::1/64
end
R1#
```

### 1.5.5 Verify Routes
The `show ip route` and `show ipv6 route` commands display directly connected networks (`C`), and local host routes (`L`). Example:

```
R1# show ip route
C   192.168.10.0/24 is directly connected
L   192.168.10.1/32 is directly connected
```

### 1.5.6 Filter Show Command Output
Commands that generate multiple screens of output are, by default, paused after 24 lines. At the end of the paused output, the --More-- text displays. Pressing Enter displays the next line and pressing the spacebar displays the next set of lines. Use the terminal length command to specify the number of lines to be displayed. A value of 0 (zero) prevents the router from pausing between screens of output.

Another very useful feature that improves the user experience in the CLI is the filtering of show output. Filtering commands can be used to display specific sections of output. To enable the filtering command, enter a pipe (|) character after the show command and then enter a filtering parameter and a filtering expression.

To filter large command outputs, use a **pipe (`|`)** with parameters like:
- `section`: Shows a section starting with the filter keyword.
```
R1# show running-config | section line vty
line vty 0 4
 password 7 110A1016141D
 login
 transport input all
```
- `include`: Displays only lines containing the specified text.
```
R1# show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0/0   192.168.10.1    YES manual up                    up
GigabitEthernet0/0/1   192.168.11.1    YES manual up                    up
Serial0/1/0            209.165.200.225 YES manual up                    up
Serial0/1/1            unassigned      NO  unset  down                  down
R1#
R1# show ip interface brief | include up
GigabitEthernet0/0/0   192.168.10.1    YES manual up                    up
GigabitEthernet0/0/1   192.168.11.1    YES manual up                    up
Serial0/1/0            209.165.200.225 YES manual up                    up
```
- `exclude`: Excludes all output lines that match the filtering expression.
```
R1# show ip interface brief | exclude unassigned
Interface              IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0/0   192.168.10.1    YES manual up                    up
GigabitEthernet0/0/1   192.168.11.1    YES manual up                    up
Serial0/1/0            209.165.200.225 YES manual up                    up
```
- `begin`: Shows all the output lines from a certain point, starting with the line that matches the filtering expression.
```
R1# show ip route | begin Gateway
Gateway of last resort is not set
      192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.10.0/24 is directly connected, GigabitEthernet0/0/0
L        192.168.10.1/32 is directly connected, GigabitEthernet0/0/0
      192.168.11.0/24 is variably subnetted, 2 subnets, 2 masks
C        192.168.11.0/24 is directly connected, GigabitEthernet0/0/1
L        192.168.11.1/32 is directly connected, GigabitEthernet0/0/1
      209.165.200.0/24 is variably subnetted, 2 subnets, 2 masks
C        209.165.200.224/30 is directly connected, Serial0/1/0
L        209.165.200.225/32 is directly connected, Serial0/1/0
```

### 1.5.8 Command History Feature
You can scroll through previously executed commands using **Ctrl+P** or the **Up Arrow** key.
To return to more recent commands in the history buffer, press Ctrl+N or the Down Arrow key.

By default, command history is enabled and the system captures the last 10 command lines in its history buffer. Use the `show history` privileged EXEC command to display the contents of the buffer. Use the `terminal history size` user EXEC command to increase or decrease the size of the buffer.

```
R1# terminal history size 200
R1# show history
  show ip int brief
  show interface g0/0/0
  show ip route
  show running-config
  show history
  terminal history size 200
```


# Switching Concepts
Here's a structured overview of **Frame Forwarding** and its related concepts based on your provided text:

## 2.1 Frame Forwarding

### 2.1.1 Switching in Networking
- **Switching and Forwarding**: Fundamental in networking and telecommunications.
- **Types of Switches**: Utilized in LANs, WANs, and the PSTN(public switched telephone network).
- **Ingress and Egress**:
  - **Ingress**: Port where a frame enters the device.
  - **Egress**: Port through which frames leave the device.
- **LAN Switch Functionality**:
  - Maintains a single master MAC address table for forwarding traffic through the switch.
  - Each Ethernet frame exits the same egress port regardless of the ingress port.
- **Forwarding Rule**: An Ethernet frame is not forwarded out the same port it was received.

### 2.1.2 The Switch MAC Address Table
- **Components of a Switch**: Integrated circuits and software hat controls the data paths through the switch. 
- **Purpose of MAC Address Table**: Directs network communications based on destination MAC addresses.
- **Learning Process**: Switch learns devices on each port and populates the MAC address table using source MAC addresses.

For a switch to know which port to use to transmit a frame, it must first learn which devices exist on each port. As the switch learns the relationship of ports to devices, it builds a table called a MAC address table. This table is stored in content addressable memory (CAM) which is a special type of memory used in high-speed searching applications. For this reason, the MAC address table is sometimes also called the CAM table.

LAN switches determine how to handle incoming data frames by maintaining the MAC address table. A switch populates its MAC address table by recording the source MAC address of each device connected to each of its ports. The switch references the information in the MAC address table to send frames destined for a specific device out of the port which has been assigned to that device.

### 2.1.3 The Switch Learn and Forward Method
- **Step 1: Learn** (Source MAC Address):
  - If the MAC address is new, it is added to the MAC table with the corresponding port.
  - If the MAC address exists, the refresh timer is updated (default: 5 minutes).
  - If the MAC is found on a different port, the entry is updated to the current port.
  
- **Step 2: Forward** (Destination MAC Address):
  - For unicast addresses, if a match exists, the frame is forwarded out the specified port.
  - If no match is found, the frame is flooded out all ports except the ingress port.
  - Broadcast or multicast frames are also forwarded out all ports except the ingress port.

### 2.1.5 Switching Forwarding Methods
- **Layer 2 Forwarding Decisions**: Made rapidly using ASICs.
- **Methods**:
  - **Store-and-Forward Switching**: 
    - Checks the entire received frame for errors (using CRC - cyclic redundancy check) before forwarding.
    - Cisco's primary LAN switching method.
  - **Cut-Through Switching**: 
    - Begins forwarding after determining the destination MAC address without checking the entire frame.

### 2.1.6 Store-and-Forward Switching
- **Characteristics**:
  - **Error Checking**: Compares the FCS(frame check sequence) of the frame against its own calculations; forwards only error-free frames.
  - **Automatic Buffering**: Supports different Ethernet speeds by storing the entire frame before forwarding.

### 2.1.7 Cut-Through Switching
- **Comparison with Store-and-Forward**:
  - Can forward frames without FCS checks, allowing rapid frame switching.
  - Frames can start being forwarded as soon as the destination MAC address is received.
- **Fragment Free Switching**: A modified cut-through method that improves error checking with minimal latency increase.
- **Use Cases**: Ideal for high-performance computing applications needing low process-to-process latencies.
- **Drawback**: High error rates can clog bandwidth with invalid frames.

Here's a detailed overview of **Collision and Broadcast Domains**, explaining their concepts and implications in networking:

## 2.2 Collision and Broadcast Domains

### 2.2.1 Collision Domains
- **Definition**: Collision domains are segments of a network where devices share the same bandwidth and compete for transmission. Collisions occur when two or more devices attempt to communicate simultaneously within this domain.
  
- **Legacy Hub-Based Ethernet**:
  - In older network architectures using hubs, all devices share the same collision domain.
  - A collision happens if multiple devices attempt to send data at the same time.

- **Switching and Duplex Modes**:
  - **Half-Duplex**: In half-duplex mode, each switch port forms its own collision domain, which can lead to collisions if two devices try to transmit at the same time.
  - **Full-Duplex**: When operating in full-duplex, there are no collisions because devices can send and receive data simultaneously.
  - By default, Ethernet switch ports negotiate to use full-duplex if the connected device supports it. If connected to a half-duplex device (like an older hub), the switch port will also operate in half-duplex mode, creating a collision domain.

- **Autonegotiation**: 
  - Switch ports negotiate the highest common duplex mode and speed with connected devices. For example, if a switch is connected to a PC that supports full-duplex at 100 Mbps, they will both operate at this speed.

### 2.2.2 Broadcast Domains
- **Definition**: A broadcast domain encompasses all devices that receive broadcast frames sent by any device within that domain.
  
- **Interconnected Switches**:
  - A collection of interconnected switches forms a single broadcast domain. Unlike collision domains, broadcast domains are segmented by network layer devices like routers.
  
- **Layer 2 Broadcasts**: 
  - A broadcast frame is identified by a destination MAC address of all binary ones. When a device sends a broadcast, it is received by all devices within the MAC broadcast domain.
  
- **Forwarding Broadcast Frames**:
  - When a switch receives a broadcast frame, it forwards it out of all ports except the port on which it was received, ensuring every device in the broadcast domain receives it.
  
- **Impact on Network Efficiency**:
  - Broadcasts are necessary for locating devices and services but can lead to reduced efficiency. Excessive broadcasts can cause congestion and slow down network performance.
  
- **Example of Broadcast Propagation**:
  - If Switch S1 sends a broadcast frame, it will forward it to all its ports, including the port connected to Switch S2, which will then propagate the frame to all its connected devices.

### 2.2.3 Alleviating Network Congestion
- **Characteristics of LAN Switches**:
  - **Full-Duplex Ports**: Interconnected switch ports typically operate in full-duplex mode, eliminating collision domains and allowing for higher performance.
  
- **Performance Factors**:
  - **Fast Port Speeds**: Switches come with various port speeds (e.g., 100 Mbps, 1 Gbps, 10 Gbps), impacting performance. Higher speeds are more costly but reduce congestion.
  - **Fast Internal Switching**: Efficient internal architecture allows for high-performance switching capabilities.
  - **Large Frame Buffers**: Larger buffers can temporarily store incoming frames, preventing drops when there’s a speed mismatch between ingress and egress ports.
  - **High Port Density**: More ports on a single switch reduce the need for additional switches, which lowers costs and keeps local traffic contained, alleviating congestion.
