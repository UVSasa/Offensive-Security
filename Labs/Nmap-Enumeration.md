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
**Step 1**: Set Up the Lab

1. Machines Needed:
    * Attacker: Kali Linux (your scanning box with Nmap).
    * Victims: Windows VM, Ubuntu VM, maybe a small vulnerable machine (like Metasploitable2).
    * Network: Put all VMs in the same subnet (bridged or host-only adapter).
👉 Real World Parallel: In a corporate LAN, attackers must first identify which machines are alive before choosing targets.

-------------------------------------------------------------------------------------------------------------------------------------------
**Step 2: Live Host Discovery**

1). ARP Scan (local subnet only)
Our first step is to figure out how attackers figure out which IPs are “alive.” For this we will use an arp scan.

Command:
- sudo nmap -PR -sn 192.168.1.0/24

Explanation:
- Fastest way to find local devices since ARP can’t be blocked by firewalls.
- In the real-world this is the same as plugging into a company LAN and seeing who’s around.
- -sn option decalres you want to scan without conducting a port scan
- -PR option indicates you only want an arp scan

2). ICMP Echo (Ping Scan)

Command: 
- sudo nmap -PE -sn 192.168.1.0/24

Explanation
- Why: Useful outside local subnet, but may be blocked by firewalls.
- Real-world: Similar to a ping sweep attackers use on external networks.





















