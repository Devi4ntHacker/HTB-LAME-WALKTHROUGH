<h1>HTB "LAME" Walkthrough 😒</h1>

<h3>Goals</h3>Get the user flag and the root flag.
<h3>Target IP</h3> 10.10.10.3

<h2>NMAP Scan</h2>
<pre>
sudo nmap -Pn -sV -sC -O --min-rate 10000 -p- 10.10.10.3
Starting Nmap 7.93 ( https://nmap.org ) at 2024-02-21 15:14 GMT
Nmap scan report for 10.10.10.3
Host is up (0.081s latency).
Not shown: 65530 filtered tcp ports (no-response)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 2.3.4
|_ftp-anon: Anonymous FTP login allowed (FTP code 230)
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.10.14.7
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      vsFTPd 2.3.4 - secure, fast, stable
|_End of status
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
| ssh-hostkey: 
|   1024 600fcfe1c05f6a74d69024fac4d56ccd (DSA)
|_  2048 5656240f211ddea72bae61b1243de8f3 (RSA)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.0.20-Debian (workgroup: WORKGROUP)
3632/tcp open  distccd     distccd v1 ((GNU) 4.2.4 (Ubuntu 4.2.4-1ubuntu4))
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: WAP|broadband router|remote management|printer|general purpose
Running (JUST GUESSING): Linux 2.4.X|2.6.X (92%), Arris embedded (92%), Dell embedded (92%), Linksys embedded (92%), Tranzeo embedded (92%), Xerox embedded (92%), Dell iDRAC 6 (92%)
OS CPE: cpe:/o:linux:linux_kernel:2.4.36 cpe:/h:dell:remote_access_card:6 cpe:/h:linksys:wet54gs5 cpe:/h:tranzeo:tr-cpq-19f cpe:/h:xerox:workcentre_pro_265 cpe:/o:linux:linux_kernel:2.4 cpe:/o:linux:linux_kernel:2.6 cpe:/o:dell:idrac6_firmware
Aggressive OS guesses: DD-WRT v24-sp1 (Linux 2.4.36) (92%), Arris TG862G/CT cable modem (92%), Dell Integrated Remote Access Controller (iDRAC6) (92%), Linksys WET54GS5 WAP, Tranzeo TR-CPQ-19f WAP, or Xerox WorkCentre Pro 265 printer (92%), Linux 2.4.21 - 2.4.31 (likely embedded) (92%), Linux 2.4.27 (92%), Linux 2.6.27 - 2.6.28 (92%), Linux 2.6.8 - 2.6.30 (92%), Dell iDRAC 6 remote access controller (Linux 2.6) (92%), Supermicro IPMI BMC (Linux 2.6.24) (92%)
No exact OS matches for host (test conditions non-ideal).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 2h30m00s, deviation: 3h32m09s, median: 0s
| smb-os-discovery: 
|   OS: Unix (Samba 3.0.20-Debian)
|   Computer name: lame
|   NetBIOS computer name: 
|   Domain name: hackthebox.gr
|   FQDN: lame.hackthebox.gr
|_  System time: 2024-02-21T10:18:26-05:00
|_smb2-time: Protocol negotiation failed (SMB2)
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 268.00 seconds
</pre>
After looking through our Nmap scan we see that we have ports 21,22,139,445, and 3632 open.

<h2>PORT 21</h2>
port 21(FTP) shows that we have an anonymous login available so lets check if we can get in this way.
<pre>
  ftp 10.10.10.3
Connected to 10.10.10.3.
220 (vsFTPd 2.3.4)
Name (10.10.10.3:root): anonymous
331 Please specify the password.
Password:
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
226 Directory send OK.
ftp> pwd
257 "/"
ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
226 Directory send OK.
</pre>
We are able to login with the credentials anonymous/blank
Unfortunately we don't have access to anything.

Revisiting Port 21 we can see that we have vsFTPd 2.3.4, let's search Metasploit for any known vulnerabilities.
<pre>
  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution
</pre>
Unfortunately this exploit doesn't work, lets check the other ports.

<h2>PORT 445</h2>
Port 445 is open and tells us that the machine is running Samba smbd 3.0.20-Debian, so let's see if this version has any known vulnerabilities.

Using Metasploit we find that this version of Samba is vulnerable
<pre>
  exploit/multi/samba/usermap_script  2007-05-14       excellent  No     Samba "username map script" Command Execution
</pre>

This exploit WORKS!

now lets see who we are
<pre>
  whoami
  root
</pre>
We are root! Let's look for the flags now!
by changing directory to "/root" and using the "ls" command we can see that this is where the root.txt file is located.
by exploring the machine we find the user flag located in "/home/makis" and using the "ls" command we can see that this is where the user.txt file is located.
<h2>FLAGS</h2>
root flag:619648c877f0eb27d3c908492241ab8b

user flag:83ba5d5b88cd7efa7d763b8aae0dfa40
