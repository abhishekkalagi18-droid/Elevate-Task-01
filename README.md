# Elevate Task 01 — Local Network Port Scanning

## 1. Problem Statement

Network services running on devices can expose systems to security risks when they are unnecessary, outdated, or improperly configured. This task focuses on scanning an authorized local network to identify active devices, open ports, and the services running on those ports.

## 2. Objective

The objectives of this task are:

* Discover active devices on the local network.
* Identify open TCP ports.
* Detect services and their versions.
* Understand network service exposure.
* Analyze the security risks associated with exposed services.
* Capture and analyze Nmap traffic using Wireshark.

## 3. Tools Used

* Kali Linux
* Nmap 7.99
* Wireshark
* VMware
* Metasploitable 2

## 4. Lab Environment

The scanning was performed inside an authorized VMware cybersecurity laboratory.

**Network Range:**

```text
192.168.181.0/24
```

**Primary Target:**

```text
192.168.181.134
```

The target is a Metasploitable 2 virtual machine intentionally designed for security testing and training.

## 5. Nmap Scanning

The following command was used to scan the local network:

```bash
sudo nmap -sS -sV 192.168.181.0/24 -oN scan_results.txt
```

Where:

* `-sS` performs a TCP SYN scan.
* `-sV` detects service versions.
* `-oN` saves the results in normal text format.
* `192.168.181.0/24` represents the local lab network.

The scan identified **4 active hosts**.

## 6. Target Service Discovery

A detailed scan was performed against the Metasploitable machine:

```bash
nmap -sS -sV 192.168.181.134 -oX scan_results.xml
```

Important services identified included:

| Port | Service    | Detected Version       |
| ---: | ---------- | ---------------------- |
|   21 | FTP        | vsftpd 2.3.4           |
|   22 | SSH        | OpenSSH 4.7p1          |
|   23 | Telnet     | Linux telnetd          |
|   25 | SMTP       | Postfix                |
|   53 | DNS        | ISC BIND 9.4.2         |
|   80 | HTTP       | Apache 2.2.8           |
|  139 | NetBIOS    | Samba                  |
|  445 | SMB        | Samba                  |
|  111 | RPC        | rpcbind                |
| 1524 | Bindshell  | Metasploitable service |
| 2049 | NFS        | NFS                    |
| 2121 | FTP        | ProFTPD 1.3.1          |
| 3306 | MySQL      | MySQL 5.0.51a          |
| 5432 | PostgreSQL | PostgreSQL 8.3.x       |
| 5900 | VNC        | VNC 3.3                |
| 6667 | IRC        | UnrealIRCd             |
| 8009 | AJP13      | Apache Jserv           |
| 8180 | HTTP       | Apache Tomcat          |

Additional services were detected and are available in `scan_results.txt` and `scan_results.xml`.

## 7. Wireshark Packet Analysis

Wireshark was used to observe the network traffic generated during an Nmap TCP SYN scan.

A smaller scan was performed for packet analysis:

```bash
sudo nmap -sS -p 21,22,23,80 192.168.181.134
```

The captured packets were analyzed using the Wireshark filter:

```text
ip.addr == 192.168.181.134 && tcp
```

### TCP SYN Scan Behavior

For an open TCP port, the typical communication is:

```text
Kali → Target       SYN
Target → Kali       SYN, ACK
```

For a closed TCP port:

```text
Kali → Target       SYN
Target → Kali       RST, ACK
```

This demonstrates how Nmap can determine the state of TCP ports without completing a normal TCP connection.

The Wireshark capture is saved as:

```text
wireshark.png
```

## 8. Security Observations

The scan demonstrated that a single system can expose many network services.

Potential security concerns include:

* FTP may expose file-transfer services without modern encryption.
* Telnet provides remote access without modern encrypted communication.
* SMB/NetBIOS can expose file-sharing functionality.
* Database services such as MySQL and PostgreSQL should not normally be unnecessarily exposed to untrusted networks.
* VNC provides remote graphical access and requires strong authentication and network restrictions.
* Older software versions may contain known security weaknesses.
* Unnecessary services increase the system's attack surface.

An open port itself does not automatically mean that the service is vulnerable. The actual risk depends on the service, configuration, authentication, software version, network exposure, and security controls.

## 9. Security Recommendations

The following measures can reduce network exposure:

1. Disable unnecessary network services.
2. Restrict sensitive services using firewall rules.
3. Replace insecure protocols such as Telnet with secure alternatives.
4. Keep operating systems and network services updated.
5. Restrict database services to authorized systems.
6. Use strong authentication.
7. Apply network segmentation where appropriate.
8. Regularly scan systems to identify unexpected exposed services.
9. Monitor network traffic for suspicious activity.

## 10. Evidence

The following files are included in this repository:

```text
scan_results.txt
scan_results.xml
wireshark.png
screenshots/
```

`scan_results.txt` contains the readable Nmap output.

`scan_results.xml` contains structured Nmap scan information.

`wireshark.png` contains the Wireshark packet capture from the TCP SYN scan.

## 11. Outcome

This task provided practical experience in network reconnaissance and service enumeration. The exercise demonstrated how Nmap can identify active hosts, open TCP ports, and service versions, while Wireshark provided packet-level visibility into the TCP SYN scanning process.

## 12. Ethical Scope

All scanning and packet capture activities were performed within an authorized local VMware cybersecurity laboratory using intentionally vulnerable training systems. No unauthorized external systems were scanned.

## 13. Conclusion

The exercise demonstrated the importance of understanding network exposure. Identifying open ports and running services is an essential first step in security assessment because it helps administrators determine which services need to be secured, restricted, updated, or disabled.
