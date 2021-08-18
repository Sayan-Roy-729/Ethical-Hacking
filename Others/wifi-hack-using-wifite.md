# Hack wifi using Wifite
```bash
sudo wifite
```
It will turn on monitor mode to your network interface wnal0 and will show the available metworks. When you ready, press "ctrl + c" and choose the target wifi (by the number provided by the wifite). Then if you press the enter, it will try to crack and create a txt file named "wordlist-probable.txt" where thousands possible password will be stored to crack. 

Kali linux comes with default wordlist where millions possible passwords are stored. For that execute
```bash
cd /usr/share/wordlist && gzip -d rockyou.txt.gz
```
To know how many passwords are stored, execute
```bash
wc -l rockyou.txt
```
From the output, you can see that there are around 14344392 possible passwords. You can use this file to attack the wifi
```bash
wifite --wpa --dict /usr/share/wordlists/rockyou.txt --kill
```
And if it finds the password, then you are good to go