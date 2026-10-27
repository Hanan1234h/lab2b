**PERFECT!** 📸 

You nailed it. The screenshots show the `echo-request` going to `128.230.0.1` and the `echo-reply` coming back. That is exactly what your instructor wants for Filter 3.

**Section 3.1 is officially 100% COMPLETE!** You can stop the sniffer (`Ctrl+C`) and close the extra tabs.

Now, let's move on to the really fun part: **Section 3.2 - Task 1.2: Spoofing ICMP Packets.**

In this task, you are going to forge an ICMP packet with a fake source IP address and send it to Host A (`10.9.0.5`). We will use **Wireshark** to prove that the packet was sent with the fake IP.

### Step 1: Open Wireshark
1. In your Ubuntu VM, open **Wireshark** (you can find it in the Applications menu or by typing `sudo wireshark` in the terminal).
2. Select your `br-ef011bdcf807` interface and double-click it to start capturing. (It might ask for your password, `dees`).
3. Let it run in the background. We will come back to it.

### Step 2: Create the Spoofing Script
Go back to your terminal and create this script. I have already set the fake source IP to `1.2.3.4` and the destination to Host A (`10.9.0.5`).

```bash
cd ~/Desktop
cat <<EOF > spoof.py
#!/usr/bin/env python3
from scapy.all import *

# Create the IP packet with a fake source IP
a = IP()
a.dst = '10.9.0.5'
a.src = '1.2.3.4'

# Create the ICMP echo request
b = ICMP()

# Stack them and send
p = a/b
send(p)
EOF
chmod a+x spoof.py
```

### Step 3: Run the Script
Run the script as root:
```bash
sudo ./spoof.py
```
*(You should see `Sent 1 packets.` printed on the screen).*

### Step 4: Stop Wireshark and Take Screenshot
1. Go back to **Wireshark** and click the red square **Stop** button at the top left.
2. Look at the captured packets. You should see an **ICMP Echo (ping) request**.
3. Click on that packet.
4. Look at the **middle pane** (the IP header details). You should see **`Source: 1.2.3.4`** and **`Destination: 10.9.0.5`**.

📸 **SCREENSHOT TIME:**
Take a screenshot of Wireshark showing:
- The packet list at the top.
- The `Source: 1.2.3.4` in the middle pane.

*(Note: Depending on how the network routes the fake reply, you might also see an Echo Reply coming back to `1.2.3.4`. If you see it, include it in the screenshot!).*

Save that screenshot and let me know when it's done! We are flying through this now. Next up is **Task 1.3 (Traceroute)**.
