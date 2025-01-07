# How To Read The Tcpdump Traffic Log

This reading explains how to identify the brute force attack using tcpdump. [Link of the tcpdump traffic log](https://github.com/KAmii-cxo/How-To-Read-Tcpdump-Traffic-Log/blob/main/tcpdump%20traffic%20log)

```
14:18:32.192571 IP your.machine.52444 > dns.google.domain: 35084+ A? yummyrecipesforme.com. (24)

14:18:32.204388 IP dns.google.domain > your.machine.52444: 35084 1/0/0 A 203.0.113.22 (40)
```

  - The initial part of the DNS & HTTP traffic log file shows the source computer (`your.machine.52444`) using port `52444` to send a DNS query to the DNS server (`dns.google.domain`) in order to resolve the destination URL (`yummyrecipesforme.com`).

  - The DNS server then responds to the source computer with the IP address of the destination URL (`203.0.113.22`).
---

```
14:18:36.786501 IP your.machine.36086 > yummyrecipesforme.com.http: Flags [S], seq 2873951608, win 65495, options [mss 65495,sackOK,TS val 3302576859 ecr 0,nop,wscale 7], length 0

14:18:36.786517 IP yummyrecipesforme.com.http > your.machine.36086: Flags [S.], seq 3984334959, ack 2873951609, win 65483, options [mss 65495,sackOK,TS val 3302576859 ecr 3302576859,nop,wscale 7], length 0
```


- The next section shows the source computer sending a connection request (Flags [S]) from `your.machine.36086` using port `36086` directly to the destination `yummyrecipesforme.com.http`.
  - The `.http` suffix represents the port number, with HTTP typically associated with port `80`.
- The reply indicates that the destination has acknowledged receiving the connection request (`Flags [S.]`).
- Communication between the source and the intended destination continues for approximately 2 minutes, as indicated by the timestamps between this block (14:18) and the next DNS resolution request, which occurs at 14:20.


TCP Flag codes include:
```
Flags [S]  - Connection Start 
Flags [F]  - Connection Finish 
Flags [P]  - Data Push
Flags [R]  - Connection Reset
Flags [.]  - Acknowledgment
```
---

```
14:18:36.786589 IP your.machine.36086 > yummyrecipesforme.com.http: Flags [P.], seq 1:74, ack 1, win 512, options [nop,nop,TS val 3302576859 ecr 3302576859], length 73: HTTP: GET / HTTP/1.1
```

  - The log entry with the code `HTTP: GET / HTTP/1.1` shows that the browser is requesting data from `yummyrecipesforme.com` using the HTTP GET method and HTTP protocol version 1.1. This could potentially be the request to download a malicious file.
```
14:20:32.192571 IP your.machine.52444 > dns.google.domain: 21899+ A? greatrecipesforme.com. (24)

14:20:32.204388 IP dns.google.domain > your.machine.52444: 21899 1/0/0 A 192.0.2.172 (40)

14:25:29.576493 IP your.machine.56378 > greatrecipesforme.com.http: Flags [S], seq 1020702883, win 65495, options [mss 65495,sackOK,TS val 3302989649 ecr 0,nop,wscale 7], length 0

14:25:29.576510 IP greatrecipesforme.com.http > your.machine.56378: Flags [S.], seq 1993648018, ack 1020702884, win 65483, options [mss 65495,sackOK,TS val 3302989649 ecr 3302989649,nop,wscale 7], length 0
```

  - A sudden change occurs in the logs as the traffic is routed from the source computer back to the DNS server using port 52444 (`your.machine.52444 > dns.google.domain`) for another DNS resolution request.
  - This time, the DNS server resolves the traffic to a new IP address (192.0.2.172) and its associated URL (`greatrecipesforme.com.http`).
  - The traffic is then redirected to the spoofed website, with outgoing traffic from the source computer (`IP your.machine.56378 > greatrecipesforme.com.http`) and incoming traffic to the source computer (`greatrecipesforme.com.http > IP your.machine.56378`).
  - Notably, the port number on the source computer has changed again to `.56378` after being redirected to the new website.

---

### Resources for more information 
1. [An introduction to using tcpdump at the Linux command line:](https://opensource.com/article/18/10/introduction-tcpdump) Lists several tcpdump commands with example output. The article describes the data in the output and explains why it is useful.
2. [tcpdump Cheat Sheet:](https://www.comparitech.com/net-admin/tcpdump-cheat-sheet/) Lists tcpdump commands, packet capturing options, output options, protocol codes, and filter options
3. [What is a computer port? | Ports in networking:](https://www.cloudflare.com/learning/network-layer/what-is-a-computer-port/) Provides a short list of the most common ports for network traffic and their associated protocols. The article also provides information about ports in general and using firewalls to block ports.
4. [Service Name and Transport Protocol Port Number Registry:](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml) Provides a database of port numbers with their service names, transport protocols, and descriptions
5. [How to Capture and Analyze Network Traffic with tcpdump?:](https://geekflare.com/tcpdump-examples/) Provides several tcpdump commands with example output. Then, the article describes each data element in examples of tcpdump output. 
6. [Masterclass – Tcpdump – Interpreting Output:](https://packetpushers.net/masterclass-tcpdump-interpreting-output/) Provides a color-coded reference guide to tcpdump output 



