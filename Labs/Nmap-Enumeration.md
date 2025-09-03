**Starting Point**
For this lab I want to get practice getting familar with Nmap in order to effectively enumerate hosts depending on any specific needs.

For documentation purposes before starting we will make a few assumptions since I will be on a local network lab environment:
  1. You already have access to a network segment (like plugging into a company LAN or compromising a foothold box).
  2. You know the subnet, but not which hosts are alive or what they’re running.

My goal is to practice:
  1. Discovering live hosts.
  2. Enumerating services.
  3. Gathering deeper intel like OS, versions, and firewall rules.
  4. Save results for later analysis.

-------------------------------------------------------------------------------------------------------------------------------------------
<h2>Step 1: Set Up the Lab</h2>

1. Machines Needed:
    * Attacker: Kali Linux (your scanning box with Nmap).
    * Victims: Windows VM, Ubuntu VM, maybe a small vulnerable machine (like Metasploitable2).
    * Network: Put all VMs in the same subnet (bridged or host-only adapter).
👉 Real World Parallel: In a corporate LAN, attackers must first identify which machines are alive before choosing targets.

-------------------------------------------------------------------------------------------------------------------------------------------
<h2>Step 2: Live Host Discovery</h2>

Notice all the scans below will be done using the -sn option. This is done to avoid scanning ports and making unneccsary "noise" before we know which hosts are "alive"


<h3>1. ARP Scan (local subnet only)</h3>

  Our first step is to figure out how attackers figure out which IPs are “alive.” For this we will use an arp scan.

Command:
  - sudo nmap -PR -sn 192.168.1.0/24

Explanation:
  - Fastest way to find local devices since ARP can’t be blocked by firewalls.
  - In the real-world this is the same as plugging into a company LAN and seeing who’s around.
  - -sn option decalres you want to scan without conducting a port scan
  - -PR option indicates you only want an arp scan


![Alt text](/Images/ArpScan.png)



-------------------------------------------------------------------------------------------------------------------------------------------

<h3>2. ICMP Echo (Ping Scan)</h3>

Command: 
  - sudo nmap -PE -sn 192.168.1.0/24

Explanation
  - Useful outside local subnet, but may be blocked by firewalls.
  - In the real-world this is similar to a ping sweep attackers use on external networks.
  - Because ICMP echo(Ping)tends to be blocked you can consider ICMP timestamp or ICMP address mask to tell if a system is online.
-------------------------------------------------------------------------------------------------------------------------------------------

<h3>3. ICMP Timestamp or Address Mask (firewall evasion)</h3>

Commands:
  - command sudo nmap -PP -sn 192.168.1.0/24 (timestamp)
  - command sudo nmap -PM -sn 192.168.1.0/24 (address mask)

Explanation:
  -  Sometimes these work when regular pings ICMP(echo) are blocked.
-------------------------------------------------------------------------------------------------------------------------------------------

<h3>4. TCP SYN Ping</h3>

For a tcp syn ping, we can send a packet with the syn flag set to a specific port and wait for a response. An open port should reply with a syn/ack, and a closed port would result in a rst. Privileged users can send TCP SYN packets and don’t need to complete the TCP 3-way handshake even if the port is open. Unprivileged user must complete the handshake

command: 
  - sudo nmap -PS22,80,443 -sn 192.168.1.0/24

Explanation:
  - Checks if hosts respond on common ports.
  - In the real-world attackers may use this to try and ping specific ports if ICMP is blocked.
-------------------------------------------------------------------------------------------------------------------------------------------

<h3>5. UDP Ping</h3>

Sending a UDP packet to an open port is not expected to lead to any reply. However if we send a UDP packet to a closed UDP port, we expect to get an ICMP port unreachable packet; this indicates that the system is up and available.

Command:
  - sudo nmap -PU53,161 -sn 192.168.1.0/24

Explanation:
  - Useful to find hosts running DNS (53) or SNMP (161).

-------------------------------------------------------------------------------------------------------------------------------------------

<h2>Step 3: Basic Port Enumeration</h2>

