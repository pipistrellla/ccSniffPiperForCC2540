ccsniffpiper for CC2540 (СС2540 with wireshark)
============
This repository is an adaptation of the **[ccsniffpiper](https://github.com/andrewdodd/ccsniffpiper)** repository for working with the CC2540 BLE sniffer.


How to Use
==========
## Setting up and Running the CC2540 BLE Sniffer

Follow the steps below to set up your environment and start capturing BLE packets using the CC2540 sniffer.

### 1. Set up the Python virtual environment

First, create and activate a virtual environment:

```bash
python3 -m venv venv
source venv/bin/activate
```
Then, install the required dependencies:
```bash
pip install pyusb
```
If you get a USB permission error, please copy 99-cc2531-sniffer.rules (mb u need to write your device data) to

- /etc/udev/rules.d 

and reload udev or reboot.
```bash
$ cp 99-cc2531-sniffer.rules /etc/udev/rules.d
$ sudo udevadm control --reload-rules
```
otherwise reboot
```bash
$ sudo reboot
sudo udevadm control --reload-rules
```

### 2. Run the Python script
Start the Python script to begin the sniffer process:

```bash
python ccsniffpiper.py
```

### 3. Open Wireshark

Open Wireshark and start capturing data from the sniffer interface. Run the following command:

```bash
sudo wireshark -k -i /tmp/ccsniffpiper
```
Note: You may need to use sudo depending on your system configuration.

### 4. Start sniffing in the Python console
Once Wireshark is up and running, type 's' and press "enter" in the Python console to start the sniffer (for me in VSCode).
This will allow you to start sniffing BLE packets using the CC2540 sniffer and Wireshark.


TI's Packet Sniffer Payload Definition
======================================
This is just documentation of the packet format from the TI USB dongle. It is not complete and is based on mostly guesswork from the user manual for the TI dongle (which is now out of date) and the existing code in **ccsniffer**. 


    0       1       2       3       4       5       6       7       8       9       10      11 >>
    |_______|_______|_______|_______|_______|_______|_______|_______|_______|_______|_______|_ >>
    |COMMAND|   Length      |           Timestamp           |Packet |  MAC Layer PDU >>>
    |       |               |                               |Length | i.e. the packet
    
    
    
 * **COMMAND**: (1 byte) - Not entirely sure of all of these values. Currently there are only 2:
  * 0x00 - Message is a captured frame
  * 0x01 - Message appears to be a heartbeat of some sort (seems to include the an 8-bit count that goes up by 4 every 2.097 seconds)
 * **Length**: (2 bytes) - The length of the rest of the message
 * **Timestamp**: (4 bytes) - The sniffer's timestamp of the captured packet since the "start" of the capture.
   * **Note Well**: This timestamp is in usecs and is multiplied by 32 (see CC2531 user guide for info)
 * **Packet Length**: (1 byte) - Length of the MAC Layer PDU (i.e. the "frame length" / PHY Header byte)
 * **MAC Layer PDU**: Variable length specified in Packet Length.

 
FAQs
====
### I don't see anything appearing in Wireshark!

 * Check that the sniffer is sniffing in the correct channel.
 * for more information about tool visit original repo: **[ccsniffpiper](https://github.com/andrewdodd/ccsniffpiper)**
 * Check that you have opened the named pipe that is being piped to.  
   *In particular, I would recommend reading the "How to use" section carefully.*
 
