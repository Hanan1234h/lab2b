**THIS IS PERFECT!** 📸

Look at your Wireshark screen—it is absolutely beautiful. You can see exactly what happened:
*   **Packet 3:** Source `1.2.3.4` sent an ICMP request to `10.9.0.5`. (This is your fake packet!)
*   **Packet 4:** Host A (`10.9.0.5`) believed the fake IP and sent the reply back to `1.2.3.4`.

**Take your screenshot right now.** Make sure Packet #3 is highlighted so the middle pane shows the IP header details, or just capture the whole list showing packets 3 and 4. Save this for your report. 

*(Note: Packet 1 is an ARP request from your VM asking for the MAC address of Host A before sending the fake packet. That's normal).*

---

### NEXT: Section 3.3 - Task 1.3 (Traceroute)

Now we are going to write a mini-traceroute program. The goal is to send packets with a TTL (Time To Live) of 1, 2, 3, etc., and see which router IP responds with a "Time Exceeded" error.

You can close Wireshark for now (or leave it running if you want to capture the traceroute traffic, but it's not strictly required for the screenshot—the terminal output is what matters).

**Step 1: Create the script**
Copy and paste this into your terminal. This script automatically loops through TTLs 1 to 15 and prints the IP address of each router it hits:

```bash
cd ~/Desktop
cat <<EOF > traceroute.py
#!/usr/bin/env python3
from scapy.all import *

target = '8.8.8.8'

for ttl in range(1, 16):
    # Create IP packet with increasing TTL
    a = IP(dst=target, ttl=ttl)
    b = ICMP()
    
    # Send packet and wait for 1 reply (timeout after 2 seconds)
    reply = sr1(a/b, verbose=0, timeout=2)
    
    if reply is None:
        print(f"{ttl} : * * * (No response)")
    else:
        # Print the IP of the router that replied
        print(f"{ttl} : {reply.src}")
        
        # If the reply is an Echo Reply (type 0), we reached the destination
        if reply.type == 0:
            print("Reached destination!")
            break
EOF
chmod a+x traceroute.py
```

**Step 2: Run the script**
```bash
sudo ./traceroute.py
```
*(It will take a few seconds to run as it waits for responses).*

**Step 3: Take Screenshot**
📸 **Take a screenshot of the terminal output.** It should show a list of IP addresses (e.g., `1 : 10.0.2.2`, `2 : 192.168.x.x`, etc.) showing the path your packets took to reach Google (`8.8.8.8`). 

Save that screenshot, and let me know when you're done! We are almost at the finish line—the final task (Task 1.4) is next!
