## Instruction Manual: Setting Up Ubuntu 20.04 Raspberry Edition on Your Raspberry Pi

This guide provides detailed instructions on installing Ubuntu 20.04 Raspberry Edition on your Raspberry Pi 3B. The setup involves initial steps performed on a separate computer and subsequent configurations done on the Raspberry Pi.

### Prerequisites
- A Raspberry Pi 3B
- A microSD card (minimum 8GB)
- An external SD card reader
- A separate computer (PC or Mac)
- A monitor, keyboard, and mouse for the Raspberry Pi
- An internet connection for both the separate computer and the Raspberry Pi

### Part 1: Preparing the SD Card (On Your Separate Computer)

#### Step 1: Download and Install the Raspberry Pi Imager
1. **Download the Imager**: 
   - Visit the [Raspberry Pi Imager download page](https://downloads.raspberrypi.org/imager/imager_latest_amd64.deb) and download the imager for Ubuntu (`.deb` file).

2. **Install the Imager**:
   - Open a terminal and run the following commands:
     ```bash
     sudo dpkg -i path_to_downloaded_imager/imager_latest_amd64.deb
     sudo apt-get install -f
     ```
   - Replace `path_to_downloaded_imager` with the directory where you downloaded the `.deb` file.

#### Step 2: Write Ubuntu Image to the SD Card
1. **Insert the SD Card** into the card reader and connect it to your computer.

2. **Open Raspberry Pi Imager**:
   - From your applications menu, launch the Raspberry Pi Imager.

3. **Select the Operating System**:
   - Click ‘**CHOOSE OS**’.
   - Go to ‘**Other general-purpose OS**’, then ‘**Ubuntu**’.
   - Choose ‘**Ubuntu 20.04 LTS (RPi 2/3/4/400) 32-bit**’.

4. **Select the SD Card**:
   - Click ‘**CHOOSE SD CARD**’ and select your SD card.

5. **Write the Image**:
   - Click ‘**WRITE**’ to begin writing Ubuntu to the SD card. Confirm the action if prompted.
   - Once complete, eject the SD card safely.

### Part 2: Initial Setup on the Raspberry Pi

#### Step 3: Booting Ubuntu on the Raspberry Pi
1. **Connect the Raspberry Pi** to a monitor, keyboard, and mouse.
2. **Insert the SD Card** into the Raspberry Pi.
3. **Power On** the Raspberry Pi and wait for it to boot up.
4. **Login**:
   - Default Username: `ubuntu`
   - Default Password: `ubuntu`
   - You will be prompted to change the password. Change it to `turtle1234`.

#### Step 4: Connect to WiFi
1. **Open a Terminal** and install the nano editor (if not installed):
   ```bash
   sudo apt-get update
   ```

2. **Edit the Netplan Configuration**:
   ```bash
   sudo nano /etc/netplan/50-cloud-init.yaml
   ```
   - Insert the configuration for your WiFi network:

   ```yaml
   wifis:
       wlan0:
           optional: true
           access-points:
               "YOUR_SSID":
                   password: "YOUR_WIFI_PASSWORD"
           dhcp4: true
   ```
   - Replace `YOUR_SSID` and `YOUR_WIFI_PASSWORD` with your actual WiFi details.

3. **Apply the Configuration**:
   ```bash
   sudo netplan apply
   ```

4. **Verify the Connection**:
   ```bash
   ip a
   ```
   - Look for the `inet` address under `wlan0`, which is your IP on the network.

#### Step 5: Install SSH Server
1. **Update Packages**:
   ```bash
   sudo apt-get update
   ```

2. **Install OpenSSH Server**:
   ```bash
   sudo apt-get install openssh-server
   ```

3. **Enable and Start SSH Service**:
   ```bash
   sudo systemctl enable ssh
   sudo systemctl start ssh
   ```

4. **Connect via SSH** (from your separate computer):
   - Ensure your PC and the Raspberry Pi are on the same network.
   - Use the IP address found with `ip a`. For example:
     ```bash
     ssh ubuntu@192.168.36.38
     ```

#### Step 6: Verify Network Devices with Nmap
1. **Install Nmap**:
   - Nmap is used to scan your network for connected devices. This is helpful to confirm that your Raspberry Pi is visible on the network.
   ```bash
   sudo apt-get install nmap
   ```



2. **Scan for Devices on Your Network**:
   - Run Nmap to see all devices on your subnet. This helps in identifying your device's IP if unsure.
   ```bash
   sudo nmap -sn <NETWORK>.<SUB_NETOWRK>.<LOCAL_NETWORK>.0/24
   ```
   - This will list all devices connected to your network, showing their IP addresses and MAC addresses. Identify your Raspberry Pi using its IP address or MAC address.

### Part 3: Additional Configurations

#### Step 7: Set Correct Time and Timezone
1. **Set Time and Date**:
   ```bash
   sudo date -s "Month Day HOUR:MINUTES:SECONDS TIMEZONE YEAR"
   ```

2. **Configure Timezone**:
   ```bash
   sudo dpkg-reconfigure tzdata
   ```
   - Follow the prompts to select the correct timezone.

#### Step 8: Disable Automatic Updates
1. **Remove Unattended Upgrades Package**:
   ```bash
   sudo apt-get remove unattended-upgrades
   ```

### Troubleshooting
- **If WiFi Does Not Connect**:
  - Double-check your SSID and password in the netplan configuration.
  - Reapply configurations with:
    ```bash
    sudo netplan --debug apply
    ```
- **For Further WiFi Setup Help**:
  - Visit [Ubuntu 20.04 Connect to WiFi from Command Line](https://linuxconfig.org/ubuntu-20-04-connect-to-wifi-from-command-line).

By following these steps, your Raspberry Pi will be configured with Ubuntu 20.04, connected to the internet via WiFi, and accessible remotely through SSH, ready for any developments or projects.
