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

## B. Connect — *(site: NIC + backbone)*

**Connection is decided per *host*, not per member.** Every host independently makes three choices,
and one account can hold **any mix** of hosts wired different ways — there is no rule that a member
picks one shape.

**The three per-host axes:**
1. **Which IMP** — one of **our** hub IMPs, or the member's **own local** IMP.
2. **NCP or FEP** — the host speaks NCP natively, or it needs a FEP (front-end).
3. **If FEP, whose** — a FEP **we** manage, or the member's **own local** FEP.

A "site" is just a collection of hosts under one account, each at its own point in that space. The
per-host variants (all valid, all mixable within one account):
4. **Native host → our hub IMP.**
5. **Native host → my own IMP** (which trunks to the backbone).
6. **Non-NCP host → our managed FEP → our hub IMP.**
7. **Non-NCP host → my own FEP → our hub IMP.**
8. **Non-NCP host → my own FEP → my own IMP.**
9. **Host other members on my IMP** — spare host ports carry other members' hosts (I become a
   sub-hub). Those guest hosts are registered under *their* accounts; the NIC still does the
   assignment.

> Example mix (one account): my local IMP carrying my NCP hosts, **plus** one non-NCP host on our
> managed FEP. Both at once. — A FEP plugs into an IMP's host port, so a FEP sits with the IMP it
> feeds (our FEP → our hub; your FEP → your IMP or our hub over the tunnel): that's about where the
> box lives, not a limit on what you can run.

### Onboarding lens (website only, not a constraint)
A friendly "how much do you want to run?" framing over the axes above:
*just a host* (we provide the IMP + FEP as needed) · *host + your own FEP* · *your own IMP* (and you
can host others). Members can mix beyond whatever lens they picked.

### The invariant that makes any mix safe
No matter how a host is wired, one rule holds and is the single control point:
> **Every host is registered, and the backbone passes traffic only from a registered `(IMP, host)`
> pair.** Register → the host reaches the net; unregistered → blocked at the choke point (confined to
> its own local IMP). Registration is the access grant; revocation removes it. This is why we can
> allow unlimited combinations and still control every host — including hosts behind a member's own
> IMP. (Requires inter-node traffic to route through the backbone hubs — see peer-to-peer routing.)

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
10. **Troubleshoot** — my node is down or won't connect. **Push-button "Diagnose my connection":**
    because the tunnel, trunk line, and IMP routing all terminate on our side, we read our own view
    and tell the member which layer is broken (tunnel → line → host-ready → NCP) and how to fix it —
    they send us nothing. A member-facing slice of the NCC (E.21). See
    [diagnostics.md](diagnostics.md).
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
21. **View network status** — the NCC map: who's up, link health. Same data feeds the per-member
    diagnostics (D.10) and answers "is the node I'm trying to reach up?" — remote IMPs via routing,
    hosts on our IMPs via host-ready, remote hosts via active probe. See [diagnostics.md](diagnostics.md).

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
