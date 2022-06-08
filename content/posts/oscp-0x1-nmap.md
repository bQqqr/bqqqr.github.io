+++
author = "Christos Kaltsas"
title = "OSCP 0x1 - Nmap"
date = "2022-06-08"
description = "An attempt to deconstruct nmap's complex usage."
tags = [
    "cheatsheet",
    "nmap",
    "ports-scanning",
    "oscp"
]
+++


# 🕵️ Nmap
Nmap was designed to rapidly scan large networks, but works fine against single hosts.

{{< notice warning >}}
Invoking network scan techniques on systems, hosts or network ranges other than those under the own responsibility is illegal in quite a few jurisdictions. 
{{< /notice >}}

## 🎯 Target Specification

Firstly, you must choose your targets. 

- Scan a single ipv4 address.    
`nmap 192.168.1.1`

- Scan multiple ipv4 addresses.  
`nmap 192.168.1.1 192.168.1.2`

- Scan a range of ipv4 addresses.  
`nmap 192.168.1.1-254`

- Scan a domain name.  
`nmap scanme.nmap.org`

- Scan using the CIDR notation.  
`nmap 192.168.1.0/24`

- Scan using an input file.  
`nmap -iL targets.txt`

## 🔍 Scan Techniques

Secondly, you must select how nmap scans the targets.

{{< notice tip >}}
Always scan with SYN and UDP unless you have not root access.
{{< /notice >}}

- Scan using SYN technique.
`sudo nmap 192.168.1.1 -sS`

- Scan using TCP techique.
`sudo nmap 192.168.1.1 -sT`  

- Scan using UDP technique. 
`sudo nmap 192.168.1.1 -sU`

## 🚪 Port Specificaton

Now, you must specify the ports, that nmap will scan.

- Scan a single port.  
`nmap 192.168.1.1 -p 80`

- Scan a range of ports.  
`nmap 192.168.1.1 -p 1-1000`

- Scan all ports.  
`nmap 192.168.1.1 -p-`

- Scan the top used ports.  
`nmap 192.168.1.1 --top-ports 100`

- Scan multiple UDP and TCP ports.  
`nmap 192.168.1.1 -p U:53, T:21-25,80`

## ⚙️ Services and Version Detection

Afterwards, you must specify how nmap detects a service's version.

- Let nmap determine the version.  
`nmap 192.168.1.1 -sV`

- Enable intensity level 9. Most likely correct. Slow.  
`nmap 192.168.1.1 -sV --version-all`


## 🖥️ OS Detection

You might also want to detect the target's Operating System.

- Remote OS detection using TCP/IP stack fingerprinting.  Let nmap guess aggresively.  
`nmap 192.168.1.1 -O --osscan-guess`

- Enable OS detection, version detection, script scanning and traceroute.  
`nmap 192.168.1.1 -A`


## ⏱️ Timing and Perfomance

Depending on your network bandwidth, you may optimize nmap accordingly. 

- `nmap 192.168.1.1 -T0` *Paranoid*
- `nmap 192.168.1.1 -T1` *Sneaky*
- `nmap 192.168.1.1 -T2` *Polite*
- `nmap 192.168.1.1 -T3` *Normal*
- `nmap 192.168.1.1 -T4` *Aggressive*
- `nmap 192.168.1.1 -T5` *Insane*

## 🤖 NSE Scripts

Automate a variety of networking tasks using NSE lua scripts.

- Scan with default NSE scripts. Considered useful for discovery and safe.  
`nmap 192.168.1.1 -sC`  
`nmap 192.168.1.1 --script default`

- Scan with a specific script.  
`nmap 192.168.1.1 --script banner`

- Scan with multiple scripts using a wildcard.  
`nmap 192.168.1.1 --script=http*`

NSE scripts define a list of categories they belong to:
- `auth`, which use brute force attacks to determine credentials.
- `broadcast` which do discovery of hosts not listed on the cmd by broadcasting on the local network.
- `brute` which use brute force attacks to guess authentication credentials of a remote server.
- `default` which are useful, fast verbosed, reliable, instrusive and private.
- `discovery` which try to actively discover more about the network by querying public registries.
- `dos` which may cause a denial of service.
- `exploit` which aim to actively exploit some vulnerability.
- `external` which may send data to a third-party database or other network resource.
- `fuzzer` which are designed to send server software unexpected or randomized fields.
-  `intrusive` which are basically unsafe.
-  `malware` which test whether the target platform is infected by malware or backdoors.  
- `safe` which are safe :).
-  `version` which are used for version detection.
- `vuln` which check for specific known vulnerabilities.

## 🗒️ Output

It's is really important to select carefully the nmap's output options.

- Save normal output to a file.  
`nmap 192.168.1.1 -oN normal.txt`

- Save xml output to a file.  
`nmap 192.168.1.1 -oX xml.txt`

- Save grepable output to a file.  
`nmap 192.168.1.1 -oG grep.txt`

- Save all output formats at once.  
`nmap 192.168.1.1 -oA nmap_results`


## 💡 Useful Combinations

- Using nmap to perform a combined UDP and SYN scan to a known target.  
`sudo nmap -Pn -sS -sU 192.168.1.1`


- Using nmap to perform a network sweep and enumerate live hosts.  
`nmap -sn 192.168.1.1-255 -oG grepable.txt`  
`cat grepable.txt | cut -d' ' -f2 | grep 192 > live_hosts.txt`
