# SLAAC and DHCPv6

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

