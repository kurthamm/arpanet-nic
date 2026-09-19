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

## Historical facts (pinned to primary sources)

Verified against primary documents so nothing rests on memory. Items still marked `[pin: …]` are not
yet tied to a primary source and should be treated as provisional.

- **The flag day — end of the NCP era.** The switch from NCP to TCP/IP was set for **1 January
  1983**. *RFC 801, "NCP/TCP Transition Plan," J. Postel (ISI), November 1981:* "The goal is to make
  a complete switch over from the NCP to IP/TCP by 1 January 1983."
  <https://www.rfc-editor.org/rfc/rfc801.txt>
- **Addressing — the "old leader" ceiling.** The original 1822 short-leader host address used a
  **6-bit IMP number and a 2-bit host number → 4 hosts per IMP** (hence ~63 IMPs, ~250 hosts). *BBN
  Report 1822 (Host–IMP Protocol); summarized at the Computer History Wiki, "Host-to-IMP Protocol,"*
  <https://gunkies.org/wiki/Host-to-IMP_Protocol>
- **Addressing — the "new leader" escape hatch.** The later **1822L long leader raised these to a
  16-bit IMP number and 8-bit host number** (thousands of IMPs), still within the NCP era. *RFC 802
  (1981), RFC 851, RFC 878, "ARPANET 1822L Host Access Protocol."*
  <https://www.rfc-editor.org/rfc/rfc851.html> · <https://www.rfc-editor.org/rfc/rfc878.html>
- **1973 reference host list.** **RFC 597, "Host Status," N. Neigus & J. Feinler (NIC), 12 December
  1973** — ~60+ hosts, and it explicitly lists the international **NORSAR-TIP** (Norway). Produced by
  the SRI **NIC**, which also shipped the geographic + logical maps. *(UCL London also connected in
  1973 per site histories; confirm against a primary source before stating as fact.)*
  <https://www.rfc-editor.org/rfc/rfc597.txt>
- **Roles.** ARPA/IPTO (funder); **BBN** (built the IMPs, ran the NCC); AT&T (leased lines);
  **SRI-NIC**, host 2 (host table, RFCs, WHOIS); **UCLA** NMC (measurement). *[pin: ARPANET
  Completion Report, BBN Report 4799.]*
- **Line speeds:** 50 kbps standard trunk; 9.6 kbps slow lines; 230.4 kbps later high-speed trunks;
  **SATNET** satellite (~64 kbps, geostationary latency). *[pin: BBN reports; SATNET papers.]*
- **IMP #1** at UCLA, ~end of August / early September 1969 (first message 29 Oct 1969). *[pin:
  ARPANET Completion Report / Kleinrock.]*
- **FEP / front-end** as a period-correct way to put a non-NCP host on the net. *[pin: site
  histories; the community IMP/host tooling above.]*

> Convention: when a fact graduates from `[pin: …]` to pinned, replace the note with the primary
> citation inline (as done above for the flag day, addressing, and the 1973 host list).
