# Connection diagnostics

A push-button **"Diagnose my connection"** for members, plus the same view for the operator.

> **Status: design.** The signals below are what the architecture makes observable; the exact
> host-status/host-dead signals will be confirmed against the h316 IMP implementation when we build.

## Why it works: we already have the view

The member's **WireGuard tunnel**, their **1822 trunk/host line**, and the **IMP routing** all
terminate on **our** side (the hubs / NCC). So diagnostics read *our own* state — **the member sends
us nothing.** This makes "Diagnose" a member-facing slice of the [NCC](architecture.md): same data,
two audiences.

## The layer ladder (outside in)

Each check has a verdict and, on failure, a specific fix. We stop reporting "broken" at the first
failing layer (the ones above it can't be judged until it's fixed).

| # | Layer | What we read | Failure means | What we tell them |
|---|---|---|---|---|
| 1 | **Tunnel** | WireGuard last-handshake + last-seen endpoint | We've heard nothing from your connector | Connector not running, or outbound UDP blocked/firewalled. Start the connector; allow outbound UDP. |
| 2 | **Trunk/host line** | IMP line state + per-line debug on your assigned port | Tunnel up, but no valid 1822 traffic on the line | Sim not running, wrong `attach -u` ports, or `BPS` mismatch. Here are the exact ports/BPS you should use. |
| 3 | **Host-ready** | 1822 host-ready signal (hosts on *our* IMPs) | Line synced, but the host isn't signaling ready | Host OS not booted, or its IMP device isn't attached. Boot the host; attach its IMP device to `<ports>`. |
| 4 | **NCP / app** | active NCP connect probe (e.g. to the login socket) | Host ready, but not answering NCP | Host up but NCP not running / login server down. |

## What we can and can't see

- **Remote IMPs — yes, natively.** Our backbone IMPs know which IMP numbers are reachable from their
  routing. Reading that is the classic **NCC** function (BBN watched IMP/line up-down network-wide).
- **Hosts on our IMPs (Tier 1 & 2) — yes.** We see host-ready line state directly.
- **Hosts on a remote member's IMP (Tier 3) — indirectly.** We see *their IMP* is up via routing, and
  determine the *host* with an **active probe** (the network returns "destination host dead" if it
  isn't there). Not passively visible; actively testable.

## Example verdict

```
Diagnose: COLUMBIA-ITS
  ✅ Tunnel up          last handshake 6s ago
  ✅ Line synced        mi/hi on 21621 <-> tunnel 10.42.2.12
  ⚠️ Host not ready     line is up but no host-ready signal
     → Your PDP-10 sim is reachable but not signaling host-ready.
       Check the sim is booted and its IMP device is attached to
       21622:10.42.2.0:21621.
```

## "Is the node I'm trying to reach up?"

The same machinery answers the *other* direction: when a member can't reach another node, we report
that target's status straight from the [NCC](architecture.md) view (IMP reachable? host-ready? or a
host-dead probe for a Tier-3 host).

## Optional: local self-check

The connector can run a small local check too (is my tunnel up? is my sim attached to the expected
ports?) — complementing the server-side view for the fullest picture. Cheap; nice-to-have.
