## Instruction Manual: Setting Up Ubuntu 20.04 Raspberry Edition on Your Raspberry Pi

This guide will assist you in installing or reinstalling Ubuntu 20.04 Raspberry Edition on your Raspberry Pi 3B. Follow these steps carefully to ensure a smooth setup.

### Prerequisites
- Raspberry Pi 3B
- SD card with at least 8GB capacity
- SD card reader
- Monitor and keyboard
- Internet connection

### Installation Guide

#### Step 1: Download and Install Raspberry Pi Imager
1. **Download the Imager**: Go to the Raspberry Pi [download page](https://downloads.raspberrypi.org/imager/imager_latest_amd64.deb) and download the latest version of Raspberry Pi Imager for Ubuntu.
   
2. **Install the Imager**:
   ```bash
   sudo dpkg -i imager_latest_amd64.deb
   sudo apt-get install -f
   ```
   This installs the Raspberry Pi Imager and fixes any dependency issues.

#### Step 2: Write Ubuntu Image to SD Card
1. **Insert your SD Card** into the SD card reader and connect it to your computer.
   
2. **Launch Raspberry Pi Imager**: Open the Imager application from your applications menu.

3. **Configure Imager**:
   - Click ‘**CHOOSE OS**’.
   - Navigate to ‘**Other general-purpose OS**’.
   - Select ‘**Ubuntu**’ and choose ‘**Ubuntu 20.04 LTS (RPi 2/3/4/400) 32-bit**’.

4. **Select the SD Card**:
   - Click ‘**CHOOSE SD CARD**’.
   - Select your SD card from the list.

5. **Write the Image**:
   - Click ‘**WRITE**’ and confirm to start the writing process. This will take a few minutes.

6. **Eject the SD Card** safely from your computer once the process completes.

#### Step 3: Initial Setup on Raspberry Pi
1. **Insert the SD Card** into your Raspberry Pi.

2. **Connect the Raspberry Pi** to a monitor and a keyboard.

3. **Power On** the Raspberry Pi and wait as it boots up for the first time.

4. **Log In**:
   - Username: `ubuntu`
   - Password: `ubuntu`
   - Note: It may take a few attempts to log in due to background setup processes.

5. **Change the Default Password** as prompted:
   - New password: `<PASSWORD>`

#### Step 4: Disable Automatic Updates
1. **Open a Terminal** and enter the following command:
   ```bash
   sudo apt -y remove unattended-upgrades
   ```

#### Step 5: Connect to Wi-Fi
1. **Edit Netplan Configuration**:
   ```bash
   sudoedit /etc/netplan/50-cloud-init.yaml
   ```

2. **Configure Wi-Fi Settings**: Add the appropriate configuration block under `wifis`. Ensure correct indentation:

   - **For open networks**:
     ```yaml
     wifis:
       wlan0:
         optional: true
         access-points:
           "<SSID-NAME>": {}
         dhcp4: true
     ```

   - **For WPA-secured networks**:
     ```yaml
     wifis:
       wlan0:
         optional: true
         access-points:
           "<SSID-NAME>":
             password: "<PASSWORD>"
         dhcp4: true
     ```

3. **Apply Changes**:
   ```bash
   sudo netplan apply
   ```

4. **Check Connectivity**:
   ```bash
   ip a
   ```
   Note the `inet` address under `wlan0`, indicating your IP address on the network.

#### Step 6: Verify Network Configuration with Nmap
1. **Install Nmap** if not already installed:
   ```bash
   sudo apt-get install nmap
   ```

2. **Scan for Devices on Your Network**:
   ```bash
   sudo nmap -sn <...>.<...>.<...>.0/24
   ```
   Identify your Raspberry Pi in the list by its MAC address or the manufacturer hint.

### Troubleshooting WiFi Connection
If your WiFi connection fails, refer to detailed guides like [Ubuntu 20.04 Connect to WiFi from Command Line](https://linuxconfig.org/ubuntu-20-04-connect-to-wifi-from-command-line) or reapply configurations with debugging:
```bash
sudo netplan --debug apply
```

### Setting the Correct Time and Timezone
1. **Set the Current Time**:
   ```bash
   sudo date -s "Month Day HOUR:MINUTES:SECONDS TIMEZONE YEAR"
   ```

2. **Configure the Timezone**:
   ```bash
   sudo dpkg-reconfigure tzdata
   ```
   Follow the prompts to select

 the correct timezone (e.g., Brussels).

3. **Install `htpdate` for HTTP-based Time Synchronization**:
   ```bash
   sudo apt-get install htpdate
   ```

By following these steps, your Raspberry Pi will be prepared and configured with Ubuntu 20.04 Raspberry Edition, connected to the internet, and correctly set up for any robotic projects or general use. Ensure all steps are followed correctly for a successful setup.
