![20250809_124834](https://github.com/user-attachments/assets/74b077e1-6b24-40ff-9dd8-d26f9c95fbab)
# Orange Pi R1 Plus LTS Router Setup (OpenWrt)

This document explains how to set up an **Orange Pi R1 Plus LTS** running OpenWrt as a router connected to your home network.  
It also covers how to connect a second device to it, find its IP addresses, and test connectivity from the terminal.

---

## 📦 Hardware Requirements

- **Orange Pi R1 Plus LTS** with OpenWrt installed
- Ethernet cables (minimum 2)
- Home router with internet connection
- (Optional) Network switch
- A computer for initial configuration

---

## 📥 Step 0 — Flashing OpenWrt Image to Orange Pi

Before you can configure your Orange Pi as a router, you need to write the OpenWrt firmware image to its microSD card. You can use for example BalenaEtcher to do that.
Find the image here:

https://drive.google.com/drive/folders/173IOA9AQr6Jy0bkmaNmt8QL3bSG5_xoq?usp=sharing

---

## 🔌 Physical Connection Overview

**Before configuration:**
1. **PC** → (Ethernet) → **LAN port** of Orange Pi  
2. Power on Orange Pi.

**After configuration:**
---

## ⚙️ Step 1 — Access OpenWrt Interface

1. Connect your PC to the **LAN** port of the Orange Pi.
2. Open a browser and go to:  http://192.168.1.1

*(If your OpenWrt is already at `192.168.2.1`, use that instead.)*
3. Set a **root password**.

---

## ⚙️ Step 2 — Configure LAN Interface

> This ensures your router’s LAN network does not conflict with your home router’s network.

1. In **LuCI** (OpenWrt web UI):
- Go to **Network → Interfaces → LAN → Edit**
- Change:
  ```
  IPv4 address: 192.168.5.1
  Netmask: 255.255.255.0
  ```
- Ensure **DHCP Server** is **enabled**.
2. Click **Save & Apply**.
3. During the 90-second confirmation:
- Unplug and reconnect your PC’s Ethernet cable.
- Reopen browser at:
  ```
  http://192.168.5.1
  ```

---

## ⚙️ Step 3 — Configure WAN Interface

1. In LuCI:
- Go to **Network → Interfaces → WAN → Edit**
- Set Protocol to:
  ```
  DHCP Client
  ```
2. Save & Apply.

---

## 🔥 Step 4 — Firewall Check

- **LAN Zone**: Input = Accept, Output = Accept, Forward = Accept → Forward to WAN  
- **WAN Zone**: Input = Reject, Output = Accept, Forward = Reject

---

## 🔗 Step 5 — Final Connection

Connect:
- **Home Router LAN port → Orange Pi WAN port**
- **Orange Pi LAN port → Switch or Direct to Devices**



---

## 🧪 Step 6 — Testing the Network

From a PC connected to the Orange Pi LAN:

```bash
# Check your IP address
ip a

# Your IP should be something like 192.168.5.x
# Gateway should be 192.168.5.1

# Test internet connectivity
ping -c 4 google.com
# If ping works, your router is functional.
```
## Finding the Orange Pi Router's WAN IP
The WAN IP is the address given to your Orange Pi by your main home router.
You need this if you want to SSH into the Orange Pi from the main network.

```bash
# SSH into LAN side first
ssh root@192.168.5.1

# Show all network interfaces
ip a

# Look for the WAN interface (likely eth0)
# Example output:
# 3: eth0: ...
#     inet 192.168.0.120/24 brd 192.168.0.255 scope global eth0
```
---
## 📋 Checking Connected Devices
``` bash
cat /tmp/dhcp.leases
```
---
## ✅ Summary of Useful Commands
```bash
# SSH into Orange Pi LAN side
ssh root@192.168.5.1

# Show IP addresses for all interfaces
ip a

# List connected LAN devices
cat /tmp/dhcp.leases

# Ping to test internet
ping -c 4 google.com

# Network scan (from PC on main router)
sudo nmap -sn 192.168.0.0/24
