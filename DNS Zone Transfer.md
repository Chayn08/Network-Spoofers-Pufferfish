# Objective

The goal is to use tools like dig and nmap to retrieve DNS records from a vulnerable server (10.10.30.13) hosting the zone jedha-cs-fullstack.org.

Tools Required:

- Terminal (Linux/Unix environment recommended)

- dig command (part of the dnsutils package)

- nmap command (with default scripts installed)

  ### DNS Zone Transfer with dig

DNS Zone Transfers use a specific query type and protocol: AXFR (Full Zone Transfer). To request a transfer of a specific DNS zone from a server, you need:

1. The IP address of the DNS server.
2. The domain name of the DNS zone.

In our case:

- Domain: jedha-cs-fullstack.org
- DNS Server IP: 10.10.30.13

```
dig @10.10.30.13 jedha-cs-fullstack.org AXFR
```

- @10.10.30.13: The IP address of the DNS server to query.
- jedha-cs-fullstack.org: The domain we want to transfer.
- AXFR: The query type for a full zone transfer.

If the server is vulnerable, this command will display all DNS records for the domain!

### DNS Zone Transfer with nmap

nmap is one of the most powerful tools for penetration testing and network scanning.

It also includes a script specifically for DNS Zone Transfers called dns-zone-transfer.

```
nmap --script dns-zone-transfer -p 53 --script-args dns-zone-transfer.domain=jedha-cs-fullstack.org 10.10.30.13
```

- --script dns-zone-transfer: This invokes the nmap script for attempting a zone transfer.
- -p 53: This specifies the DNS service port (53).
- --script-args dns-zone-transfer.domain=jedha-cs-fullstack.org: This defines the domain we want to transfer.
- 10.10.30.13: The IP address of the DNS server.

If successful, nmap will list all DNS records for the domain.

### Explanation of Results

1. Using dig:

- If the command dig @10.10.30.13 jedha-cs-fullstack.org AXFR is successful, it will return DNS records such as:
  
- A Records: IP addresses associated with subdomains.
- CNAME Records: Canonical names or aliases.
- NS Records: Nameservers for the domain.
- SOA Record: The Start of Authority record for the domain.
  
- A successful output indicates that the DNS server is misconfigured and allows unauthorized zone transfers, exposing internal network details.

2. Using nmap:

- If the nmap command successfully retrieves DNS records, it confirms that the DNS server is open to zone transfers. The output should be similar to what dig shows, detailing subdomains, IP addresses, and other DNS information.

### Conclusion

If the DNS Zone Transfer is successful, it reveals that the DNS server is vulnerable and needs to be secured. In a real-world scenario, this could expose sensitive information about a network's internal structure, which is a major security risk.

Always remember: Unauthorized attempts to perform DNS Zone Transfers on servers you do not own or do not have permission to test are illegal. Only perform these actions in controlled environments or with explicit permission.

