# arpanet-nic

**Coordination and infrastructure for a growing, historically-accurate reconstruction of the
NCP-era ARPANET — a network of real replica and emulated hosts, connected through real IMPs.**

This project is the **NIC + NCC + backbone** for that network:

- **NIC** (Network Information Center) — the registry: host/IMP number assignment, the host table,
  WHOIS/Finger-style directory, and the place you request a connection.
- **NCC** (Network Control Center) — live status/monitoring of the IMP mesh and every node.
- **Backbone** — a small set of always-on IMPs (the "BBN role") that members attach their host to,
  or trunk their own IMP into.

It is **not** a rebuild of any single lab. It is the coordination point that lets *anyone* with a
period-correct machine — a PiDP replica (10/11/8/1), or an emulator (SIMH, klh10, dps8m, Hercules,
DtCyber, the h316 IMP) — join one shared ARPANET.

> **Status: PLANNING / DOCUMENTATION.** Nothing here is deployed yet. This repository currently holds
> the design: the charter (what period we freeze and why), the architecture, the topology, how you
> join, the transport/security model, and the period telecom model. Code and running services come
> after the design is settled.

## Why this exists

Replica IMPs and hobby PDP machines are multiplying. Each one on its own is an island. The real
ARPANET was never a single central switch — it was a *distributed* network with two genuinely central
*services*: **BBN** ran the IMP subnet and the NCC; **SRI** ran the NIC (host 2). This project
provides exactly those two services so independent machines can form one coherent network instead of
a pile of point-to-point hacks.

## Guiding principle: historical accuracy first

**People who lived the ARPANET will use this.** The *guest* experience — the `@L` login, site
heralds, NCP behavior, IMP quirks, and the feel of a 50 kbps line — must be as accurate as we can
make it. Everything a user sees inside the network is the sacred historical artifact.

The *transport underneath* (how bytes cross the modern internet between two emulated IMPs) is not
part of that artifact — the real ARPANET's leased lines were just an opaque, private wire. We are
free to harden the wire (encryption, NAT traversal) as long as it stays invisible to the guest.

See [`docs/charter.md`](docs/charter.md) for the full statement.

## Documentation

| Doc | What it covers |
|---|---|
| [user-journeys.md](docs/user-journeys.md) | Master list of every journey — site = connect + observe; use is host-to-host |
| [exhibit-and-platform.md](docs/exhibit-and-platform.md) | One site, two front doors: the open Exhibit vs. the account-gated living Platform, and the access model |
| [charter.md](docs/charter.md) | The frozen period, the values, what's in scope |
| [architecture.md](docs/architecture.md) | The BBN + NIC roles, the living-network model, the address ceiling |
| [network-topology.md](docs/network-topology.md) | The seed backbone, trunk spine, numbering, geographic continuity |
| [joining.md](docs/joining.md) | The two ways to join, the request flow, what we collect |
| [request-flow-and-config.md](docs/request-flow-and-config.md) | End-to-end: request → assignment → generated `wg0.conf` + `attach -u` bundle → bring-up |
| [transport-and-security.md](docs/transport-and-security.md) | How IMPs actually connect (verified), WireGuard packaging, security |
| [bootstrap.md](docs/bootstrap.md) | The one-command connector (`arpanet-nic-connect`) — design reference |
| [diagnostics.md](docs/diagnostics.md) | Push-button "Diagnose my connection" — the layer ladder we read from our side |
| [telecom.md](docs/telecom.md) | Period line grades (50 kbps &c.), latency, the satellite link |
| [references.md](docs/references.md) | Primary sources for every historical and technical claim |

## Roadmap (implementation milestones)

Tracked as GitHub issues (dependency-ordered):

1. [M1 — Backbone consolidation](../../issues/1) (existing lab becomes the backbone)
2. [M2 — WireGuard transport + hub endpoints + relay fallback](../../issues/2)
3. [M3 — NIC registry + accounts (GitHub OAuth) + auto-provisioning](../../issues/3)
4. [M4 — Connector / bootstrap packaging](../../issues/4)
5. [M5 — Backbone access control (registration gates the net)](../../issues/5)
6. [M6 — Line grades / period telecom](../../issues/6)
7. [M7 — NCC + push-button diagnostics](../../issues/7)
8. [M8 — The Exhibit (open web terminal + TIP node)](../../issues/8)
9. [M9 — Platform website (two front doors)](../../issues/9)
10. [M10 — Lifecycle & operator tooling](../../issues/10)

## Related projects (kept separate on purpose)

- **kurthamm/arpanet** — Kurt's own lab reconstruction. Becomes the *first member* on this network,
  not the base of it.
- **kurthamm/pidp10-arpanet-node** — how-to for attaching one physical PiDP-10 replica.

This repo is the shared standard those (and everyone else's nodes) connect through.
