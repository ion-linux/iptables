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


