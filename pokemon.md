nmap -sC -sV <ip>

22/tcp open ssh 

80/tcp open http 

gobuster dir -u http://10.10.98.108/ -w usr/share/wordlists/dirb/common.txt  

ssh pokemon@10.10.98.108 

whoami

pokemon@root:~/Desktop$ ls
P0kEmOn.zip


unzip P0kEmOn.zip 

inside the zip there is a a file, grass-type.txt, that has hexadecimal that when cedoded gives us the flag for the grass-type pokemon

We find water-type.txt in var/www/html and is has a ceasar cypher (rotated 12 times), we decode it and get the water-type pokemon

locate fire-type

Now we know where the fire-type.txt is. The text in encoded with Base64. We decode it and get the fire type pokemon.
