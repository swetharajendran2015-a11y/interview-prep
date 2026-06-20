# DDoS Fundamentals — Interview Q&A

This file covers the most commonly asked DDoS questions in NOC Engineer and Application Security interviews. Each answer is written the way you would explain it in an interview — clear, practical, not just a textbook definition.

---

## 1. What is a DDoS attack?

A DDoS (Distributed Denial of Service) attack is when multiple systems flood a target — a server, network, or service — with traffic to make it unavailable to legitimate users.

The key word is **distributed** — the attack comes from many sources at the same time, which makes it harder to block than a regular DoS attack coming from a single IP.

The goal is not to break into the system — it is to make it unreachable.

---

## 2. What is the difference between DoS and DDoS?

- **DoS** — attack comes from a single source. Easier to mitigate — just block that one IP.
- **DDoS** — attack comes from hundreds or thousands of sources simultaneously. Much harder to block because you cannot just block one IP without affecting legitimate traffic too.

DDoS attacks typically use botnets — large networks of compromised devices sending traffic at the same time.

---

## 3. What are the three categories of DDoS attacks?

**Volumetric attacks**
- Goal: saturate the bandwidth pipe
- Measured in Gbps or Tbps
- Examples: UDP flood, ICMP flood, DNS amplification
- They do not need to reach the server — they just need to fill the pipe

**Protocol attacks**
- Goal: exhaust network device resources like firewalls, load balancers, or routers
- Measured in packets per second (pps)
- Examples: SYN flood, ACK flood, fragmented packet attacks
- They exploit how protocols work at L3/L4

**Application layer attacks**
- Goal: exhaust server resources by sending seemingly legitimate requests
- Measured in requests per second (rps)
- Examples: HTTP flood, Slowloris, DNS query flood
- These are harder to detect because individual requests look normal

---

## 4. Which OSI layers do DDoS attacks target?

- **Layer 3 (Network)** — ICMP flood, IP fragmentation attacks
- **Layer 4 (Transport)** — SYN flood, UDP flood, ACK flood
- **Layer 7 (Application)** — HTTP flood, Slowloris, DNS amplification

Most volumetric attacks target L3/L4. Application layer attacks target L7 and are harder to detect because they look like normal user traffic.

---

## 5. What is a SYN flood and how does it work?

A SYN flood exploits the TCP three-way handshake.

Normal TCP handshake:
1. Client sends SYN
2. Server replies with SYN-ACK
3. Client sends ACK — connection established

In a SYN flood:
- Attacker sends thousands of SYN packets with spoofed source IPs
- Server sends SYN-ACK back — but gets no ACK because the source IP is fake
- Server keeps half-open connections in memory waiting for the ACK that never comes
- Server runs out of connection table space — legitimate users cannot connect

**Mitigation:** SYN cookies, rate limiting SYN packets, upstream filtering

---

## 6. What is a UDP flood?

UDP is connectionless — there is no handshake. An attacker sends massive volumes of UDP packets to random ports on the target.

The target server:
- Receives the UDP packet
- Checks if any application is listening on that port
- Finds nothing — sends back an ICMP "destination unreachable" message
- Repeats this for every packet — exhausting resources

At high enough volumes the bandwidth pipe gets saturated before the server even processes the packets.

**Mitigation:** Null routing, upstream rate limiting, UDP traffic filtering

---

## 7. What is an ICMP flood?

Also called a ping flood. Attacker sends a massive number of ICMP Echo Request (ping) packets to the target.

The target has to respond to each one with an ICMP Echo Reply — consuming both inbound and outbound bandwidth.

At scale this saturates the network pipe and makes the target unreachable.

**Mitigation:** Rate limit ICMP traffic at the network edge, block ICMP from untrusted sources

---

## 8. What is an HTTP flood?

An L7 application layer attack. The attacker sends a high volume of HTTP GET or POST requests to the target web server.

Unlike volumetric attacks — each request looks like normal user traffic. The server has to process every request — eventually running out of CPU, memory, or database connections.

**Why it is harder to detect:**
- Traffic volume may not be unusually high
- Requests look legitimate at L3/L4
- Requires L7 inspection to identify the attack pattern

**Mitigation:** Cloud WAF rate limiting, bot detection, CAPTCHA challenges, IP reputation filtering

---

## 9. What is a botnet?

A botnet is a network of compromised devices (computers, routers, IoT devices) that an attacker controls remotely.

The attacker uses the botnet to launch DDoS attacks — each device sends traffic to the target simultaneously. Because the traffic comes from thousands of real IP addresses around the world, it is hard to block without also blocking legitimate users.

Famous example: Mirai botnet — compromised IoT devices like cameras and routers to launch massive DDoS attacks.

---

## 10. What is null routing (BGP blackhole)?

Null routing means telling the network to drop all traffic destined for a specific IP address before it reaches the target.

In a DDoS context:
- The targeted IP is advertised as a blackhole route via BGP
- Upstream routers drop all traffic to that IP at the provider edge
- The attack traffic never reaches the customer network
- Side effect: legitimate traffic to that IP is also dropped

It is a blunt but fast mitigation — useful when the attack volume exceeds the customer's pipe capacity and the targeted IP can be temporarily taken offline.

---

## 11. What is the difference between scrubbing and null routing?

**Null routing:**
- All traffic to the targeted IP is dropped
- Fast and simple
- Legitimate traffic is also blocked — service goes down
- Used when attack volume is too high to scrub

**Scrubbing:**
- Traffic is redirected to a scrubbing centre
- Clean traffic is forwarded to the customer — attack traffic is dropped
- Service stays up during the attack
- Requires scrubbing infrastructure and takes slightly longer to activate
- Better option for customers who need high availability during an attack

---

## 12. What tools did you use in the NOC to detect and monitor DDoS attacks?

- **Kentik** — flow analysis and DDoS detection. Used to identify attack sources, protocol breakdown, and traffic volumes at a flow level
- **Grafana** — traffic visualization. Used to see spikes and patterns on dashboards in real time
- **Pingdom** — availability monitoring. Used to confirm whether the customer service was impacted and when it recovered
- **Jira** — incident ticketing. Every DDoS incident was logged and tracked here
- **Confluence** — documentation. Post-incident reports and runbooks were maintained here

---

> ⚠️ All content is based on publicly available knowledge and personal study. Not affiliated with any company or certification body.

---

## 🤝 Connect

- 📧 swetharajendran4055@gmail.com
- 💼 LinkedIn — https://www.linkedin.com/in/swetha-rajendran-4349a12a1
- 🐙 GitHub — https://github.com/swetharajendran2015-a11y
- 🎥 YouTube — https://www.youtube.com/@alexessecurity
