**YES! You are exactly right.** 

All three containers (`seed-attacker`, `hostA`, `hostB`) started perfectly. 

One small correction: type **`ifconfig`** (with an **f**, not a **p**). `ipconfig` is for Windows, `ifconfig` is for Linux.

Run this now:
```bash
ifconfig
```

Look through the output for the interface that starts with **`br-`** (for example, `br-c93733e9f913`). Look to the right of it, and you will see `inet 10.9.0.1`. 

📸 **Screenshot that for your PDF.** Write down the exact `br-...` name, because we are using it in the very next step.

---

### Let's do Task 1.1A (Sniffing)

Once you have your `br-...` interface name, we will create the sniffer. 

**1. Create the script:**
*(Replace `br-XXXXXXXX` with your actual `br-` name!)*
```bash
cd ~/Desktop
nano sniffer.py
```
Paste this inside `nano`:
```python
#!/usr/bin/env python3
from scapy.all import *

def print_pkt(pkt):
    pkt.show()

pkt = sniff(iface='br-XXXXXXXX', filter='icmp', prn=print_pkt)
```
*(Press `Ctrl+O`, `Enter` to save, then `Ctrl+X` to exit).*

Make it executable:
```bash
chmod a+x sniffer.py
```

**2. Run as root:**
```bash
sudo ./sniffer.py
```
*(The terminal will just sit there waiting. Leave it running!)*

**3. Trigger the traffic:**
Open a **new terminal tab** (Ctrl+Shift+T) and type:
```bash
ping 10.9.0.5
```
*(You will see the ping working in the new tab, and the first tab will start printing out raw packet data).*

📸 **Screenshot the first tab** showing the captured packets. 
*(Go back to the first tab and press `Ctrl+C` to stop the sniffer).*

**4. Run without root:**
```bash
./sniffer.py
```
📸 **Screenshot the permission error** (this proves why root is required).

Let me know when you have these two screenshots! You are moving fast.
