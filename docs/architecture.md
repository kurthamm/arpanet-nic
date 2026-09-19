# Architecture

## The two central services (what this project runs)

The real ARPANET was distributed — no central switch — but it had two genuinely central *services*.
This project provides exactly those:

### The BBN role — run the subnet
BBN built the IMPs and ran the **Network Control Center**, the 24/7 nerve center that monitored every
IMP and kept the network alive. We reproduce that:

- **Backbone IMPs** — a small set of always-on Honeywell-316 (h316) IMP emulators at real geographic
  anchors. Members either attach a host to one, or trunk their own IMP into the spine. See
  [network-topology.md](network-topology.md).
- **NCC status** — live monitoring of the mesh and every node: who's up, link state, anomalies. This
  is also the **abuse console** (see [transport-and-security.md](transport-and-security.md)).

### The NIC role — run the directory
SRI ran the **Network Information Center** (host 2): the official host table, the RFC repository,
WHOIS/Finger, and the protocol handbooks. We reproduce that:

- **Registry** — assigns IMP numbers and host numbers, tracks who owns what, enforces uniqueness.
- **Host table** — the authoritative list every node can pull (the modern equivalent of `HOSTS.TXT`).
- **Directory** — WHOIS/Finger-style lookup of nodes and operators.
- **Request intake** — the front door where you ask for a connection (see [joining.md](joining.md)).

## The living-network model

Every node on the map is a **real participant's real machine**. We do **not** run recreated
historical nodes. New members either:

1. **attach a host** to one of our backbone IMPs (the common case), or
2. **bring their own IMP** and trunk it into the spine (the advanced case).

The real 1973 ARPANET map is used as a **reference** — for where to anchor the backbone and for the
geographic placement logic — **not** as a set of historical seats to claim. See
[network-topology.md](network-topology.md).

## Machines that can participate

Any period-correct machine, as a physical replica or a software emulator, connected via **native
NCP** or a **FEP** (see [charter.md](charter.md)):

| Machine | Replica | Emulator | Real ARPANET role | NCP path |
|---|---|---|---|---|
| PDP-10 | PiDP-10 | SIMH, klh10 | ITS, TENEX, TOPS-10/20, WAITS | native |
| PDP-11 | PiDP-11 | SIMH | ARPANET Unix, ANTS | native (Unix) / FEP |
| PDP-1 | PiDP-1 | SIMH | BBN-1D (real host) | native / FEP |
| PDP-8 | PiDP-8 | SIMH | marginal host | FEP |
| Multics (H6180) | — | dps8m | MIT-Multics | FEP |
| IBM 360/370 | — | Hercules | UCLA-CCN &c. | FEP |
| XDS Sigma 7/9 | — | SIMH | UCLA-NMC | FEP |
| CDC 6000 | — | DtCyber | CDC sites | FEP |
| Honeywell 316 IMP | Oscar's (forthcoming) | h316 | the IMP itself | — |

## The address ceiling — a real, period-authentic limit

The 1822 "old leader" addressing caps the network:

- **IMP number is 6 bits → ~63 IMPs, ever.** A hard historical limit.
- **~4 hosts per IMP** → the real host ceiling is roughly **63 × 4 ≈ 250 hosts**, not 63.

This is why the [joining](joining.md) policy **prefers attaching a host to an existing IMP over
spinning up a new one**: it is a 4× multiplier on capacity *within the pure period*, and it mirrors
how the real net packed multiple hosts onto one IMP in dense areas. Scarcity is treated as a
*feature* — a finite roster makes an IMP number mean something, the way being one of the original
ARPANET sites did.

### The authentic escape hatch (deferred)

When the real ARPANET outgrew this, BBN introduced the **"new leader" (1822L logical addressing)** in
the late 1970s, expanding the IMP field (~16 bits) into the thousands — **still NCP, still pre-1983.**
So raising the ceiling is a documented, period-correct move available to us *if* we ever choose to
advance the frozen period from ~1973 toward ~1978. Per the [charter](charter.md) we are **not** doing
this now; the ~250-host runway (via host-attach) means the decision is likely years away.

**Not on the table:** interconnecting multiple separate ARPANETs via gateways — that is precisely
what TCP/IP was invented to do (the mid-'70s "Catenet"), and it slides out of period. One network,
under one ceiling.
