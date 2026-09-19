# References

Sources for the historical and technical claims in this repository. Historical accuracy is the first
value of this project (see [charter.md](charter.md)); corrections from people with first-hand
knowledge are welcome.

## Simulator / transport (verified this design cycle)

- **Open SIMH — ARPANET IMP/TIP extensions for the H316.**
  <https://opensimh.org/simdocs/h316_imp_doc.html>
  - Modem interface UDP attach: `ATTACH MIn llll:w.x.y.z:rrrr`; remote address mandatory;
    "connections must be one to one." No dynamic peer discovery / no NAT traversal.
  - Line speed: `SET MIn BPS=<rate>` (example `56000`); the sim models transmission timing from BPS.
    Propagation delay/latency is **not** modeled.
- **SIMH modem interface source** — `H316/h316_mi.c`.
  <https://github.com/simh/simh/blob/master/H316/h316_mi.c> (host interface: `h316_hi.c`)
- **larsbrinkhoff/imp** — a modern re-implementation of an ARPANET IMP.
  <https://github.com/larsbrinkhoff/imp>
- **HackerSmacker/ncp** — ARPANET NCP for Linux/UNIX/BSD/Windows + IMP-host interface to SIMH
  (originally by Lars Brinkhoff). <https://github.com/HackerSmacker/ncp>
- **The ARPANET IMP Program: Retrospective and Resurrection** (Walden et al.) —
  <https://walden-family.com/bbn/imp-code.pdf>

## Historical facts to keep sourced (to be pinned to primary docs)

The following are used across the design and should each be tied to a primary source as the docs
mature. Stated here so nothing rests on memory alone:

- **NCP era:** ~1971 (NCP deployed) to **Jan 1, 1983** (TCP/IP flag day). *[pin: BBN/ARPA histories,
  RFC 801 "NCP/TCP Transition Plan".]*
- **Addressing:** 6-bit IMP number → ~63 IMPs; ~4 hosts per IMP → ~250-host ceiling; the late-1970s
  **"new leader" / 1822L** logical addressing raised it. *[pin: BBN Report 1822; 1822L /
  RFC 851/852.]*
- **Roles:** ARPA/IPTO (funder); **BBN** (built IMPs, ran the NCC); AT&T (leased lines); **SRI-NIC**,
  host 2 (host table, RFCs, WHOIS); **UCLA** NMC (measurement). *[pin: ARPANET Completion Report.]*
- **1973 reference map:** ~40 IMPs; first international nodes **NORSAR** (Norway) and **UCL**
  (London); **RFC 597** (Dec 1973) host list. *[pin: BBN maps; RFC 597.]*
- **Line speeds:** 50 kbps standard trunk; 9.6 kbps slow lines; 230.4 kbps later high-speed trunks;
  **SATNET** satellite (~64 kbps, geostationary latency). *[pin: BBN reports; SATNET papers.]*
- **IMP #1** at UCLA, Sept 2, 1969. *[pin: ARPANET Completion Report / Kleinrock.]*
- **FEP / front-end** as a period-correct way to put a non-NCP host on the net. *[pin: site
  histories; the community's IMP/host tooling above.]*

> Convention: when a fact graduates from "known" to "pinned," replace its `[pin: …]` note with the
> primary citation inline.
