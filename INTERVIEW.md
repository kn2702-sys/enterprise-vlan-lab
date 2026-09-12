# Interview answers this lab trains

Use these as **spoken** answers; point to your Packet Tracer screenshots.

### What is a VLAN?
A VLAN is a Layer-2 broadcast domain carved out of a switch. Devices in VLAN 10 (HR) do not see broadcasts from VLAN 20 (IT) even on the same physical switch fabric. We use VLANs for segmentation, security boundaries, and smaller broadcast domains.

### Why do we use trunk ports?
A trunk carries **multiple VLANs** between switches (or switch↔router) using **802.1Q tags**. Access ports carry a single untagged VLAN for end devices. Without trunks, each VLAN would need its own physical uplink.

### What happens if the native VLAN is wrong?
The native VLAN is the one sent **untagged** on a trunk. If the two ends disagree, untagged frames are mapped to different VLANs → black holes, management issues, and CDP “native VLAN mismatch” warnings. Always match native VLAN on both ends.

### How can two VLANs communicate?
They need a **Layer-3 device**: router-on-a-stick, multilayer switch SVI, or firewall. In this lab, R1 subinterfaces route between VLAN 10/20/30/99.

### What is router-on-a-stick?
One physical router interface with **subinterfaces**, each tagged with `encapsulation dot1Q <vlan>` and an IP used as that VLAN’s default gateway. Traffic between VLANs is routed on the router; the switch trunk delivers all tagged VLANs on one cable.

### Why can a PC talk inside its VLAN but not another VLAN?
Same VLAN = switching only (L2). Other VLAN needs correct **gateway**, working **trunk** to the router, and a matching **subinterface**. Typical fails: wrong gateway, trunk pruned VLAN, or `dot1Q` VLAN ID ≠ subnet (Fault F).
