# NetSec World

A 16-bit overworld for learning network security, one world at a time. **World 1-1** covers wired 802.1X with EAP-TLS. Follow one laptop's packets across a pixel-art map, from the switch port to Cisco ISE, and click anything to zoom in and inspect it.

**▶ [Play it here](https://QuantumBitX.github.io/netsec-world/)**

## What's inside

- **Animated packet flow** from the supplicant through the NAD, router and firewall to the RADIUS server in DC ISE World, colour-coded by layer: EAPOL, RADIUS and TLS.
- **Click-to-zoom inspector** for every message, showing the full encapsulation on both sides of the NAD: Ethernet, EAPOL, EAP and TLS on the access port, and IP/UDP, RADIUS attributes, EAP and TLS across the network.
- **Certificate checks** for the server and client certificates, and device views with example switch config, firewall rules and ISE policy.
- **Five scenarios:** the happy path, an expired client certificate, an untrusted server certificate, a firewall blocking UDP 1812, and a shared secret mismatch.
- **Message ladder** of the whole conversation, plus a six-question quiz.

## Episodes

| World | Topic | Status |
|---|---|---|
| 1-1 | Wired 802.1X with EAP-TLS | Available |
| 1-2 | Wi-Fi: EAP-TLS and the 4-way handshake (Desert RF World) | Coming soon |
| 1-3 | RadSec and cloud RADIUS (Dino World DMZ) | Coming soon |
| 1-4 | ??? from the lake | Coming soon |
| 1-5 | Cloud Access World | Coming soon |

## Run it locally

It's a single self-contained `index.html` with no build step. Open it in any modern browser.

## Notes

Addresses, names and identifiers are illustrative examples. The handshake shown is EAP-TLS with TLS 1.2 (RFC 5216). EAP-TLS with TLS 1.3 (RFC 9190) shortens the exchange but keeps the same roles.
