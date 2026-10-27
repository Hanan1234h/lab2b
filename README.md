**YES! Your screenshots are absolutely PERFECT.** 📸 

You can clearly see the `ICMP echo-request` and `echo-reply` packets between `10.9.0.1` and `10.9.0.5`. This is exactly what Screenshot 1 needs to be. Save those images for your report.

Now, do exactly what you asked:

### Step 5: Stop the sniffer and run WITHOUT root
1. Click on the terminal tab where the sniffer is currently running and press **`Ctrl+C`** to stop it.
2. Run this command:
```bash
./sniffer.py
```

### Step 6: Take Screenshot 2
You will immediately see an error that says **`PermissionError: [Errno 1] Operation not permitted`**. 
📸 **Take a screenshot of that error.** This proves why you need root.

---

### NEXT: Task 1.1B (Filters)

Once you have that error screenshot, we are going to do **Task 1.1B**. This is super fast; we just change the `filter` part of the code.

**Filter 1: Capture only ICMP (You basically just did this, but let's make a clean script for it).**
```bash
cd ~/Desktop
cat <<EOF > filter1.py
#!/usr/bin/env python3
from scapy.all import *
def print_pkt(pkt):
    pkt.show()
pkt = sniff(iface='br-ef011bdcf807', filter='icmp', prn=print_pkt)
EOF
chmod a+x filter1.py
```

**Filter 2: Capture TCP from a specific IP to port 23.**
```bash
cat <<EOF > filter2.py
#!/usr/bin/env python3
from scapy.all import *
def print_pkt(pkt):
    pkt.show()
pkt = sniff(iface='br-ef011bdcf807', filter='tcp and src host 10.9.0.5 and dst port 23', prn=print_pkt)
EOF
chmod a+x filter2.py
```

**Filter 3: Capture traffic to/from a specific subnet (like 128.230.0.0/16).**
```bash
cat <<EOF > filter3.py
#!/usr/bin/env python3
from scapy.all import *
def print_pkt(pkt):
    pkt.show()
pkt = sniff(iface='br-ef011bdcf807', filter='net 128.230.0.0/16', prn=print_pkt)
EOF
chmod a+x filter3.py
```

**How to test these and get screenshots:**
*   **For Filter 1:** Run `sudo ./filter1.py`, open a new tab, and `ping 10.9.0.5`. Take a screenshot. Stop it with `Ctrl+C`.
*   **For Filter 2:** Run `sudo ./filter2.py`. Open a new tab and run `telnet 10.9.0.5`. (If telnet isn't installed, just run `nc 10.9.0.5 23`). Take a screenshot of the captured packet. Stop it.
*   **For Filter 3:** Run `sudo ./filter3.py`. Open a new tab and run `ping 128.230.0.1`. Take a screenshot. Stop it.

Take your time, grab your screenshots, and let me know when Task 1.1B is done! You're doing great.
