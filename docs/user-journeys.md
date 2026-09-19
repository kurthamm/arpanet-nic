# User journeys

The complete inventory of who does what on the platform.

> **Status: planning.** This is the master list; individual journeys will be detailed one at a time.

## The dividing principle

Our platform does exactly two things: **help you get connected** (the NIC / coordination side) and
**let you watch the network** (the NCC status side).

**Everything you actually *do* on the ARPANET is host-to-host, from your own host, over the IMPs — it
is the network itself, not a feature of this site.** There is nothing on the website that reaches
into hosts. This is really the ARPANET (at least to start).

So the journeys split into: things the **site** mediates (A, B, D, E, F below) and things that happen
**on the network between hosts** (C), which we list only for completeness.

---

## A. Join / coordinate — *(site: NIC)*
1. **Discover & decide** — learn what the network is, confirm my machine qualifies, pick a tier.
2. **Register** — create my member identity/account (the *person*, separate from any node key).
   *(Open: do we require accounts, and how is the person authenticated vs. the node's key?)*
3. **Request a connection** — submit what I run + location + line grade; receive my assignment
   (number, hub, trunk peers) and config bundle.
   *(Open: provisioning is automatic vs. operator-approved — see F.29.)*

## B. Connect — the three tiers of involvement — *(site: NIC + backbone)*
The member picks **how much they want to run**; we fill in the rest.

**Tier 1 — I just run a host** (we provide the IMP, and the FEP if the host can't speak NCP):
4. **Native-NCP host → our IMP** *(case 1a)*.
5. **Non-NCP host → we host the FEP → our IMP** *(case 1c — FEP-as-a-service, confirmed offered)*.

**Tier 2 — I run my host + its FEP:**
6. **Non-NCP host → my own FEP → our IMP** *(case 1b)*.

**Tier 3 — I run my own IMP:**
7. **Native-NCP host → my IMP → trunk into the backbone** *(case 2a)*.
8. **Non-NCP host → my FEP → my IMP → trunk into the backbone** *(case 2b)*.
9. **Host other members on my IMP** — become a sub-hub *(sub-hub)*.

> A **multi-host site** (one operator, several hosts) is just Tier 1/2/3 with more than one host —
> not a separate journey. **Native-NCP vs. FEP** is a property of each host, not a connection type.

## C. On the network — *the ARPANET itself, host-to-host, NOT the site*
Once connected, done entirely from the member's own host over the IMPs. The site provides none of
this — it is inherent to the network and works exactly as it did in 1972:
- log into another host (`@L`)
- offer my host as a destination others can log into
- query the NIC host for directory/WHOIS/Finger and to fetch RFCs
- host-to-host email
- file transfer (FTP)

Listed for completeness only; these are not platform features.

## D. Change / lifecycle — *(site: NIC)*
10. **Troubleshoot** — my node is down or won't connect.
11. **Move location** — relocate on the map, possibly re-trunk to a nearer hub.
12. **Swap machine or OS** — update my host-table entry.
13. **Change line grade.**
14. **Add another host** — grow into a multi-host site.
15. **Change tier** — e.g., start Tier 1, later stand up my own IMP.
16. **Rotate / replace a lost key.**
17. **Go temporarily offline** — vacation; keep my number during a grace period.
18. **Report another node** — flag abuse or a broken peer.
19. **Transfer ownership** — hand my node/number to another person *(edge case)*.
20. **Deregister** — leave for good; free my IMP/host number, drop from the host table and map.

## E. Observe — *(site: NCC)*
21. **View network status** — the NCC map: who's up, link health.

## F. Operator (you) — *(site: NIC + NCC)*
22. **Provision requests** — automatic or eyeball-and-approve.
23. **Assign & reclaim numbers; publish the host table.**
24. **Run & monitor the backbone** — NCC.
25. **Operate the we-hosted FEPs** — the Tier-1c service.
26. **Handle a bad actor** — detect → warn → throttle → revoke → appeal.
27. **Grow the backbone** — add/retire a hub as regions fill.
28. **Manage the address ceiling** — the eventual new-leader decision.
29. **Back up / restore** the registry and hub state.
30. **Resolve disputes** — hostname/number conflicts.

---

## Parked (not launch journeys)
- **Visitor with no hardware / public TIP.** To use the network you need a host — the site is not a
  way in. Revisit later if we ever want a hardware-less on-ramp.

## Open decisions surfaced by these journeys
- **Member identity/accounts** — required? person-auth vs. node-key (A.2).
- **Provisioning** — automatic vs. operator-approved (A.3 / F.22).
- **FEP-as-a-service (1c)** — confirmed offered; operational model to define (B.5 / F.25).
- **Number lifecycle** — offline grace period + reclamation policy (D.17 / D.20 / F.23).
