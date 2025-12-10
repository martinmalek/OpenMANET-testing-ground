# usbc-gadget

Configures USB-C gadget mode on Raspberry Pi 4 to connect EUDs directly to the OpenMANET mesh network.

## Overview

This role enables USB-C gadget mode on Raspberry Pi 4 running OpenWrt/OpenMANET. USB-C connected devices (EUDs) become part of the mesh network and receive dynamic IP addresses from the mesh network's DHCP server (10.41.0.0/16 range).

## Features

- Enables USB-C gadget mode via boot configuration (`dtoverlay=dwc2` and `modules-load=dwc2,g_ether`)
- Adds `usb0` interface to the mesh network bridge (`br-ahwlan`)
- EUDs receive IPs from the mesh network DHCP (10.41.0.100-10.41.0.115 by default)
- EUDs are part of the mesh network and can communicate with all mesh nodes
- No separate NAT required - EUDs are on the same network as mesh nodes

## Prerequisites

- Raspberry Pi 4 running OpenWrt/OpenMANET
- USB-C port available
- Required kernel modules installed:
  - `kmod-usb-dwc2`
  - `kmod-usb-gadget-eth`
- Mesh network bridge (`br-ahwlan`) must exist

## Variables

Variables are defined in `defaults/main.yml`:

- `usbc_gadget_interface`: USB gadget interface name (default: `usb0`)
- `usbc_gadget_mesh_bridge`: Mesh network bridge name (default: `br-ahwlan`)

Override these in `group_vars/all.yml` or `host_vars/<hostname>.yml` if needed.

## Usage

```bash
ansible-playbook playbooks/site.yml --tags usbc-gadget
```

## How It Works

1. **Boot Configuration**: Adds `dtoverlay=dwc2` to `/boot/config.txt` and `modules-load=dwc2,g_ether` to `/boot/cmdline.txt`
2. **Bridge Configuration**: Adds `usb0` as a port to the `br-ahwlan` bridge via UCI
3. **Init Script**: Loads `g_ether` module and adds `usb0` to the bridge on boot
4. **DHCP**: EUDs receive IPs from the existing mesh network DHCP (managed by `ahwlan` interface)

## Network Topology

```
[Connected EUD] --USB-C--> [Pi 4 usb0] --bridge--> [br-ahwlan: 10.41.0.1/16] <--> [Mesh Network]
                              |
                              DHCP: 10.41.0.100 - 10.41.0.115 (from mesh DHCP)
```

**Note**: USB-C connected EUDs are on the same network as mesh nodes:
- Mesh network: `10.41.0.0/16` (ahwlan interface)
- EUDs receive IPs from mesh DHCP range (typically `10.41.0.100-10.41.0.115`)
- No NAT - direct mesh network access

## Testing

After deployment, connect a device via USB-C cable. The device should:

1. Receive an IP address via DHCP from the mesh network (e.g., `10.41.0.100`)
2. Be able to communicate with all mesh nodes
3. Have access to mesh network services

Check interface status:
```bash
ip addr show usb0
brctl show br-ahwlan
```

Check DHCP leases:
```bash
cat /tmp/dhcp.leases | grep usb0
```

Verify bridge membership:
```bash
bridge link show | grep usb0
```

## Notes

- Requires reboot after initial configuration to enable gadget mode
- USB-C cable must support data (not just power)
- Only one USB gadget interface is supported
- Throughput is typically 300-350 Mbps
- Works with direct USB-C to USB-A/C cable (not through USB-C dock)
- EUDs are on the same broadcast domain as mesh nodes
- No separate firewall zone needed - usb0 is part of the ahwlan zone

## Troubleshooting

If `usb0` interface doesn't appear:

1. Check if modules are loaded: `lsmod | grep g_ether`
2. Check boot configuration: `cat /boot/config.txt | grep dwc2`
3. Check cmdline: `cat /boot/cmdline.txt | grep modules-load`
4. Check init script logs: `logread | grep usbc-gadget`
5. Manually load module: `modprobe g_ether`

If EUD doesn't get IP:

1. Check if usb0 is in bridge: `brctl show br-ahwlan | grep usb0`
2. Check mesh DHCP status: `/etc/init.d/dnsmasq status`
3. Check UCI bridge config: `uci show network | grep br-ahwlan`
4. Check network reload: `ubus call network reload`

If EUD can't reach mesh nodes:

1. Verify bridge membership: `bridge link show | grep usb0`
2. Check mesh network: `batctl o`
3. Verify firewall: `iptables -L -n -v`

### Android Device Issues

If Android devices don't automatically enable USB network interface:

**Known Limitation:** Android is primarily designed to SHARE internet via USB (USB tethering), not to RECEIVE internet from USB gadgets. Many Android devices don't automatically enable the USB network interface when connected to a USB gadget.

**Possible Solutions:**

1. **Check USB Connection Mode:**
   - Settings → Developer options → Default USB configuration
   - Try: "File Transfer (MTP)", "PTP", or "No data transfer"
   - Some devices need specific modes to recognize network adapters

2. **Check Network Settings:**
   - Settings → Network & internet
   - Look for "Ethernet" or "USB Ethernet" option
   - Some phones show it under "More connection settings"

3. **Try Different USB Protocols:**
   - The role attempts to use CDC-ECM first (better Linux/Android compatibility)
   - Falls back to RNDIS if needed
   - Both protocols are supported by `g_ether`

4. **Root Access (Advanced):**
   - If device is rooted, you can manually enable the interface:
     ```bash
     adb shell
     su
     setprop sys.usb.config rndis,adb
     # or
     svc usb setFunctions rndis
     ```

5. **Alternative: Use USB-A Port:**
   - Some devices work better with USB-A to USB-C adapters
   - Connect phone to Pi's USB-A port instead of USB-C port

**Note:** Not all Android devices support receiving network via USB. This is a device/OS limitation, not a Pi configuration issue.
