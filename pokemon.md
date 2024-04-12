nmap -sC -sV <ip>

22/tcp open ssh 

80/tcp open http 

gobuster dir -u http://10.10.98.108/ -w usr/share/wordlists/dirb/common.txt  

ssh pokemon@10.10.98.108 

whoami

pokemon@root:~/Desktop$ ls
P0kEmOn.zip


