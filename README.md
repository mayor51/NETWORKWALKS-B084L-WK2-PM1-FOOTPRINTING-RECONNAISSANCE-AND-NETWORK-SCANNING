#   NETWORKWALKS-B084L-WK2-PM1-FOOTPRINTING-RECONNAISSANCE-AND-NETWORK-SCANNING

# FOOTPRINTING, RECONNAISSANCE AND NETWORK SCANNING

**Internship Programme:** Networkwalks Cybersecurity Internship  
**Internship Code:** B084L  
**Week:** 02  
**Session:** PM1  
**Operating System:** Kali Linux  
**Additional Tools:** Windows Command Prompt, Nmap and Zenmap

---

# INTRODUCTION

This practical focused on gathering information about a website and examining devices connected to a local network. I used Kali Linux to investigate the domain `networkwalks.com` and its publicly observable web infrastructure. I also used Nmap and Zenmap to identify active devices within a local network.

The first phase involved domain footprinting, DNS enumeration, HTTP header inspection, website technology identification and Web Application Firewall detection. The second phase involved identifying my local network configuration and scanning its IP address range to discover active hosts.

The exercises provided practical experience with reconnaissance tools and helped me understand how information about web infrastructure and network devices can be collected and interpreted during a security assessment.

# PART 1: FOOTPRINTING AND RECONNAISSANCE

## 1. WHOIS — Domain Registration Information

I started by retrieving the registration information associated with `networkwalks.com` using WHOIS.

**Command:**

```bash
whois networkwalks.com
```

### Results

The lookup returned the following information:

| Information | Result |
|---|---|
| Domain | networkwalks.com |
| Registrar | GoDaddy.com, LLC |
| Creation Date | 6 November 2019 |
| Updated Date | 12 November 2025 |
| Expiry Date | 6 November 2027 |
| Name Server 1 | ns6135.hostgator.com |
| Name Server 2 | ns6136.hostgator.com |

The domain registration record also displayed several domain status codes, including `clientTransferProhibited` and `clientUpdateProhibited`.

### Analysis

The WHOIS results identified GoDaddy as the registrar and HostGator as the provider associated with the domain's name servers. The registration dates and domain status codes provided additional information about the domain's registration and management.



![whois](screenshots/whois.png)
![whois2](screenshots/whois2.png)

---

## 2. NSLOOKUP — DNS Resolution

I used NSLookup to determine the IP address associated with the domain and identify the DNS resolver responding to the request.

**Command:**

```bash
nslookup networkwalks.com
```

### Results

| Information | Result |
|---|---|
| DNS Server | 8.8.8.8 |
| DNS Port | 53 |
| Domain | networkwalks.com |
| Resolved IPv4 | 192.232.216.135 |

The response was identified as non-authoritative, meaning the answer was returned by a DNS resolver rather than directly by the domain's authoritative name server.

### Analysis

The lookup successfully resolved `networkwalks.com` to `192.232.216.135`. This IP address was subsequently useful when examining the website's HTTP responses and associated technologies.

![nslookup](screenshots/nslookup.png)

---

## 3. cURL — HTTP Response Header Inspection

I used cURL to inspect the HTTP response headers returned by the website.

**Command:**

```bash
curl -I https://networkwalks.com
```

### Results

The request returned an `HTTP/2 200` response, indicating that the server successfully processed the request.

Other information observed included:

- Web server: Apache
- Content type: `text/html; charset=UTF-8`
- WordPress-related API endpoint information.
- Cookie security attributes: Secure and HttpOnly.
- Referrer policy: `no-referrer-when-downgrade`.
- Permissions policy header.

### Analysis

The response headers provided information about the website's server configuration and how it communicates with clients. The Secure and HttpOnly cookie attributes were also visible in the response.

This inspection helped me understand how HTTP headers can reveal useful details about a web application's configuration without requiring access to its backend.

![curl](screenshots/curl.png)
---

## 4. DNSRECON — DNS Enumeration

I used DNSRecon to collect additional DNS records associated with the domain.

**Command:**

```bash
dnsrecon -d networkwalks.com
```

### Results

The enumeration returned eight records and identified several DNS record types.

**A Record**

```text
networkwalks.com
192.232.216.135
```

**NS Records**

```text
ns6135.hostgator.com
ns6136.hostgator.com
```

**MX Record**

```text
mail.networkwalks.com
192.232.216.135
```

**SOA Record**

The tool identified the domain's Start of Authority record.

**TXT Records**

The output included a Google site verification record and an SPF policy:

```text
v=spf1 +a +mx +ip4:50.87.144.87
+include:websitewelcome.com ~all
```

**SRV Records**

The enumeration also identified service records associated with cPanel email autodiscovery.

The DNSSEC query did not return an answer.

### Analysis

DNSRecon provided more detailed information than the initial NSLookup query. It revealed the domain's name servers, mail configuration, TXT records and service records.

The findings showed how DNS enumeration can expose information about the different services associated with a domain.

![dnsrecon](screenshots/dnsrecon.png)

---

## 5. WAFW00F — Web Application Firewall Detection

I used WAFW00F to determine whether a Web Application Firewall was detectable on the website.

**Command:**

```bash
wafw00f networkwalks.com
```

### Results

The tool returned:

```text
The site https://networkwalks.com
is behind ModSecurity (SpiderLabs) WAF.
```

The detection process completed after two requests.

### Analysis

The result identified ModSecurity (SpiderLabs) as the detected Web Application Firewall.

ModSecurity is used to inspect HTTP traffic and apply security rules against potentially malicious requests.

Identifying the WAF provided information about one of the security technologies protecting the website's web traffic.

![wafwoof](screenshots/wafw00f.png)

---

## 6. WHATWEB — Website Technology Fingerprinting

