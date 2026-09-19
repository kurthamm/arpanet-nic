# Transport and security

This is the *wire* — below the 1822/NCP layer, invisible to the guest (see [charter.md](charter.md)).
Everything here is verified against the actual simulator and upstream sources; citations in
[references.md](references.md).

## How IMPs actually connect (verified)

Both trunk types are **raw UDP socket pairs** via SIMH's `attach -u`:

- **IMP ↔ IMP (modem interface):** `attach -u mi2 <lport>:<remoteIP>:<rport>`
- **Host ↔ IMP (host interface):** IMP side `attach -u hi2 <lport>:<remoteIP>:<rport>`; the host
  sim's own IMP device is the mirror image.
- **FEP** (for non-NCP hosts): `ncp-telnet` carries NCP through the IMP; a small bridge relays that
  to the host sim's terminal line. This runs *local to the member's host* and is not part of the
  trunk transport.

**The constraint that drives the whole design:** the SIMH IMP UDP link requires a **fixed, known
remote address at attach time**. There is **no dynamic peer discovery and no NAT traversal** — the
Open SIMH IMP doc gives the syntax `ATTACH MIn llll:w.x.y.z:rrrr` (remote mandatory) and states
"connections must be one to one." DNS names are allowed but resolved *once*, at attach.

Consequence: our hubs have stable public addresses (fine), but a member on a **dynamic IP or behind
CGNAT** has no fixed address for our hub to send return packets to. **This is a protocol limitation,
not a user-skill problem** — no amount of config editing fixes it in raw SIMH. Something must give
both ends a stable, reachable address.

## Primary solution: packaged WireGuard

WireGuard is the modern equivalent of the ARPANET's dedicated leased line — a private point-to-point
circuit. It solves the SIMH problem *completely* and adds real security:

- **Stable addressing** — inside the tunnel both ends get a fixed private IP (e.g. `10.x.x.x`) that
  never changes even as the member's real internet IP roams. That is exactly what `attach -u`
  demands, permanently.
- **Dynamic IP + CGNAT handled** — the member dials *out* with `PersistentKeepalive`; no
  port-forwarding, works behind carrier-grade NAT.
- **Cryptographic identity + encryption** — real impersonation-proofing.

It stays entirely below the 1822 layer, so the guest experience is untouched.

### Packaging (removes the only downside — setup friction)

1. **The registry generates a complete `wg0.conf`** per member: hub `Endpoint`, assigned tunnel IP,
   `AllowedIPs`, `PersistentKeepalive = 25`. No hand-editing.
2. **Raspberry Pi / Debian** (Oscar's replicas + most SIMH users — the common case): a **one-command
   bootstrap** installs `wireguard`, generates the keypair **locally** (private key never leaves the
   box; only the public key is submitted), installs the conf as a `wg-quick@` service, and sets the
   sim's `attach -u` to point at the stable tunnel IP. Shippable as a `.deb`.
3. **macOS / Windows:** install the official WireGuard app, import the generated `.conf`, activate.
   Two clicks.

## Fallback: our-side UDP relay (no user tunnel)

For anyone who genuinely cannot run WireGuard: a small **UDP relay** in front of each hub IMP. The
member points their `attach -u` at the relay's public port and dials out; the relay **learns their
live source address** from incoming packets and forwards both directions to the local hub IMP.

- **Member effort:** one `attach -u` line. No app, no port-forward.
- **Cost:** weaker auth (possession of a per-member secret relay port + source-pinning, not crypto)
  and unencrypted traffic. Acceptable for a hobby net; not the default.

## Security model

| Threat | Mitigation |
|---|---|
| **Impersonation** (claiming someone else's IMP/host number) | Per-node key (WireGuard). Only the private-key holder can be that node. |
| **Scanning / DoS on public ports** | Raw 1822 ports are never exposed — only the WireGuard listener (silent to unauthenticated packets) or the relay. |
| **Squatting** numbers/hostnames | NIC approval + uniqueness; numbers are scarce (63 IMPs) and gated. |
| **A legitimately-joined member misbehaving** (flood, bad routing) | Instant **key revocation** (pull from the hub's allowed list — no IP chase), hub-side rate-limiting, and **NCC monitoring** as the abuse console — exactly what BBN's NCC did. |
| **Privacy** | The hub sees a member's real IP; other members do not (traffic relays through the hub). Public map location can be approximate. |

## Access control: registration gates the network

Every host must be registered with the NIC — **wherever it sits, including behind a member's own
IMP.** Registration is not paperwork; it is the **access grant**:

- Inter-node traffic routes **through the backbone hubs** (a deliberate choke point).
- Each packet carries its source address `(IMP number, host number)`. The backbone passes traffic
  **only from a registered `(IMP, host)` pair**; anything else is dropped.
- So an **unregistered host is confined to its own local IMP** — it cannot reach any other host on
  the network. **Register → access; revoke/deregister → access removed**, instantly.

This holds for *every* connection combination (see the connect axes in
[user-journeys.md](user-journeys.md)), so unlimited mixing stays fully under central control. We
cannot *prevent* a member adding a host to their own IMP (their box), but we **see it** (an unknown
host number appearing at the backbone) and it **cannot traverse the net** until registered. The
filter lives at our infrastructure edge — invisible to the guest; a registered host's 1822/NCP
experience is unchanged.

## What we collect at request time

See [joining.md](joining.md). Notably: contact, approximate location, machine + OS, desired hostname,
and the member's **public key** — but **never their IP address** (they dial out; we don't need it).
