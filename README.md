# NetSec World

A playable 16-bit overworld for learning network security, one world at a time. You follow real packets across a pixel-art map — EAPOL, RADIUS, TLS and more — and click any device or moving packet to zoom in and inspect its actual fields. Each world is a self-contained lesson that unlocks the next.

**▶ [Play it here](https://quantumbitx.github.io/netsec-world/)**

## What's inside

Every world shares the same toolkit:

- **Animated packet flow** that travels along the map's roads between devices, colour-coded by protocol layer.
- **Click-to-zoom inspector** — click any device or the moving packet to see the full encapsulation decoded field by field: headers, RADIUS attributes, EAP and TLS records, flags and certificates.
- **Real configuration views** for each device: switch and access-point config, firewall rules, ISE policy, and `radsecproxy.conf`.
- **Multiple scenarios per world** — a happy path plus the common failures, each one replayable so you can see the symptom and the cause.
- **A message ladder** of the whole exchange, and a **six-question quiz**.
- **Playback controls:** play/pause, step forward/back, ¼–2× speed, follow-packet and zoom.

## The worlds

### World 1-1 · Wired 802.1X with EAP-TLS
A laptop gets onto a wired network: EAPOL to the switch, RADIUS across the routed network and firewall, and a full mutual TLS handshake with Cisco ISE, ending in a VLAN assignment.
*Scenarios: happy path · expired client certificate · untrusted server certificate · firewall blocking UDP 1812 · shared-secret mismatch.*

### World 1-2 · Wi-Fi: EAP-TLS and the 4-way handshake (Desert RF World)
The same laptop goes wireless. Beacons and association, EAP-TLS over the air through the access point, then the Access-Accept hands the AP its PMK and the 4-way handshake derives the encryption keys.
*Scenarios: happy path · untrusted server certificate (evil twin) · firewall dropping fragmented RADIUS.*

### World 1-3 · RadSec across the Internet (Dino World DMZ)
A remote branch office authenticates to head-office ISE across the public Internet. RadSec builds a mutually authenticated TLS tunnel on TCP 2083 to a proxy in the DMZ, which relays RADIUS to ISE. Includes a Blast-RADIUS demonstration showing why plain UDP RADIUS with MAB can be forged on an untrusted path, and how RadSec shuts it down.
*Scenarios: happy path · Blast-RADIUS forgery · proxy certificate rejected · firewall blocking TCP 2083.*

## Unlocking

Each world unlocks when you finish any journey in the one before it — World 1-2 after 1-1, World 1-3 after 1-2 — with a short celebration. Your browser remembers what you've unlocked.

| World | Topic | Status |
|---|---|---|
| 1-1 | Wired 802.1X with EAP-TLS | Available |
| 1-2 | Wi-Fi: EAP-TLS and the 4-way handshake | Available |
| 1-3 | RadSec and the Blast-RADIUS attack | Available |
| 1-4 | ??? from the lake | Coming soon |
| 1-5 | Cloud Access World | Coming soon |

## Run it locally

A single self-contained `index.html` — no build step and no dependencies (fonts load from Google Fonts, and it falls back to system fonts offline). Open it in any modern browser.

## Accuracy

Addresses, names and identifiers throughout are illustrative examples.

- EAP-TLS is shown with TLS 1.2 (RFC 5216); EAP-TLS with TLS 1.3 (RFC 9190) shortens the exchange but keeps the same roles.
- World 1-2 shows WPA2-Enterprise with CCMP-128; WPA3-Enterprise uses the same steps and makes Protected Management Frames mandatory.
- World 1-3 shows RadSec / RADIUS-over-TLS (RFC 6614, being revised by the IETF RADEXT group) on TCP 2083, and the Blast-RADIUS attack (CVE-2024-3596). The attack is a conceptual illustration of what the weakness allows and how to defend against it, not a working exploit.
