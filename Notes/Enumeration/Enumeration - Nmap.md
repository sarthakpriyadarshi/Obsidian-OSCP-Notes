## NMAP

Generates high amount of traffic in the scanned machine, so we must know this can be recognized by traffic analyzers or packet scanners. The more ports are open, the more traffic is generated. A scan of all 65535 ports will generate about 4MBs of traffic. A full TCP/UDP scan on all the ports for 254 hosts (eg: 192.168.1.0/24) can reach over 1GB of traffic over the time for the scanned machine. That for sure can be detected.

### TCP SYN Scan

```shell
nmap -sS <target>
```

Send **SYN** request to a machine, with out handshake. In this way a **SYN-ACK** is sent back to the sender and we know the port is open. The requester at the end does not send the final ACK, resulting in less noise in the network. In this way less traffic and less steps are done in the scan process.

### TCP Connect Scan

```shell
nmap <target>
```

Performs a full TCP connection. This is the default method of NMAP. It takes longer because the handshake is completed.

### UDP Scan

```shell
nmap -sU <target>
```

Apart from port-specific protocols, like **SMTP** or others, it sends an **ICMP** (ICMP port unreachable method) packet to the receiver port and wait for response. Here (but not only here) **sudo** is required because the system access the raw socket in order to implement the IPv4 protocol in user space. This is because sending and receiving raw packets requires root access on a Unix or Mac system. On Windows, you will have to use an administrator account.

### Network Sweeping

```shell
nmap -sn <target-range>
```

The discovery sends the UDP requests, but also a TCP SYN packet to port 443 and a TCP ACK packet to port 80 and ICMP requests for the traditional [ping sweep](https://it.wikipedia.org/wiki/Ping_sweep). This is done for each host. For a specific sweep, it could be used a more effective scan with:

```
nmap -p <PORT> <target>
```

### Most common ports

```shell
nmap -sT <target>
```

### Traceroute

```shell
nmap -A <target>
```

### OS Fingerprint

```shell
nmap -O <target>
```

Try to guess the operating system behind the server, based on how the server is responding. This is effective because the TCP/IP stack is implemeted differently in the Operating Systems. The result fingreprint is then matched with a list of fingerprint of many OS. Adding **--osscan-guess** we get a guess of the OS.

### Server Banners

```shell
nmap -sV <target>
```

In order to have a better understanding of the service we can try to read the server fingerprint for that port. This increase the traffic for the scan.

### [NMAP Scripting Engine](https://nmap.org/book/nse.html)

```shell
nmap -sV -sC --script=banner -p 80,443 <target>
```

With the directive --script we can specify the script we want. With the -sC the "default" scripts are used. In this case we only want HTTP/HTTPS banners, so we scan only the 80 and 443 ports.

Integrates user created script for automated scanning. To read more about the script we are using we can run

```shell
nmap --script-help <SCRIPT-NAME>
```

The list of script can be found at **/usr/share/nmap/scripts**. To use a custom NMAP script (.nes script) we must download it first from Github and move it in the NMAP script folder. After we run `sudo nmap --script-updatedb` and we will use the script with

```shell
nmap -sV --script "cve-script-name" <target>
```

##  LOLBAS Approach

```powershell
Test-NetConnection -Port <PORT> <HOST>
```

With some **powershell** scripting, the util **Test-NetConnection** can be used as a **NMAP** scan both for TCP and UDP.

```powershell

1..10000 | % {echo ((New-Object Net.Sockets.TcpClient).Connect("target-ip", $_)) "TCP port $_ is open"} 2>$null
```