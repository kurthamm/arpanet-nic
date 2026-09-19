# Charter

The rules of the network. These are curatorial decisions — deliberately fixed, so the network has a
stable identity that period-correct machines can be built and judged against.

## 1. The frozen period

The network reconstructs the **classic NCP-era ARPANET**. The core technology ran essentially
unchanged from the deployment of NCP (~1971) until the **TCP/IP flag day, January 1, 1983** — about
a twelve-year window in which the network grew from 4 hosts to over 100 but the *technology* barely
moved: **NCP, the 1822 host interface, and the Honeywell 316/516 IMP.**

- **Technology envelope:** NCP (no TCP/IP), 1822 host↔IMP interface, Honeywell 316-class IMPs.
- **Hard boundary:** nothing past **Jan 1, 1983**. The flag day is the wall.
- **Reference snapshot:** **1973** — mature NCP, the first international nodes (NORSAR, UCL London),
  and a documented host list (RFC 597, Dec 1973). Used as the canonical starting shape; see
  [network-topology.md](network-topology.md).

We freeze a period on purpose. Equipment does not get "upgraded" out from under people; IMPs do not
get retired for newer hardware. The point is to *inhabit* a technological moment, not to relive the
march to the modern internet.

### Later-period upgrades are an explicit, deferred choice

The real ARPANET hit its own scaling wall and solved it *within* the NCP era (the "new leader" /
1822L logical addressing, late 1970s — see [architecture.md](architecture.md)). That gives us a
historically-authentic way to raise the ceiling **if we ever need to**. We are **not** doing it now.
Starting constraints stand until there is a deliberate decision to advance the period. See the
address-ceiling section of [architecture.md](architecture.md).

## 2. Historical accuracy is the first value

**People who lived it will be on this network.** The bar is set by them.

- **The guest is sacred.** The `@L` login, heralds, NCP, IMP behavior, host operating systems, and
  the *feel* of a period line are the historical artifact. They are reproduced as faithfully as
  possible and are never altered for convenience.
- **The wire is not sacred.** How bytes cross the modern internet between two emulated IMPs is
  transport, not artifact. The real leased lines were opaque, private circuits; our encrypted
  tunnels are the modern stand-in for that same private wire. Hardening the wire is allowed *only*
  while it stays completely invisible to the guest.
- **Cite, don't guess.** Historical and technical claims in this repo carry sources
  ([references.md](references.md)). Corrections from people with first-hand knowledge are welcome and
  expected.

## 3. Two ways onto the net — both go *through* the IMPs

There is no third "bypass" option. A node is only really on the ARPANET if its traffic crosses the
IMP network.

- **Native NCP** — the host's own OS speaks 1822/NCP to its IMP (ITS, TENEX/TOPS-20, WAITS,
  ARPANET Unix). The golden path.
- **FEP (Front-End Processor)** — a small front-end speaks NCP on behalf of a host whose OS can't
  (Multics, IBM 360/370, Sigma, CDC). This is a *period-correct* method — real sites used front-ends
  this way — and it still routes through the IMPs.

**Banned: the terminal-line bypass** — wiring a user session straight to a simulator's TTY port,
skipping the IMP network. It looks like it works and defeats the entire purpose.

## 4. Membership and scope

- Any **period-correct machine** may join: PiDP replicas (10/11/8/1), Oscar's forthcoming H316 IMP
  replica, or software emulators (SIMH, klh10, dps8m, Hercules, DtCyber, the h316 IMP).
- The network keeps **geographic continuity** — nodes sit at real locations and trunk to nearby
  neighbors, the way the real net grew. See [network-topology.md](network-topology.md).
- This project runs the **shared services** (NIC registry, NCC status, backbone IMPs). It does
  **not** run recreated historical nodes — the machines on the map are real participants' real
  machines.

## 5. This is its own thing

Kept deliberately separate from any single contributor's lab, and not a rebrand of anyone else's
replica project. The engineering idea we take from the wider community is the right one: **route real
sessions through real IMPs.** The implementation and coordination here stand on their own.
