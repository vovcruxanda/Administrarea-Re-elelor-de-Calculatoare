# Switch Configuration Summary

## 1.1 Configure a Switch with Initial Settings

### 1.1.1 Switch Boot Sequence
To configure a Cisco switch, you must power it on and let it complete the five-step boot sequence:
1. **Power-on Self-Test (POST):** POST checks the CPU, DRAM, and flash memory.
2. **Boot Loader:** The boot loader software initializes the CPU and prepares the flash file system.
3. **CPU Initialization:** The boot loader sets up the CPU registers, memory mapping, and speed.
4. **Flash File System Initialization:** The flash file system is prepared for use.
5. **IOS Loading:** The boot loader locates and loads the default IOS image, transferring control to the IOS.

### 1.1.2 The `boot system` Command
The switch uses the **BOOT** environment variable to find and load the IOS. If the variable is not set, the switch defaults to the first available executable. The `boot system` command specifies the IOS image location in flash memory. Example:
```shell
S1(config)# boot system flash:/c2960-lanbasek9-mz.150-2.SE/c2960-lanbasek9-mz.150-2.SE.bin
```
Use the `show boot` command to verify the current boot configuration.

### 1.1.3 Switch LED Indicators
Cisco Catalyst switches have LED indicators for monitoring the switch's status:
- **SYST (System):** Power and operation status.
- **RPS (Redundant Power System):** Status of the redundant power system.
- **STAT (Status):** Port status.
- **DUPLX (Duplex):** Duplex mode (full or half).
- **SPEED:** Port speed.
- **PoE:** Power over Ethernet status (if supported).

The **Mode** button toggles between different LED modes to show port status, speed, and PoE.

### 1.1.4 Recovering from a System Crash
If the operating system is missing or damaged, you can access the switch through the boot loader via the console:
1. Connect a PC to the switch via console.
2. Reconnect the power and hold the **Mode** button.
3. Release the **Mode** button when the **System** LED turns green.
4. Use the boot loader to view or set the BOOT environment variable and load a new IOS image.

### 1.1.5 Switch Management Access
To enable remote management, configure a **Switch Virtual Interface (SVI)** with an IP address and subnet mask. A default gateway is needed to manage the switch from a different network.

### 1.1.6 Switch SVI Configuration Example
By default, VLAN 1 is the management interface, but best practices suggest using a different VLAN, such as VLAN 99. To configure VLAN 99 with an IPv4 and IPv6 address:
```shell
S1(config)# interface vlan 99
S1(config-if)# ip address 172.17.99.11 255.255.255.0
S1(config-if)# ipv6 address 2001:db8:acad:99::1/64
```

### 1.1.7 Lab - Basic Switch Configuration
A hands-on lab follows these concepts, guiding users through the basic switch configuration steps, including setting up the management interface and configuring the switch boot process.
