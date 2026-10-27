Perfect! This is exactly what we need to see. 

Your Host A container ID is **`62cf11b7afe5`**. 

Now, do this:

1. Type this command to get inside Host A (you only need the first few letters of the ID):
```bash
docksh 62cf
```
*(Your prompt will change to look like `root@62cf11b7afe5:/#`)*

2. Now, run the ping **from inside the container**:
```bash
ping -c 2 128.230.0.1
```

3. Click back on your **first tab** (the one running `sudo ./filter3.py`). You will immediately see the ICMP packets! 

📸 **Take your screenshot for Filter 3.**

Once you have that, you can press `Ctrl+C` to stop the sniffer, and type `exit` to leave the container. **Section 3.1 is done!** Let me know when you are ready for Task 1.2 (Spoofing).
