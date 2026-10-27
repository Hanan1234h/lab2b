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

**HII**
##
##
**YES! That screenshot is PERFECT.** 📸 

You have exactly what you need for Task 1.1A: the root run (capturing packets) and the non-root run (the `PermissionError: [Errno 1] Operation not permitted` you just captured). Save that image for your report!

Now, let's knock out **Task 1.1B (Filters)**. Yes, go ahead and run that block of code I gave you. 

Here is the exact step-by-step to get your screenshots for the three filters. *(Note: For Filter 2, if `telnet` says "command not found", just use `nc 10.9.0.5 23` instead).*

---

### Filter 1: Capture only ICMP
1. Create and run the script:
```bash
sudo ./filter1.py
```
2. Open a **new terminal tab** (`Ctrl+Shift+T`) and run:
```bash
ping -c 2 10.9.0.5
```
3. Go back to the first tab. You will see the ICMP packets. 
📸 **Take a screenshot of the captured packet.**
4. Press `Ctrl+C` in the first tab to stop the sniffer.

### Filter 2: Capture TCP from 10.9.0.5 to port 23
1. Run the script:
```bash
sudo ./filter2.py
```
2. Open a **new terminal tab** and trigger a TCP connection to port 23 (Telnet) on Host A:
```bash
telnet 10.9.0.5
```
*(If it says telnet is not installed, just run: `nc 10.9.0.5 23`)*
3. Go back to the first tab. You should see the TCP SYN packet trying to connect to port 23.
📸 **Take a screenshot of the captured TCP packet.**
4. Press `Ctrl+C` in the first tab to stop the sniffer.

### Filter 3: Capture traffic to/from subnet 128.230.0.0/16
1. Run the script:
```bash
sudo ./filter3.py
```
2. Open a **new terminal tab** and ping that fake subnet:
```bash
ping -c 2 128.230.0.1
```
3. Go back to the first tab. You will see the outgoing ICMP request going to `128.230.0.1`. 
📸 **Take a screenshot of the captured packet.**
4. Press `Ctrl+C` in the first tab to stop the sniffer.

---

Let me know when you have those three screenshots! Next up is **Section 3.2 (Spoofing ICMP Packets)**, which is where things get really fun.

55
55
55
**Don't worry, you don't need to log in!** 

The fact that you reached the `login:` prompt is **perfect**. It means your VM successfully established a TCP connection to port 23 on Host A, which is exactly the traffic we need to capture. 

Here is what to do right now:

### 1. Stop Telnet
Go to the tab where telnet is running and press **`Ctrl + ]`** (Control and the right bracket key). Then type `quit` and press Enter to close the connection.

### 2. Take the Screenshot for Filter 2
Click on the **first tab** (the one running the sniffer). 
*   If you see TCP packets printed out, **📸 TAKE A SCREENSHOT**.
*   **⚠️ WARNING:** If the sniffer tab is blank, it's because the filter I gave you was `src host 10.9.0.5`. Since *you* are the one initiating the connection (from the VM), the source is your VM (`10.9.0.1`), not Host A. 

**If the tab is blank, do this quick fix:**
1. Press `Ctrl+C` in the sniffer tab to stop it.
2. Run this command to fix the filter to just look for TCP traffic going *to* port 23:
```bash
sed -i "s/filter='tcp and src host 10.9.0.5 and dst port 23'/filter='tcp and dst port 23'/" filter2.py
```
3. Run the sniffer again: `sudo ./filter2.py`
4. In your other tab, run `telnet 10.9.0.5` again, and you will see the TCP packets. 
5. **📸 TAKE A SCREENSHOT** and press `Ctrl+C` to stop the sniffer.

---

### 3. Let's do Filter 3 (Subnet)
Now for the last filter. 

1. Run the script:
```bash
sudo ./filter3.py
```
2. Open a **new tab** and run:
```bash
ping -c 2 128.230.0.1
```
3. Go back to the first tab, and you will see the ICMP packets going to that subnet.
4. **📸 TAKE A SCREENSHOT** and press `Ctrl+C` to stop the sniffer.

Once you have the screenshots for Filter 2 and Filter 3, **Task 1.1 is completely done!** Let me know when you are ready for Task 1.2 (Spoofing).
