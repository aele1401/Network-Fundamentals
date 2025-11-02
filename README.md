# Network Administration - Vulnerabilites & Mitigation

### Pinging Hosts
- `sudo apt install fping` to install fping if not already installed.
- Run the script below to ping the hosts and determine which hosts are reachable. Also, inlcude the OSI layer for this operation.
```
#!/bin/bash
ips=(
    `15.199.95.91/28`
    `15.199.94.91/28`
    `11.199.158.91/28`
    `167.172.144.11/32`
    `11.199.141.91/28`
)

for ip in ${ips[@]};
do
    fping -g $ip
done
```
![Diagram](https://github.com/aele1401/Network-Fundamentals/blob/main/Images/fping.png)

- OSI layer is layer 3 the network layer which handles the routing, logical adressing, packet forwarding, fragmentation and reassembly, and error handling. Common protocols include IP (IPv4 & 6), ICMP (which we used fping in this case), and IGMP. Devices that operate at this layer are routers and layer 3 network switches.

### Nmap
- Run an nmap scan with: `sudo nmap -sS 167.172.144.11`
![Diagram](https://github.com/aele1401/Network-Fundamentals/blob/main/Images/nmap.png)

- OSI layer is 2 the data link layer that's responsible for node-to-node data transfer and error detection and correction in the physical transmission medium. Protocols include ethernet, PPP, Wi-Fi, and HDLC. Devices operating at this layer are bridges and switches.
- Open port is port 22 which should be closed if not in use and if in use to secure it by enabling public and private key encryption.

### NSLOOKUP
- Use nslookup tool to exmaine the following domain.
    * `nslookup rollingstone.com` or `nslookup 151.101.128.69`
    ![Diagram](https://github.com/aele1401/Network-Fundamentals/blob/main/Images/nslookup1.png)
    ![Diagram](https://github.com/aele1401/Network-Fundamentals/blob/main/Images/nslookup2.png)
- Layer 5, session layer for  establishing, managing, and terminating sessions between two communicating devices or applications. It provides the mechanisms to control the dialogues (connections) between computers. Protocols include RPC, SQL PPTP, and NetBIOS. Devices include application and database servers.
- Based on the information in nslookup, IOC of DNS spoofing that can redirect web traffic.
- For mitigating this, use TLS encryption to mitigate domain compromise.

### Identifying the Hacker
![Diagram](https://github.com/aele1401/Network-Fundamentals/blob/main/Images/hacker1.png)
![Diagram](https://github.com/aele1401/Network-Fundamentals/blob/main/Images/hacker2.png)
- Layer 3 - network layer attack.
- Hacker located on POST /formservice/
- Hacker has the MAC address **00:0C:29:1D:B3:B1**
- IOC of ARP poisoning, traffic redirected to malicious device instead of correct destination
- Vulnerabilites:
    * Rockstar Corp. configured machines not to accept any connections and machine `167.172.144.11` accepted an ICMP echo request which is a security misconfiguration.
    * After SYN scan was conducted results showed port 22 was open without encryption enabled which is vulnerable to SSH attacks.
    * Utilizing nslookup tool domain `rollingstone.com` returned with multiple IP addresses which is indicative of DNS spoofing with traffic redirection which can lead to pharming and credential harvesting attacks.
    * There's confirmation of a hacker somewhere on the network with the above MAC redirecting traffic.
- Mitigations:
    * Restrict ICMP echo requests and properly configure settings.
    * Close port 22 if not in use or enable public and private key encryption. Also, disable any root privileges.
    * Utilize a static ARP in server to prevent spoofing.
    * Utilize IPS/IDS tools that scan send alerts and block suspicious/malicious activities like this.
    * Utilize DAI with rate limiting which filters ARP messages through a switch.
