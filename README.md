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
A remote branch office authenticates to head-office ISE across the public Internet. The branch switch builds a mutually authenticated TLS 1.3 tunnel on TCP 2083 to a RadSec proxy in the DMZ, which relays RADIUS to ISE. Includes a Blast-RADIUS demonstration showing why plain UDP RADIUS with MAB can be forged on an untrusted path, and how RadSec shuts it down.
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

- EAP-TLS is shown with TLS 1.2 (RFC 5216). In TLS 1.2 the handshake, including both certificates, is not encrypted, so the switch, the firewall and anyone else on the path can read it, although they cannot alter it or learn the keys. EAP-TLS with TLS 1.3 (RFC 9190) shortens the exchange and encrypts the certificates, but keeps the same roles.
- EAP-TLS fragments are sized by the EAP MTU of the access link, which the NAD sends to ISE as Framed-MTU. Worlds 1-1 and 1-2 use 1300, so RADIUS packets carrying certificates stay under a 1500-byte path MTU. The World 1-2 fragmentation scenario uses 1500 to show what happens otherwise.
- Every RADIUS packet's Authenticator is labelled for what it really is: a random Request Authenticator in an Access-Request, and an MD5 Response Authenticator (over the packet, the request authenticator and the shared secret) in every response. The response authenticator is what Blast-RADIUS forges.
- World 1-2 shows WPA2-Enterprise with CCMP-128; WPA3-Enterprise uses the same steps and makes Protected Management Frames mandatory.
- World 1-3 shows RadSec / RADIUS-over-TLS on TCP 2083 with a TLS 1.3 tunnel, including the proxy's CertificateVerify. RadSec was specified in the experimental RFC 6614; the IETF RADEXT group's replacement, draft-ietf-radext-radiusdtls-bis, is in IESG evaluation as of October 2026. World 1-3 also shows the Blast-RADIUS attack (CVE-2024-3596). The attack is a conceptual illustration of what the weakness allows and how to defend against it, not a working exploit.
- Device configuration is shown in real syntax but trimmed for teaching. Check it against your vendor's documentation and software version before using it.

## Changelog

### October 2026 · accuracy review

**Corrections to what the game teaches**
- **TLS visibility (World 1-1).** The ClientHello tip and quiz question 1 no longer say the switch "cannot read" the TLS handshake. With TLS 1.2 the certificates travel in the clear; what the switch and firewall cannot do is tamper with the handshake or derive the keys.
- **RADIUS Authenticator (all worlds).** Responses now show a Response Authenticator (MD5 with the shared secret) instead of "16 random bytes". In World 1-3 the MAB Access-Request now shows a random Request Authenticator, and the MD5 response authenticator is marked as the part Blast-RADIUS forges.
- **RadSec TLS 1.3 handshake (World 1-3).** The proxy's flight now runs ServerHello, EncryptedExtensions, CertificateRequest, Certificate, CertificateVerify, Finished, so the proxy actually proves it holds its private key. The extra server Finished after the branch's Finished is gone; an optional NewSessionTicket replaces it.
- **Blocked TCP 2083 (World 1-3).** A silently dropped SYN still ends in a timeout. The tip, firewall panel and quiz question 5 now explain the real difference: no TCP connection forms, so no RADIUS is ever sent.
- **Framed-MTU (World 1-1).** The certificate-fragmentation tip now explains that fragmentation follows the EAP MTU (Framed-MTU), not a RADIUS size limit, since RADIUS can carry up to 4,096 bytes. The wired NAD now uses Framed-MTU 1300 (`dot1x mtu 1300`), consistent with the World 1-2 fragmentation lesson.

**Smaller fixes**
- World 1-3 firewall: the proxy → ISE rule now uses the proxy's inside address, 10.60.1.10, and a udp/1813 accounting rule has been added.
- World 1-3 branch config: valid IOS-XE RadSec syntax (`address ipv4`, `key radsec` and `tls port 2083` on separate lines). The branch device is now an access switch, SW-BRANCH-07, to match its configuration and its NAS-Port-Type.
- World 1-3 `radsecproxy.conf`: uses `ListenTLS`, gives the client block a `host`, matches branch certificates on subjectAltName DNS instead of the CN, and adds the `realm *` block needed to forward requests to ISE.
- Untrusted-certificate failures (Worlds 1-1, 1-2 and 1-3) no longer list a server name mismatch alongside the `unknown_ca` alert, which only covers an untrusted issuer.
- World 1-1 NAD config adds `radius-server dead-criteria` and `radius-server deadtime`, which Cisco IOS needs before it will mark a server dead or use critical authentication.
- World 1-2 Access-Accept identifier corrected from 56 to 55.
- World 1-1 Finished step: the two Finished messages *prove* both sides derived the same keys, rather than marking when they first hold them.
- Blast-RADIUS tip and quiz now cite RFC 3579 for the Message-Authenticator requirement with EAP.
- World 1-2 LLC/SNAP header now shows the OUI field (00-00-00).

**Bug fixes**
- The Internet pad no longer shows a padlock and a locked grey pad after World 1-3 is unlocked, and its device panel no longer says "Locked until Episode 1-3" from Worlds 1-1 and 1-2.
- Choosing a scenario now respects the "reduce motion" setting, as choosing a world already did.
- Removed unused duplicate copies of eight functions left over from adding World 1-3.
- Page description, social preview text and map description now mention World 1-3.
