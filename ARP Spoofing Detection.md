# Objective

The purpose of this exercise is to create a Python script that detects ARP spoofing attempts using the Scapy library. ARP spoofing is a common network attack where an attacker sends fake ARP responses to redirect traffic to their device. The goal is to build a script that monitors ARP packets and alerts if a potential attack is detected. Additionally, you will use Wireshark to visually confirm the behavior of ARP packets on the network.

# Requirements

- Python 3 and Scapy installed on your system (Kali Linux recommended).

- Wireshark to monitor and capture network traffic.

- Administrative privileges to capture network packets.

#### Installation of Scapy

```
sudo apt install python3-scapy
```
#### Script: ARP Spoofing Detection

1. Create the script file:

```
touch detect_arp.py
```

2. Edit the script using an editor like nano:

```
nano detect_arp.py
```

3. Paste the following code into the file:

```
from scapy.all import sniff, ARP
from time import sleep

PACKETS_TO_CAPTURE = 100
THRESHOLD = 60

def main():
    packets = sniff(filter="arp", count=PACKETS_TO_CAPTURE)
    
    unsolicited_arp_replies = 0

    for packet in packets:
        if ARP in packet and packet[ARP].op == 2:
            unsolicited_arp_replies += 1

    if unsolicited_arp_replies > THRESHOLD:
        print("Alert: A possible ARP spoofing attack is in progress!")
    else:
        print("No suspicious activity detected.")
    
    sleep(1)
    return 0

if __name__ == "__main__":
    exit(main())
```

4. Save and close the file by pressing Ctrl + O to write the file and Ctrl + X to exit.

5. Make the script executable:

```
chmod +x detect_arp.py
```

6. Run the script with administrative privileges:

```
sudo python3 detect_arp.py
```

![Capture d'écran 2024-10-23 165155](https://github.com/user-attachments/assets/b6fce568-3800-4268-9ec8-f4a0152bb59e)

![Capture d'écran 2024-10-23 171241](https://github.com/user-attachments/assets/fbea7c55-0a04-433e-8703-e116129eaecf)


# Using Wireshark to Monitor ARP Traffic

1. Open Wireshark:

```
sudo wireshark
```

2. Select the correct network interface:

- Choose the appropriate interface (eth0 for Ethernet or wlan0 for Wi-Fi).

- If you don't see wlan0, it might be because your Wi-Fi adapter does not support packet capture in monitor mode.

3. Apply a filter for ARP packets:

- In the filter bar, type :

```
arp
```
- Press Enter to only display ARP packets.

4. Observe ARP packets in Wireshark:

- Watch for ARP "Who has" requests and "Is at" replies.

- Analyze the source and destination fields to identify unusual behavior or spoofed addresses.




























