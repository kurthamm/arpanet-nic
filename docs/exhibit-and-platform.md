# The Exhibit and the Platform

One website, one shared backbone, **two front doors.** They are not two projects competing — they
are two views of the same network, answering two different questions.

## The two front doors

### Door 1 — the Exhibit ("Experience the ARPANET") — open, no login
- The **1972 map** and **open web terminals** into the historical hosts the operator designates
  (TENEX, Multics, ITS, WAITS…).
- Anyone, instantly, **no account.** This is the museum and the public hook.
- Keeps its own identity (the existing project's public face).

### Door 2 — the Platform ("Join the living ARPANET") — account required
- Its own distinct look, so it *feels* separate (it shares the backbone underneath, but presents as
  the new thing).
- The **live node map/list** (current members, anonymized to city/state, growing), **add your
  equipment**, the connector, the registry.
- **Account (GitHub OAuth) required to join and add.**

## The access / reachability model

Access is **asymmetric** — this is what makes the open door safe.

| Actor | Can reach |
|---|---|
| **Anonymous Exhibit web-terminal** (no login) | **Exhibit hosts only** — can `@L` freely *among* Exhibit computers, but cannot reach the member network. |
| **Member host** (account, added equipment) | **All member hosts + all Exhibit hosts.** |

Rules that follow:
- **The Exhibit is a contained zone.** Members can reach *into* it; anonymous sessions cannot reach
  *out* of it — and cannot jump *through* an Exhibit host into the member net. The Exhibit is
  reachable, never a launch point.
- **Member-to-member is fully open**, exactly like the real ARPANET: any member host is reachable by
  any other member. The only gate is the **host's own login**, not the network. *("If you don't like
  that, don't join.")*
- **The public may KNOW the living network exists** and read about it (and see the anonymized live
  map as information), but **cannot *do* anything with it without being a member who has added
  equipment.**

## The two maps (not redundant)
- **1972 map = history.** Static, curated, "what the ARPANET *was*." Lives in the Exhibit.
- **Live map = now.** Dynamic, "who's connected *right now*," anonymized (city/state). Lives in the
  Platform.
Each is labeled by its purpose; they reinforce, they don't compete. The Exhibit's historical hosts
are the founding/backbone nodes of the living network, so the two doors cross-link:
Exhibit → "this is a living network, join it"; live map → "these founding nodes are the real sites,
try them in the Exhibit."

## The Exhibit host set — curated and changeable
- The Exhibit is **not a fixed set** — the operator curates it and may **add more historically
  accurate hosts** over time.
- The operator can **move their own nodes between the Exhibit and the member network** — the member
  side makes a good **testing ground** (dogfooding) before promoting a node into the Exhibit.

## The web terminal is one TIP node
The open web terminal is **one Exhibit TIP** (Terminal IMP) — historically accurate, with its own
number, visible on the map — serving **many concurrent anonymous sessions** (not one TIP per
visitor). It is a real node on the backbone, scoped so its reachability is the Exhibit only.

> **Note — this is a deliberate, scoped exception to "the site never reaches into hosts."** The site
> reaches into hosts *only* through the Exhibit TIP, *only* to Exhibit hosts, for anonymous visitors.
> Everything on the member side remains host-to-host.

## Future idea (deferred, not now)
Give members an account option to **add their own node to the Exhibit group** (so the public can
reach it too). Technically involved — parked as a future enhancement.
