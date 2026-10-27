# lab2b


Here is everything you need for **Task 2** and the **Task 3 Setup + Section 3.1**, formatted perfectly so you can copy-paste it into your public repo (like a `README.md`) and then run it in the terminal. 

### 🚨 IMPORTANT FOR TASK 3 🚨
Before you start Task 3, you must download the `Labsetup.zip` file. I recommend doing this *inside* the Ubuntu VM using the Firefox browser. You can get it from the SEED Labs website (seedsecuritylabs.org -> Lab Setup -> Packet Sniffing and Spoofing Lab -> Labsetup.zip).

---

# TASK 2: Docker Review (Run these now)

**Step 1: Create the files and fix the Dockerfile**
```bash
# Move into the task2 folder we created earlier
cd ~/task2

# Create the owner.txt file with your name and date
echo "Hanan Abdosh - $(date)" > owner.txt

# Create the corrected Dockerfile
cat <<EOF > Dockerfile
FROM ubuntu:20.04
COPY owner.txt /owner.txt
EOF

# Display the corrected Dockerfile
cat Dockerfile
```
📸 **SCREENSHOT 1:** Take a screenshot showing the terminal output of `cat Dockerfile` (proving you fixed the `COPY` line).

**Step 2: Build and run the container**
```bash
# Build the image
sudo docker build -t owner-test .

# Run the container
sudo docker run -it owner-test /bin/bash
```
📸 **SCREENSHOT 2:** Take a screenshot of the successful `docker build` output (it should say something like "Successfully tagged owner-test:latest").

**Step 3: Prove it worked and exit**
*(You are now inside the container, your prompt should look like `root@<container_id>:/#`)*
```bash
# Verify the file copied successfully
cat /owner.txt

# Exit the container back to your VM
exit
```
📸 **SCREENSHOT 3:** Take a screenshot showing the `cat /owner.txt` output with your name and date inside the container.

---

# TASK 3 Setup (Labsetup & Containers)

**Step 1: Clean up Task 2 and unzip Labsetup**
```bash
# Remove the task2 container to free up resources
sudo docker rm -f $(sudo docker ps -aq)

# Go to your Downloads folder (assuming you downloaded Labsetup.zip via Firefox)
cd ~/Downloads

# Unzip the file
unzip Labsetup.zip

# Enter the folder
cd Labsetup
```

**Step 2: Build and start the containers**
```bash
# Build the container images
dcbuild

# Start the containers in the background (use -d so it doesn't freeze your terminal)
dcup -d
```

**Step 3: Find your interface name**
```bash
# Look for the interface with the IP 10.9.0.1
ifconfig
```
📸 **SCREENSHOT 4:** Take a screenshot of the `ifconfig` output showing the `br-...` interface with IP `10.9.0.1`. 
*(Write down that interface name, e.g., `br-c93733e9f913`, you will need it in the next step).*

---

# SECTION 3.1: Task 1.1A (Sniffing with/without root)

**Step 1: Create the sniffer script**
```bash
# Go back to the desktop
cd ~/Desktop

# Create the python script (IMPORTANT: Replace "br-XXXXXXXX" with your actual interface name from Screenshot 4)
cat <<EOF > sniffer.py
#!/usr/bin/env python3
from scapy.all import *

def print_pkt(pkt):
    pkt.show()

pkt = sniff(iface='br-XXXXXXXX', filter='icmp', prn=print_pkt)
EOF

# Make it executable
chmod a+x sniffer.py
```

**Step 2: Run as ROOT (Screenshot requirement)**
```bash
# Run as root (sudo)
sudo ./sniffer.py
```
*While this is running, open a **new terminal tab** (Ctrl+Shift+T) or use the host machine, and ping the VM or a container (e.g., `ping 10.9.0.5`). You should see the Scapy output printing the ICMP packets.*
📸 **SCREENSHOT 5:** Take a screenshot of the terminal showing the `sniffer.py` successfully capturing packets as root. 
*Press `Ctrl+C` to stop the sniffer.*

**Step 3: Run as NON-ROOT (Screenshot requirement)**
```bash
# Switch to the seed user
su seed

# Try to run the sniffer
./sniffer.py
```
📸 **SCREENSHOT 6:** Take a screenshot of the terminal showing the **PermissionError / Operation not permitted**. 
*Type `exit` to go back to root.*

---
**Notes for your report:**
*   **Why root is needed:** Packet sniffing requires raw socket access (`CAP_NET_RAW` capability). Normal users are not allowed to capture raw network traffic for security reasons.
*   **Task 1.1B Filters:** You will create variations of `sniffer.py` changing the `filter='...'` argument to `filter='tcp and src host 10.9.0.5 and dst port 23'` and `filter='net 128.230.0.0/16'`, run them, and screenshot the results.

Let me know when you have completed this and have the screenshots! You are moving fast now.
