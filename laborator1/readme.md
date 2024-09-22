# Switch Configuration Summary

## 1.1 Configure a Switch with Initial Settings

### 1.1.1 Switch Boot Sequence
To configure a Cisco switch, you must power it on and let it complete the five-step boot sequence:
1. **Power-on self-test (POST):** POST checks the CPU subsystem, DRAM, and flash memory. It is stored in ROM.
2. **Boot loader:** This software initializes the CPU and prepares the flash file system.
3. **CPU initialization:** The boot loader sets up the CPU registers(it controls where physical memory is mapped), the quantity of memory, and its speed.
4. **Flash File System Initialization:** The flash file system is prepared for use.
5. **IOS Loading:** The boot loader locates and loads the default IOS image, transferring control to the IOS.

### 1.1.2 The `boot system` Command
The switch uses the **BOOT** environment variable to find and load the IOS. If the variable is not set, the switch defaults to the first available executable. The `boot system` command specifies the IOS image location in flash memory. Example:
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
