**You did it perfectly.** These three screenshots are *exactly* what the grader is looking for for Task 2. 

You have:
1. The corrected Dockerfile 📸
2. The successful build 📸
3. The `owner.txt` output from inside the container 📸

You are officially done with Task 1 and Task 2. Great job. Now, let's get that Labsetup downloaded and start Task 3.

Since you haven't downloaded `Labsetup.zip` yet, here is exactly how to do it from inside your Ubuntu VM, followed by the commands to start Task 3.

### Step 1: Download Labsetup.zip
1. Inside your Ubuntu VM, open the **Firefox** web browser (the orange fox icon on the left).
2. Go to: **`https://seedsecuritylabs.org/labs_20.04/Networking/Sniffing_Spoofing/`**
3. Scroll down to the **Lab Environment Setup** section. Click the **`Labsetup.zip`** link to download it.
4. (It will save to your `~/Downloads` folder automatically).

### Step 2: Clean up Task 2 and Set up the Lab
Go back to your terminal and copy/paste these commands:

```bash
# 1. Clean up the Docker container from Task 2 to avoid conflicts
sudo docker rm -f $(sudo docker ps -aq)

# 2. Go to your Downloads folder and unzip the Labsetup file
cd ~/Downloads
unzip Labsetup.zip

# 3. Move into the Labsetup folder
cd Labsetup

# 4. Build and start the new containers
dcbuild
dcup -d
```
*(Note: `dcup -d` runs it in the background so it won't freeze your terminal).*

### Step 3: Find your Network Interface
```bash
ifconfig
```
📸 **SCREENSHOT 4:** Look for an interface that starts with `br-` (e.g., `br-c93733e9f913`) that has the IP address `10.9.0.1`. **Take a screenshot of this**, and **write down that exact name** (the `br-xxxx` part), because you will need it for the next step!

---

### Step 4: Section 3.1 - Task 1.1A (Sniffing)
Let's create the sniffer. 

```bash
# Go back to your desktop
cd ~/Desktop

# Create the script (REPLACE br-XXXXXXXX with your actual interface name from Step 3!)
nano sniffer.py
```
*(If you don't know how to use nano, just use this command instead, but **change the br-XXXX** to your actual interface name!)*

```bash
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

### Step 5: Run it as root and non-root
**First, run it WITH root (sudo):**
```bash
sudo ./sniffer.py
```
*(While it's running, open a **new terminal tab** by pressing `Ctrl+Shift+T`. In the new tab, ping one of the containers by typing `ping 10.9.0.5`. You should see the Scapy output in your first terminal).*
📸 **SCREENSHOT 5:** Take a screenshot of the sniffer successfully capturing the ICMP packets. Then press `Ctrl+C` to stop it.

**Second, run it WITHOUT root:**
```bash
./sniffer.py
```
📸 **SCREENSHOT 6:** Take a screenshot showing the **PermissionError** (Operation not permitted). This proves why root is needed.

Let me know when you have these screenshots! Once you have them, we will do the filters (Task 1.1B) and then move on to the spoofing tasks.

##HI
**Yes, it downloaded successfully!** You can see the `200 OK` and `'Labsetup.zip' saved [1006/1006]` at the bottom. 

*(Note: 1006 bytes is very small, but that is normal for this specific lab because it only contains configuration files, not large operating system images).*

**Yes, go ahead and run those exact 4 commands you typed out.** 

Here is the block again for easy copy-pasting:

```bash
# 1. Unzip the file
unzip Labsetup.zip

# 2. Move into the folder
cd Labsetup

# 3. Build the containers (this will take a few minutes)
dcbuild

# 4. Start the containers in the background
dcup -d
```

### ⚠️ What to watch out for:
If you get an error during `unzip` that says something like *"End-of-central-directory signature not found"*, it means the website sent you a broken/HTML file instead of the real zip. Let me know if that happens, and I'll give you the direct GitHub link to download it instead.

### Next Step (after `dcup -d` finishes):
Once the containers are running in the background, you need to find your network interface. Run this:
```bash
ifconfig
```
Look for the interface starting with **`br-`** that has `inet 10.9.0.1`. 

📸 **Take a screenshot of that `ifconfig` output!** You will need that `br-...` name for the Scapy scripts. Let me know when you have it!
