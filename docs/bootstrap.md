# The connector bootstrap (design reference)

`arpanet-nic-connect` is the one-command onboarding helper for Raspberry Pi / Debian — the common
platform for replicas and SIMH. It hides WireGuard entirely (see
[transport-and-security.md](transport-and-security.md)); the member's whole job is to run it.

> **Status: design reference — illustrative, not tested, not shipped.** This shows the intended shape
> so we can review the flow. Real packaging (a `.deb`, signing, error handling) comes later.

## What it does

1. Installs `wireguard` if absent.
2. **Generates the WireGuard keypair locally** — the private key never leaves the machine; only the
   public key is sent to the NIC.
3. Submits the join request (or resumes one), receives the assignment bundle
   ([request-flow-and-config.md](request-flow-and-config.md)).
4. Installs `wg0.conf` and enables the `wg-quick@arpanet` service.
5. Writes the `attach -u` / `bps` lines into the member's simulator config.
6. Verifies the tunnel is up and the trunk/host link is live.

## Usage (intended)

```
arpanet-nic-connect --hostname MIT-STYLE-ITS \
                    --location "Columbia, SC" \
                    --machine "PDP-10/ITS" \
                    --line 50k \
                    --sim-config /opt/pidp10/systems/its-arpa51/boot.pidp
```

## Illustrative skeleton (Pi / Debian)

```sh
#!/bin/sh
# arpanet-nic-connect  --  DESIGN REFERENCE, not production.
set -eu

NIC_API="https://nic.arpanet-nic.example/api/v1"
WG_DIR="/etc/wireguard"
IFACE="arpanet"

# 1. dependency
command -v wg >/dev/null 2>&1 || { sudo apt-get update && sudo apt-get install -y wireguard; }

# 2. local keypair  (private key stays on THIS machine)
umask 077
[ -f "$WG_DIR/${IFACE}.key" ] || wg genkey | sudo tee "$WG_DIR/${IFACE}.key" >/dev/null
PUBKEY=$(sudo cat "$WG_DIR/${IFACE}.key" | wg pubkey)

# 3. request assignment  -> returns wg0.conf + attach lines + host-table entry
#    (only the PUBLIC key leaves the box)
BUNDLE=$(curl -fsS -X POST "$NIC_API/join" \
  --data-urlencode "hostname=$HOSTNAME_ARG" \
  --data-urlencode "location=$LOCATION_ARG" \
  --data-urlencode "machine=$MACHINE_ARG" \
  --data-urlencode "line=$LINE_ARG" \
  --data-urlencode "pubkey=$PUBKEY")

# 4. install tunnel  (NIC-supplied conf, with our private key spliced in locally)
echo "$BUNDLE" | extract wg0.conf \
  | sed "s#__PRIVATE_KEY__#$(sudo cat "$WG_DIR/${IFACE}.key")#" \
  | sudo tee "$WG_DIR/${IFACE}.conf" >/dev/null
sudo systemctl enable --now "wg-quick@${IFACE}"

# 5. splice attach -u / bps lines into the sim config (idempotent, backup first)
echo "$BUNDLE" | extract attach-lines | apply_to_sim_config "$SIM_CONFIG_ARG"

# 6. verify
wg show "$IFACE" >/dev/null && echo "tunnel up"
echo "Bring your simulator up; you should appear on the NCC map."
```

`extract`, `apply_to_sim_config`, and the arg parsing are stand-ins for the real implementation. The
private key is spliced into the conf **locally** — the NIC never sees or supplies it.

## macOS / Windows (manual, no script)

1. Install the official **WireGuard** app.
2. In the request flow, generate a keypair (the app can) and submit only the **public** key.
3. **Import** the NIC-supplied `wg0.conf` and activate the tunnel.
4. Paste the supplied `attach -u` / `bps` lines into your simulator config.

## Security notes

- The **private key never leaves the member's machine**; the NIC only ever receives the public key.
- No inbound ports: the tunnel dials out (`PersistentKeepalive`), so dynamic IP / CGNAT are fine.
- Revocation is a one-line change on the hub (remove the peer); see
  [transport-and-security.md](transport-and-security.md).