Once I identified which IPs were "alive," I focused on targeting them. In this case, I chose my Windows VM as the "victim machine" and used its IP address. Even though I already knew the IP beforehand, it was still valuable to run the scans and see all the devices on my network. This simulated how an attacker would enumerate the environment, identify active devices, and determine the best target—just as I did with my Windows VM.

-------------------------------------------------------------------------------------------------------------------------------------------

<h3>1. TCP Connect Scan</h3>

Instead of crafting raw packets (like -sS SYN scan), Nmap asks the OS to perform a full TCP three-way handshake with each target port.

Command:
  - nmap -sT 192.168.1.10

Explanation:
  - Simple scan that works even if you don’t have root.
  - In real-world environments this scan is very noisy and easy for defenders to detect in logs.


In the image below you'll see the Syn, Syn,Ack, and Ack packets completing the tcp handshake for port 22.

![Alt text](/Images/ConnectScan.png)


-------------------------------------------------------------------------------------------------------------------------------------------

<h3>2. TCP SYN Scan (stealth)</h3>

It scans the default top 1000 TCP ports on 192.168.1.10 using a stealthy half-open technique to see which ports are open, closed, or filtered.

Command:
  - sudo nmap -sS 192.168.1.10

Explanation: 
  - Default for privileged users. Doesn’t complete the 3-way handshake.
  - This scan is usually harder to detect in logs, widely used by attackers.


![Alt text](/Images/SyScan.png)

    
-------------------------------------------------------------------------------------------------------------------------------------------
<h3>3. UDP Scan</h3>

Command: 
  - sudo nmap -sU 192.168.1.10

Explanation:
  - Services like DNS, SNMP, and DHCP run on UDP.
  - Real-world: Painfully slow, but necessary.
-------------------------------------------------------------------------------------------------------------------------------------------  

<h3>4. Full Port Sweep</h3>

Command:
  - sudo nmap -sS -p- 192.168.1.10

Explanation:
  - Covers all 65,535 ports.
  - This scan is takes really long and is meticulous, but if an attacker is patient, they’ll find obscure open services.
-------------------------------------------------------------------------------------------------------------------------------------------  

<h2>Step 4: Advanced Port Scans (Firewall Testing)</h2>

<h3>1. ACK Scan</h3>

Command 
  - sudo nmap -sA 192.168.1.10

Explanation:
  - It’s not used to find open ports, but instead to check firewall rules. If the port is unfiltered, the target will usually respond with a RST (reset) packet. If the port is filtered, there will be no response or an ICMP unreachable error.
  - Helps determine whether a firewall is present and filtering traffic. It’s often used after discovering ports to map out firewall rules and packet filtering behavior.
  - sudo is needed because raw packet scanning often requires elevated privileges.

------------------------------------------------------------------------------------------------------------------------------------------- 

<h3>2. Null, FIN, Xmas Scans</h3>

Command: 
  - sudo nmap -sN 192.168.1.10 (null)
  - sudo nmap -sF 192.168.1.10(Fin)
  - sudo nmap -sX 192.168.1.10(Xmas)

Explanation:
  - No response → port is open
  - RST (reset) → port is closed
  - Useful on older TCP/IP stacks that behave differently.
  - Real-world: Sometimes bypasses firewalls/IDS.

------------------------------------------------------------------------------------------------------------------------------------------- 
<h3>3. Window Scan</h3>
  
Command:
  - sudo nmap -sW 192.168.1.10

Explanation:
  - Rare, but can fingerprint OS behavior.

------------------------------------------------------------------------------------------------------------------------------------------- 
<h2>Step 5: Service and OS Detection</h2>

<h3>1. Service Detection</h3>  

Command 
  - sudo nmap -sV 192.168.1.10

Explanation:
  - Identifies software version.
  - Helps attackers search for known vulnerabilities.


<h3>2. OS Detection</h3>

command 
  - sudo nmap -O 192.168.1.10

Explanation:
  - Determines if it’s Linux, Windows, etc.
  - Attackers tune exploits to OS, therefore enumerating the OS can lead to knowldge about potential vulnerabilities.

























