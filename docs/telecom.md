# Period telecom

The trunks between IMPs are "leased lines," and we reproduce the *feel* of period telecom — because
people who lived it will notice.

## Line speed is modeled natively (verified)

The SIMH IMP modem interface has a **simulated line speed**: `SET MIn BPS=<rate>` (the Open SIMH IMP
doc's example is `BPS=56000`), and the simulator **computes transmission timing from that rate**. So
a 50 kbps line genuinely *behaves* like 50 kbps — the throttle is real, not cosmetic. (Exact set
syntax to be confirmed against the deployed `h316` binary; the capability is documented.)

What SIMH does **not** model is **propagation delay / latency** — it only throttles bandwidth. That
is injected **hub-side** (platform-independent, so it applies regardless of the member's OS).

## Line grades (real period options)

Every trunk has a grade. The default is the iconic ARPANET line.

| Grade | Rate | Character |
|---|---|---|
| Slow leased line | 9.6 kbps | the scenic route; real (tails / Distant Host) |
| **Standard trunk** | **50 kbps** | **the iconic ARPANET backbone line — the default** |
| DDS digital | 56 kbps | slightly later-period |
| High-speed trunk | 230.4 kbps | the "fat pipe" busy trunks got (late-'70s) |
| **Satellite (SATNET-style)** | ~64 kbps + ~560 ms round-trip | authentic for **transatlantic**; relay injects the lag |

## Policy

- **Default: everyone gets the 50 kbps standard trunk.** It's the canonical line.
- **You may request a different grade** when you order a connection; the NIC "provisions" it.
- **Geography can set it:** a European member's transatlantic link is naturally the **satellite**
  grade — real ~560 ms lag, so it *feels* like reaching across the ocean in 1973.
- **No charges.** The real lines cost thousands/month; ours are free. But we keep the *ritual* — you
  "order a 50 kbps line to the West hub," the NIC provisions it, and it appears on the map with its
  grade. Free, but the romance of leasing a circuit stays.

## Where the knobs live

- **Bandwidth:** `BPS` on the MI device at **both ends** of a trunk — baked into the `attach -u`
  lines the NIC generates.
- **Latency:** shaped hub-side, per grade.
- **Assignment:** the registry records your grade; the config generator and the hub enforce it.
