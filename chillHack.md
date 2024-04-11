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


### Root flag
