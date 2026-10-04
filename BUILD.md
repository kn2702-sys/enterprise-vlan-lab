# Build guide (Packet Tracer)

Estimated time: 45–75 minutes first build.

1. **New** Packet Tracer file → save locally as `enterprise-vlan-lab.pkt` (gitignored).
2. Place **R1**, **CORE-SW**, **ACCESS-SW1/2/3**, six **PC-PT**.
3. Cable using the map in `configs/vlan-config.txt` header.
4. Configure switches from `configs/vlan-config.txt` (CORE first, then access).
5. Configure R1 from `configs/router-config.txt`.
6. PCs → IP Configuration → **DHCP**.
7. Verify:
   - `ping` own gateway
   - HR PC → IT PC → Finance PC
   - From CORE-SW: `ping 192.168.99.1`
   - `show spanning-tree` → CORE is root
8. Capture screenshots listed in `screenshots/README.md`.
9. Run every fault in `troubleshooting.md`.

If interface names differ (`Fa0/24` vs `Gi0/1`), rename in the config before pasting; keep IPs and VLAN IDs identical.
