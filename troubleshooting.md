# Troubleshooting drills (break → diagnose → fix)

Use the lab **working** first (all cross-VLAN pings succeed). Introduce **one** fault at a time.

## Standard sequence

```text
Check physical/link
       ↓
Check VLAN
       ↓
Check access/trunk port
       ↓
Check IP
       ↓
Check gateway
       ↓
Check routing
       ↓
Test connectivity
```

Useful commands:

| Device | Commands |
| --- | --- |
| Switch | `show interfaces status`, `show vlan brief`, `show interfaces trunk`, `show spanning-tree`, `show running-config` |
| Router | `show ip interface brief`, `show ip route`, `show ip dhcp binding`, `show ip dhcp pool` |
| PC | `ipconfig` / `ipconfig /renew`, `ping`, Packet Tracer Simulation (ICMP) |

---

## Fault A — Wrong VLAN assignment

**Break (on ACCESS-SW1):** put PC-HR-1 port in VLAN 20.

```text
interface FastEthernet0/1
 switchport access vlan 20
```

**Symptom:** PC may get an IT DHCP address, or keep old HR IP and lose gateway. HR↔HR design breaks.

**Diagnose:** `show vlan brief`, PC `ipconfig`.

**Fix:** `switchport access vlan 10`, renew DHCP.

---

## Fault B — Incorrect trunk

**Break (on CORE-SW toward R1):** allow list missing VLAN 20.

```text
interface GigabitEthernet0/1
 switchport trunk allowed vlan 10,30,99
```

**Symptom:** IT PCs cannot reach other VLANs or their gateway; HR↔Finance may still work.

**Diagnose:** `show interfaces trunk` — VLAN 20 missing on the R1 trunk.

**Fix:** `switchport trunk allowed vlan 10,20,30,99`.

---

## Fault C — Wrong subnet on a PC

**Break:** set PC-IT-1 static `192.168.10.50/24` gateway `192.168.10.1` while still in VLAN 20.

**Symptom:** No DHCP; cannot reach IT peers that are on `192.168.20.0/24`. Same L2 VLAN, wrong L3 network.

**Diagnose:** Compare `ipconfig` to VLAN plan; `show vlan brief` on SW2.

**Fix:** DHCP again or static `192.168.20.x/24` gw `192.168.20.1`.

---

## Fault D — Wrong default gateway

**Break:** PC-FIN-1 static `192.168.30.11/24` with gateway `192.168.10.1`.

**Symptom:** Same-VLAN ping works; other VLANs fail. Simulation shows traffic dying or wrong next hop.

**Diagnose:** Check host gateway vs VLAN plan.

**Fix:** Gateway `192.168.30.1`.

---

## Fault E — DHCP pool misconfiguration

**Break (on R1):** wrong network under IT pool.

```text
ip dhcp pool IT
 network 192.168.10.0 255.255.255.0
 default-router 192.168.20.1
```

**Symptom:** IT PCs get addresses that do not match VLAN 20 / gateway behavior is broken.

**Diagnose:** `show ip dhcp binding`, `show ip dhcp pool`, PC `ipconfig`.

**Fix:** Restore pool:

```text
ip dhcp pool IT
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
```

---

## Fault F — Incorrect subinterface VLAN ID (classic ROAS bug)

**Break (on R1):** Finance subinterface tags VLAN 20 by mistake.

```text
interface GigabitEthernet0/0.30
 encapsulation dot1Q 20
 ip address 192.168.30.1 255.255.255.0
```

**Symptom:** Finance gateway unreachable or weird inter-VLAN behavior; tags and IP network disagree.

**Diagnose:** `show ip interface brief` + running-config subinterfaces; match `encapsulation dot1Q` to VLAN plan.

**Fix:**

```text
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
```

---

## Fault G — Native VLAN mismatch (interview favorite)

**Break:** set native VLAN 99 on CORE trunk to R1, leave R1 default native 1 (or set opposite on SW1 trunk only).

```text
! on CORE-SW Gi0/1
switchport trunk native vlan 99
```

**Symptom:** Management weirdness, CDP native VLAN mismatch warnings, intermittent issues depending on PT version.

**Diagnose:** `show interfaces trunk` — Native VLAN column; compare both ends.

**Fix:** Match native VLAN on both sides of every trunk (lab default: leave native 1, or set 99 on **both** ends deliberately).

---

## Definition of “lab repaired”

- [ ] Each PC has correct subnet + gateway `.1` for its VLAN  
- [ ] Ping gateway works  
- [ ] Ping across VLANs works (HR → IT → Finance)  
- [ ] `show interfaces trunk` lists 10,20,30,99  
- [ ] `show spanning-tree` shows CORE-SW as root  
