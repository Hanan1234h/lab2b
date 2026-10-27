**PERFECT!** 📸 Those Wireshark screenshots are flawless. 

*   **Image 1** shows Packet 3: `Source: 1.2.3.4` -> `Destination: 10.9.0.5` (Your spoofed request).
*   **Image 2** shows Packet 4: `Source: 10.9.0.5` -> `Destination: 1.2.3.4` (Host A's reply to your fake IP).
*   **Image 3** shows your Traceroute script working perfectly. *(Note: It says "Reached destination!" at hop 1. This is because the VirtualBox NAT network is acting as a proxy and immediately answering the ping. It's a perfectly valid output to show your script works!)*

You are ready for the **final and most important part of the lab: Section 3.4 (Sniff-and-then-Spoof).**

---

### Section 3.4: Sniff and then Spoof (Task 1.4)

In this task, you will run a program on the VM that watches for *any* ICMP echo request. When it sees one, it immediately spoofs a reply so the sender thinks the target is alive.

**Step 1: Create the Sniff-and-Spoof script**
Copy and paste this into your terminal:
```bash
cd ~/Desktop
cat <<EOF > sniff_spoof.py
#!/usr/bin/env python3
from scapy.all import *

def spoof_pkt(pkt):
    # Check if it is an ICMP Echo Request (type 8)
    if ICMP in pkt and pkt[ICMP].type == 8:
        print(f"Sniffed request from {pkt[IP].src} to {pkt[IP].dst}")
        
        # Construct the spoofed Echo Reply (type 0)
        ip = IP(src=pkt[IP].dst, dst=pkt[IP].src)
        icmp = ICMP(type=0, id=pkt[ICMP].id, seq=pkt[ICMP].seq)
        
        # Preserve the original payload
        if pkt.haslayer(Raw):
            payload = pkt[Raw].load
            newpkt = ip/icmp/payload
        else:
            newpkt = ip/icmp
            
        send(newpkt, verbose=0)
        print(f"Sent spoofed reply from {ip.src} to {ip.dst}")

# Sniff on your br- interface
pkt = sniff(iface='br-ef011bdcf807', filter='icmp', prn=spoof_pkt)
EOF
chmod a+x sniff_spoof.py
```

**Step 2: Run the sniffer on your VM**
Keep this running in your first terminal tab:
```bash
sudo ./sniff_spoof.py
```

**Step 3: Ping from Host A (The User Container)**
1. Open a **new terminal tab**.
2. Enter the Host A container:
```bash
docksh 62cf
```
3. Now, we need to do **three separate pings** and take a screenshot of the output for each:

**Ping 1: A non-existing host on the Internet**
```bash
ping -c 2 1.2.3.4
```
📸 **Screenshot:** Take a screenshot of the terminal showing that the ping **succeeded** (because your sniffer faked the reply).

**Ping 2: An existing host on the Internet**
```bash
ping -c 2 8.8.8.8
```
📸 **Screenshot:** Take a screenshot showing the ping succeeded.

**Ping 3: A non-existing host on the LAN**
```bash
ping -c 2 10.9.0.99
```
📸 **Screenshot:** Take a screenshot showing the ping **failed** (you will likely see "Destination Host Unreachable" or it will just hang and fail). 

**Step 4: Get the Routing Output**
While still inside the Host A container, run this command and take a screenshot:
```bash
ip route get 1.2.3.4
```

---

### 🚨 VERY IMPORTANT FOR YOUR REPORT (The ARP Concept) 🚨

When you do Ping 3 (`10.9.0.99`), it will fail. In your report, you **must** explain why. Here is the answer you should write down:

> "Ping 3 to `10.9.0.99` failed because `10.9.0.99` is on the same local area network (LAN) as Host A. Before Host A can send an ICMP echo request to a LAN IP, it must first use ARP to find the MAC address of `10.9.0.99`. Since the machine does not exist, no one replies to the ARP request. Because the MAC address cannot be resolved, the operating system never sends the ICMP echo request onto the network. Therefore, our sniffing program never sees the packet and cannot spoof a reply. In contrast, Pings 1 and 2 were routed to the default gateway, so the ICMP packets were sent out immediately and our sniffer was able to catch and reply to them."

Let me know when you have all those screenshots! You're almost done!