I used WhatWeb to identify technologies and software associated with the website.

**Command:**

```bash
whatweb networkwalks.com
```

### Results

The scan identified the following technologies:

| Technology | Detected Information |
|---|---|
| Web Server | Apache |
| CMS | WordPress |
| WordPress | 7.1.4 |
| Bootstrap | 7.1.4 |
| jQuery | 3.7.1 |
| JavaScript | HTML5 and JavaScript |
| Other Components | Google Tag Manager, WordPress Download Manager |

The tool also identified an HTTP redirect to the HTTPS version of the website.

### Analysis

The results showed that the website uses WordPress and several supporting web technologies.

This information is relevant to security assessments because identifying the software components of a website helps determine which technologies may require further security review.

However, technology identification alone does not confirm the existence of a vulnerability.

![whatweb](screenshots/whatweb.png)

---

## 7. PART 1 FINDINGS SUMMARY

The footprinting and reconnaissance activities produced the following findings:

| Tool | Purpose | Main Finding |
|---|---|---|
| WHOIS | Registration lookup | GoDaddy registrar |
| NSLookup | DNS resolution | 192.232.216.135 |
| cURL | HTTP inspection | HTTP/2 200, Apache |
| DNSRecon | DNS enumeration | Eight records found |
| WAFW00F | WAF detection | ModSecurity |
| WhatWeb | Technology identification | WordPress, Apache, jQuery |

The combined results provided information about the domain's registration, DNS infrastructure, web technologies, HTTP configuration and detected security controls.

# PART 2: NETWORK SCANNING USING NMAP AND ZENMAP

## 1. Identifying the Local Network Configuration

Before scanning the network, I used the Windows Command Prompt to identify the IP address and subnet configuration of my computer.

**Command:**

```cmd
ipconfig
```

### Results

The Wireless LAN adapter displayed the following configuration:

| Network Information | Value |
|---|---|
| IPv4 Address | 172.20.10.10 |
| Subnet Mask | 255.255.255.240 |
| Default Gateway | 172.20.10.1 |
| Network Address | 172.20.10.0/28 |

The subnet mask `255.255.255.240` corresponds to a `/28` network containing 16 total IP addresses, with 14 usable host addresses.


![ipconfig](screenshots/ipconfig.png)
---

## 2. Configuring the Nmap Scan in Zenmap

After identifying the network range, I launched Zenmap to perform host discovery.

I entered the target network:

```text
172.20.10.0/28
```

I selected the **Ping Scan** profile.

The command generated by Zenmap was:

```bash
nmap -sn 172.20.10.0/28
```

The `-sn` option performs host discovery without conducting a port scan.


---

## 3. Discovering Active Hosts

After executing the scan, Nmap examined the specified network range and identified four active hosts.

### Scan Results

| S/N | IP Address | Status |
|---|---|---|
| 1 | 172.20.10.1 | Host Up |
| 2 | 172.20.10.7 | Host Up |
| 3 | 172.20.10.10 | Host Up |
| 4 | 172.20.10.11 | Host Up |

The scan summary displayed:

```text
16 IP addresses scanned
4 hosts up
```

![zenmap](screenshots/zenmap.png)

---

## 4. MAC Address Identification

The scan output also displayed MAC address information for some discovered hosts.

One of the devices was identified with the following manufacturer information:

| Information | Result |
|---|---|
| IP Address | 172.20.10.11 |
| Manufacturer | Liteon Technology |

MAC address information can help identify network devices and distinguish individual interfaces on a local network.

The captured output did not provide sufficiently clear MAC details for every host, so only the readable manufacturer information has been documented.

---

## 5. Network Scan Summary

The completed scan produced the following results:

| Description | Result |
|---|---|
| Target Network | 172.20.10.0/28 |
| Total IP Addresses Scanned | 16 |
| Active Hosts | 4 |
| Scan Type | Ping Scan |
| Nmap Option | -sn |
| Scan Duration | 4.15 seconds |

The four responding IP addresses were `172.20.10.1`, `172.20.10.7`, `172.20.10.10` and `172.20.10.11`.

The address `172.20.10.10` belonged to the computer from which the network configuration was obtained, while `172.20.10.1` was identified as the default gateway.

---

## 6. Network Topology

The scan identified four active IP addresses within the `172.20.10.0/28` network.

The discovered network addresses were:

- 172.20.10.1
- 172.20.10.7
- 172.20.10.10
- 172.20.10.11

These results provided a basic view of the active devices within the scanned subnet.

---

## 7. Analysis and Security Implications

The scan demonstrated how Nmap can identify devices responding within a specified network range.

The discovery of four active hosts provides a starting point for network inventory and further assessment.

In a real network environment, this information can help administrators identify connected devices, investigate unfamiliar hosts and maintain an accurate record of network assets.

The scan was limited to host discovery. It did not establish which ports were open or which services were running on the discovered devices. Those would require additional authorized scanning.

# CONCLUSION

This practical covered two important areas of cybersecurity reconnaissance: website footprinting and network host discovery.

In Part 1, I used six Kali Linux tools to investigate `networkwalks.com`. I retrieved domain registration information, resolved its IP address, inspected HTTP response headers, enumerated DNS records, identified its web technologies and detected ModSecurity as its Web Application Firewall.

In Part 2, I used Windows Command Prompt to identify my local network configuration and Zenmap to scan the `172.20.10.0/28` subnet. The scan examined 16 IP addresses and identified four active hosts.

The practical strengthened my ability to use reconnaissance tools, interpret their output and document technical findings. It also gave me a clearer understanding of how domain investigation and network discovery contribute to the initial stages of a cybersecurity assessment.

## AUTHOR 
joshua mayowa @sudomayor on X

