# Router D-LINK DSL256

![router pic1!](https://github.com/sdebby/HardHacking/blob/main/DSL256_reouter/img/DLINK-256-416x334.jpg)
---
![router pic2!](https://github.com/sdebby/HardHacking/blob/main/DSL256_reouter/img/maxresdefault.jpg)

## THE  MISSION : get admin SSH into the system

### Process:-

* Got UART display:
![pic1!](https://github.com/sdebby/HardHacking/blob/main/DSL256_reouter/img/IMG_7152.jpg)

 ![UART!](https://github.com/sdebby/HardHacking/blob/main/DSL256_reouter/img/IMG_7151.jpg) 
Using TTS USB (1.8 V) 115200 baud rate.

Using cutecom software

Can get a display for booting, but cannot send commands.

***fail***

---

* found original firmware file
  
  open file using binwalk (needed to install some packages)
  
  ***Did not find aniting interesting***

---

* go into web page
  
  found admin and pass from the internet:
  
  admin:admin123
  
  connecting to the website as admin.
  
  in 'services' section found:
  * FTP
  * SSH
  * TELNET

---

* Try connecting to ssh service
  
***fail- 'connection close by host'***

---

* using nmap fo review services and versions

``` bash
Starting Nmap 7.80 ( https://nmap.org ) at 2023-05-18 21:16 IDT
Warning: Servicescan failed to fill product_template (subjectlen: 45, productlen: 80). Capture exceeds length? Match string was line 9370: p/ADB P.DG A4001N WAP, Cisco Telepresence MCU 4505, Digifort Enterprise 6.5, or Telekom Speedport W723V httpd//
Nmap scan report for Broadcom.Home (192.168.1.1)
Host is up (0.0042s latency).
Not shown: 991 closed ports
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         D-Link/Comtrend DSL modem ftp firmware update
22/tcp   open  ssh         Dropbear sshd 2015.67 (protocol 2.0)
23/tcp   open  telnet      Broadcom BCM963268 ADSL router telnetd
80/tcp   open  http        micro_httpd
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: MSHOME)
443/tcp  open  ssl/https?
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: MSHOME)
631/tcp  open  http
4567/tcp open  tram?
MAC Address: 40:9B:CD:D3:2D:58 (D-Link International)
Service Info: OS: Linux; Device: broadband router; CPE: cpe:/o:linux:linux_kernel, cpe:/h:broadcom:bcm963268
```

---

* Using METASPLOIT to try to go into the system as admin.
  
***Falied on every service***
  
---

* connected to telnet service using (admin:admin123)
  
  **got into system!**
  
searched for interested files:
  * cat /ect/shadow
    * file not found
  * cat /etc/passwd
    * BINGO

    ```bash
    admin:Ibc7VMUadvMY2:0:0:Administrator:/:/bin/sh
    support:BRO7LHMuJOWcw:0:0:Technical Support:/:/bin/sh
    home:ZnJRW4hzfwT/c:0:0:Normal User:/:/bin/sh
    nobody:x3yV8vqWrI/do:0:0:nobody for ftp:/:/bin/sh

    ```

  * run this in john the ripper to crack the passwords
    
  **found passwords**

    ```bash
    admin:admin123
    support:orange
    home:12345678
    nobody:admin123
    ```

* try to connect to SSH, telnet, FTP, and HTTP using support user.
  
***fail***

* on HTTP admin panel found service: TR-069.
[WIKI](https://en.wikipedia.org/wiki/TR-069)
A remote managment tool
  * Installing [genieacs](https://genieacs.com/) on VM ubuntu linux
      [installation guide](http://docs.genieacs.com/en/latest/installation-guide.html)
    * install was not successful.
    * Try to install it on DOCKER - not seccessful.
  * Installing [TeamsACS](https://github.com/CA17/TeamsACS)
    * Installation was seccess.
    * Unable to access UI- wrong user and pass
    * Asked a question on [REDDIT](https://www.reddit.com/r/mikrotik/comments/111tu53/teamsacs_is_the_new_mikrotik_tr069_acs_server/jnt5z59/?context=3)


...........
