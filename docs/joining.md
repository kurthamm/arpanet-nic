# Joining the network

Two ways on, both through the IMPs (see [charter.md](charter.md)).

## Front door: attach a host to our IMP

For members who have a host (replica or emulator) and don't want to run an IMP. This is the common
case, and it's the most NAT-friendly (you only ever dial out).

- The NIC assigns a **host number on the nearest hub with a free host port** (by real geography).
- Historically this is a **Very Distant Host (VDH)** — a host reached over a long leased line rather
  than co-located. So remote host-attach is a real period configuration, not a workaround, and it
  gets a [line grade](telecom.md).

## Advanced: bring your own IMP

For members who want to run an IMP (Oscar's H316 replica or the h316 emulator) that others can attach
to.

- The NIC assigns an **IMP number** (from the 5–63 pool) and a **trunk assignment**: which existing
  IMP(s) to trunk to (nearest hub + a neighbor, to satisfy the ≥2-trunk redundancy rule).
- Requires the ability to run the WireGuard tunnel (packaged — see
  [transport-and-security.md](transport-and-security.md)).

## Does the requester pick their IMP?

**Default: no — the NIC assigns by geography** (nearest hub / best trunk peers), which keeps
[geographic continuity](network-topology.md) and is easier. **An override is allowed** for advanced
users who want a specific hub or peer, validated against capacity.

## What we collect

**Host connection:**
- Contact (name, email) — for the registry/WHOIS and to reach you.
- Approximate location (city/region) — for hub assignment and the map. Coarse is fine.
- Machine + OS (e.g. "PDP-11 / Unix v6") — for the host table and to know native-NCP vs FEP.
- Desired hostname — subject to NIC uniqueness/approval.
- Your **public key** — the credential.
- Desired [line grade](telecom.md) — default 50 kbps.
- **Not your IP** — never needed; you dial out.

**IMP connection:** all of the above, plus your IMP software (replica vs h316 emulator) and trunk
preference (auto/nearest or specific).

## The request flow (target design)

1. Fill out the request form on the website (the fields above).
2. Generate your keypair locally (the bootstrap does this; the private key never leaves your machine)
   and submit the **public** key.
3. The NIC assigns your number(s), picks trunk/attach peers by geography + redundancy, sets your line
   grade, and returns a **copy-paste bundle**:
   - your ready-to-use WireGuard `wg0.conf`,
   - your assigned IMP/host number and hub/trunk target,
   - your `attach -u` line(s) with the stable tunnel address and correct `BPS`,
   - your host-table (`HOSTS.TXT`) entry.
4. Run the bootstrap (Pi/Linux) or import the conf (Mac/Windows). You're on the ARPANET, and you
   appear on the NCC map.

The NIC does the assignment and config generation; the member copies, pastes, and runs.
