Don't worry, your script isn't broken, and you didn't do anything wrong! 

**Here is why it's stuck:** When you run `ping 128.230.0.1` from your **VM**, the traffic goes out your main internet adapter (`enp0s3`), *not* the Docker network adapter (`br-ef011bdcf807`) that your sniffer is listening on. 

To make the sniffer see it, we need to send the ping from **inside the container** (Host A). The container uses the VM as its gateway, so the traffic *must* pass through the `br-` interface, where your sniffer will catch it.

Here is exactly how to fix it:

### Step 1: Leave the sniffer running
Keep the terminal tab with `sudo ./filter3.py` open and running. 

### Step 2: Open a new terminal tab and go into the container
Open a new tab (`Ctrl+Shift+T`) and run these commands to get a shell inside Host A:

```bash
# Find the container ID for Host A
dockps
```
*(You will see a list. Look for `hostA-10.9.0.5` and copy the first 2 or 3 characters of its ID).*

```bash
# Enter the container (replace 'xx' with the ID you just copied)
docksh xx
```
*(Your prompt will change to look something like `root@b1004832e275:/#`)*

### Step 3: Ping from inside the container
Now, run the ping from *inside* the container:
```bash
ping -c 2 128.230.0.1
```

### Step 4: Take your screenshot!
Go back to your **first tab** (the sniffer). You will immediately see the ICMP packets! 

📸 **Take a screenshot of the captured packet.**

Once you have the screenshot, you can press `Ctrl+C` in the sniffer tab to stop it. 

Let me know when you have it! (Once you do, Section 3.1 is completely finished).
