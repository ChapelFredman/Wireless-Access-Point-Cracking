## Wireless-Access-Point-Cracking

### Objective
To capture and decrypt a Wi-Fi access point’s password hash by leveraging monitor mode to intercept data from nearby networks.

### Skills Learned
* Network sniffing
* De-authentication attacks
* Password cracking
* Use of Wi-Fi hacking tools (airodump-ng, aireplay-ng, aircrack-ng, hashcat)

### Tools Used
* Kali Linux (or other Linux distribution with Wi-Fi hacking tools)
* Wireless network adapter with monitor mode capability


### Steps

#### 1. Enable Monitor Mode:
To start, ensure you have a compatible wireless card that supports **Monitor Mode**. This allows you to capture packets from surrounding networks without connecting to them. Run the following commands:

1. Identify your wireless card:
    
    ```bash
    
    ifconfig
    iwconfig
    
    ```
    
2. Set the wireless card to **Monitor Mode**:
    
    ```bash
    
    sudo ifconfig <interface> down
    sudo iwconfig <interface> mode monitor
    sudo ifconfig <interface> up
    
    ```
  ##### 1.5. Enable Monitor mode with *airmon-ng*
  Sometimes we get error while changing the card mode with the lasts steps, so we can do it with   airmon-ng:
   ```bash
   sudo airmon-ng start <interface>

   ```
  ###### 1. Sometimes there are Processes that might interfere with setting monitor mode. When that's the case we have to *Kill Conflict Processes*
   ```bash
   sudo airmon-ng check kill

   ```
 
3. Verify with `iwconfig`.


#### 2. Network Discovery:
Now that your wireless card is in Monitor Mode, you can start capturing network traffic, specifically aiming to intercept a **four-way handshake** from a target wireless access point (AP).

1. We'll use the next command to "Sniff" all the wireless access points around you.
   ```bash
   airodump-ng <interface>

   ```
This will display a list of wireless networks, their BSSIDs (MAC addresses), channels, and other information.

2. Choose a network to target. Note its BSSID and channel. (copy them to some note)
   Ctrl + C when you found the Wireless network
   --------------------------------------------------------------------------
#### 3. De-authentication Attack:
Tool: aireplay-ng
Purpose: To force devices to re-authenticate, capturing the handshake.
How-to: Use aireplay-ng to send de-authentication frames to the target network, causing devices to disconnect.

#### 4. Capturing Handshake:
Tool: airodump-ng
Purpose: To capture the four-way handshake during re-authentication.
How-to: Continue running airodump-ng with the target network's channel and BSSID to capture the handshake.

#### 5. Cracking the Hash:
Tools: aircrack-ng or hashcat
Purpose: To break the hashed password.
How-to: Use aircrack-ng to attempt a brute-force attack with a wordlist. hashcat can be used for more advanced attacks and GPU acceleration.


 
