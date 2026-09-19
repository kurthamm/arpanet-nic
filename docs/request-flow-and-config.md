# Request flow and generated config

How a request becomes a live node. This is the **target design**; nothing here is running yet.
Config below is illustrative — the NIC fills the placeholders. Addresses/ports/keys are examples.

## End to end

```
member fills form  ─►  NIC assigns (number, peers, line grade)  ─►  NIC emits a bundle
        │                                                                     │
        ▼                                                                     ▼
 generate keypair locally                                        member runs bootstrap / imports conf
 (private key stays put; submit PUBLIC key)                      ─►  WireGuard up, sim attached
                                                                 ─►  node appears on the NCC map
```

## 1. The request (see [joining.md](joining.md) for fields)

Contact, approximate location, machine + OS, desired hostname, desired
[line grade](telecom.md) (default 50 kbps), and the member's **public key**. Never the IP.

## 2. NIC assignment

The registry:
- picks the connection type — **host-attach** (nearest hub with a free host port) or **bring-an-IMP**
  (next free IMP number 5–63 + trunk peers), by real geography ([network-topology.md](network-topology.md));
- assigns a **stable tunnel IP** (inside the WireGuard network — this is what makes SIMH's
  fixed-address requirement satisfiable forever; see [transport-and-security.md](transport-and-security.md));
- records the **line grade** → the `BPS` value both ends will use;
- reserves the **hostname** and allocates the host/IMP number;
- returns the bundle below.

## 3. The generated bundle

### a. `wg0.conf` (the private "leased line")

```ini
[Interface]
# NODE: MIT-STYLE-ITS   host 12 on hub CENTRAL (IMP 2)
PrivateKey = <generated locally — NEVER leaves the member's machine>
Address    = 10.42.2.12/32          # stable tunnel IP assigned by the NIC

[Peer]
# arpanet-nic CENTRAL hub
PublicKey           = <central hub public key>
Endpoint            = central.hub.arpanet-nic.example:51820
AllowedIPs          = 10.42.0.0/16  # the tunnel network (hub + peers)
PersistentKeepalive = 25            # dial-out; holds NAT/CGNAT mapping open
```

### b. The `attach -u` line(s), with the line-grade `BPS`

**Host-attach** (member's host sim reaches the hub IMP's host interface over the tunnel):
```
; host sim (e.g. PDP-10/ITS)
set imp enable
attach -u imp 20120:10.42.2.0:20012   ; 10.42.2.0 = CENTRAL hub tunnel IP (stable)
```

**Bring-an-IMP** (member's IMP trunks into the hub; both ends set the same BPS = line grade):
```
; member IMP (assigned IMP 7), trunk to CENTRAL hub (IMP 2)
set mi1 enabled
set mi1 bps=50000                     ; 50 kbps standard trunk
attach -u mi1 11107:10.42.2.0:11172   ; to CENTRAL hub tunnel IP
```
The hub side (configured by us) is the mirror image with matching `bps`.

### c. Host-table entry (the NIC's `HOSTS.TXT`)

```
HOST : 10.42.2.12 : MIT-STYLE-ITS : PDP-10 : ITS : NCP :
```

## 4. Bring-up

### Raspberry Pi / Debian (the common case) — one bootstrap
The `arpanet-nic-connect` script (shippable as a `.deb`):
1. installs `wireguard` if absent;
2. **generates the keypair locally**, submits only the public key to the NIC, receives the bundle;
3. drops `wg0.conf` and enables `wg-quick@arpanet` (systemd);
4. writes the `attach -u` / `bps` lines into the member's sim config;
5. verifies the tunnel is up and the trunk/host link is live.

### macOS / Windows
Install the official WireGuard app, **import the `wg0.conf`**, activate, then paste the `attach -u`
lines into the sim config. Two clicks plus one paste.

## 5. What the member never has to do

- Hand-write a WireGuard config or run `wg genkey`.
- Discover or configure their public IP; port-forward; fight CGNAT.
- Figure out addressing, trunk peers, or line-speed values.

The NIC does assignment + config generation; the member copies, pastes, runs — then shows up on the
[NCC](architecture.md) map. Their private key never leaves their machine.
