# packet-tracer-advanced-routing.

# Advanced Network Routing & Security – Cisco Packet Tracer

## Project Overview
Built and configured a multi-domain network in Cisco Packet Tracer to model an enterprise connected to a service provider. The lab covered dynamic routing (OSPF), inter-domain routing intent (BGP), access control, NAT (static + PAT), and core services (DNS/HTTP). Validation included CLI show commands and client browsing tests.

## Topology (Conceptual)
- Enterprise edge routers (R1, R2) with a LAN per site.
- Service/peering router (R3) representing an external AS.
- PCs in 192.168.1.0/24, 192.168.2.0/24 and a separate 172.16.0.0/16 segment.
- HTTP/DNS server at 192.168.1.3; public mapping 222.223.50.3.

## Addressing Highlights
- Backbone / WAN link: 192.168.0.0/24 (R1–R2).
- Site networks: 192.168.1.0/24, 192.168.2.0/24.
- External / “internet”: 222.223.50.0/24.
- Lab / campus: 172.16.0.0/16.

## Configuration Summary
1. **OSPF**
   - R1 and R2 run OSPF; Area 0 on the backbone, Area 1 for the site LAN.
   - Verified adjacencies and route exchange.

2. **eBGP (design intent)**
   - R2 ↔ R3 peering to advertise 222.223.50.0/24.
   - Note: If the selected Packet Tracer image lacks BGP support, I document peering intent and route exchange using supported platforms (e.g., 2911) or emulate advertisement via static routes for end-to-end tests.

3. **ACL**
   - Extended ACL denying inbound traffic to host 192.168.2.1, while permitting other flows.

4. **Services**
   - DNS & HTTP enabled on 192.168.1.3 with an A record for `first_lastname.com`.

5. **NAT**
   - Static NAT: 192.168.1.3 → 222.223.50.3 for public reachability.
   - PAT (overload) for 172.16.0.0/16 to access external networks.

6. **Validation**
   - Routing: `show ip route`, `show ip route ospf`.
   - Peering: `show ip bgp summary` (when supported), or static test routes.
   - ACLs: `show access-lists`, positive/negative pings.
   - NAT: `show ip nat translations`.
   - Client test: Browser to `http://first_lastname.com` resolves and loads.

## Representative CLI Snippets

### OSPF (R1)
router ospf 1
network 192.168.0.0 0.0.0.255 area 0
network 192.168.2.0 0.0.0.255 area 1

### OSPF – R2

router ospf 1
network 192.168.0.0 0.0.0.255 area 0
network 192.168.1.0 0.0.0.255 area 0

### eBGP (when supported)

router bgp <AS_R2>
neighbor <R3_IP> remote-as <AS_R3>
network 222.223.50.0 mask 255.255.255.0

### ACL – Deny inbound traffic to 192.168.2.1

ip access-list extended BLOCK-PC-192_168_2_1
deny ip any host 192.168.2.1
permit ip any any
interface Serial2/0
ip access-group BLOCK-PC-192_168_2_1 in

### NAT (R3)

interface 
ip nat inside
interface 
ip nat outside

ip nat inside source static 192.168.1.3 222.223.50.3

access-list 10 permit 172.16.0.0 0.0.255.255
ip nat inside source list 10 interface  overload


### DNS/HTTP (Server 192.168.1.3)
- HTTP: ON  
- DNS: `first_lastname.com → 192.168.1.3`

---

## Outcomes
- End-to-end connectivity across OSPF domains.  
- Public exposure of internal web server via Static NAT.  
- Restricted access with ACLs.  
- Name resolution via DNS and HTTP access tested from clients.  







