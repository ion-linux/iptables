# iptables

[Netfilter](netfilter.org) is a software firewall inside Linux kernel.
_iptables_ command configures the iptables software, root privileges are required.
Every packet is inspected by firewall rules. 

Iptables firewall uses TABLEs to organize it's rules. Further on tables are organized into CHAINS.
A rule is placed in a specific CHAIN on a specific TABLE.

Top to bottom match, when match is found the TARGET (action) is executed.

netfilter tables:
- filter
    - default table for iptables
    - built-in chains: INPUT, OUTPUT, FORWARD 
- nat
    - used for SNAT and DNAT
    - built-in chains: PREROUTING, POSTROUTING, OUTPUT (local generated packets)
- mangle
    - packet alteration
    - built-in chains: PREROUTING, INPUT, FORWARD, OUTPUT, POSTROUTING
- raw
    - can mark packets that should not be handled by the connection tracking system
    - use NOTRACK target on the packet
    - built-in chains: PREROUTING and OUTPUT
  

CHAIN traversal:
  - incoming traffic filtered on the INPUT CHAIN of the FILTER TABLE
  - outgoing traffic filtered on the OUTPUT CHAIN of the FILTER TABLE
  - routed traffic is filtered on the FORWARD CHAIN of the FILTER TABLE
  - SNAT/MASQUERADE is performed on the POSTROUTING CHAIN of the NAT TABLE
  - DNAT/port-forwarding is performed on the PREROUTING CHAIN of of the NAT TABLE
  - if packet headers modified in the MANGLE TABLE
  - skip connection tracking via NOTRACK target rules in the RAW TABLE


# iptables command
iptables [-t table_name] -COMMAND CHAIN_NAME matches -j TARGET/JUMP

Table:
- filter (default)
- nat
- mangle
- raw

Command:
- A 
    - append rule at the end of the selected chain   
- I 
    - insert one or more rules in the selected chain o specific position
    - default position is 1
- D 
    - delete one or more rules from selected chain
- R 
    - replace a rule in the selected chain
- F 
    - flush selected chain
    - all chains in the table if none is given
- Z 
    - zero the counters for byte and byte counters on a specific chain if given
    - no chain given, all chains are selected
- L 
    -  List all rules in the selected chain
    -  if no chain selected, all rules are listed
- N 
    - create a new user defined chain
- X 
    - delete the user defined chain
- P 
    - set the policy for the built-in chain (INPUT, OUTPUT or FORWARD)

Chain:
- INPUT
- OUTPUT
- FORWARD
- PREROUTING
- POSTROUTING
- USER_DEFINED

Matches:
- -s - source_ip
- -d - dest_ip
- -p - protocol
- --sport - source_port
- --dport - destination_port
- -i - incoming interface
- -o - outgoing interface
- -m mac
- -m time
- -m quota
- -m limit
- -m recent

Target/Jump:
- ACCEPT
- DROP
- REJECT
- LOG
- SNAT
- DNAT
- MASQUERADE
- LIMIT
- RETURN
- TEE
- TOS
- TTL

# Examples
List details of filter table
```
iptables -t filter -vn -L
```

Drop all icmp requests then check the filter TABLE
```
iptables -t filter -A INPUT -p icmp --icmp-type echo-request -j DROP
```


Deny all traffic to ubuntu.com from the linux host
```
iptables -t filter -A OUTPUT -p tcp --dport 80 -d www.ubuntu.com -j DROP
iptables -t filter -A OUTPUT -p tcp --dport 443 -d www.ubuntu.com -j DROP
```

Drop port 69by inserting on top of input chain:
```
iptables -I INPUT -p udp --dport 69 -j DROP
```

Drop port 23 on third position of input chain:
```
iptables -I INPUT 3 -p tcp --dport 23 -j DROP
```

Accept port 22 on top to vew the hitcounts, clear counters then list:
```
iptables -A OUTPUT -p tcp --dport 22 -j ACCEPT
```
```
iptables -Z
```
```
iptables -L -vn
```

Create MYCHAIN:
```
iptables t filter -n MYCHAIN
```

Change default policy from ACCEPT to DROP:
```
iptables -P FORWARD DROP
```

Delete in the OUTPUT chain rule number 2:
```
iptables -D OUTPUT 2
```

