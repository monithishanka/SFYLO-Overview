# 🛡️ Security Architecture, Cryptography & Compliance: SFYLO

<p align="center">
  <a href="https://sfylo.online"><img src="https://img.shields.io/badge/🌐_Live-sfylo.online-C8F31D?style=for-the-badge&labelColor=0A0A0A" alt="Live Platform"/></a>
  <img src="https://img.shields.io/badge/Cryptography-Argon2id%20%7C%20AES--256--GCM-10B981?style=for-the-badge" alt="Cryptography"/>
  <img src="https://img.shields.io/badge/Privacy-Zero--Knowledge%20Companion-0EA5E9?style=for-the-badge" alt="Zero-Knowledge"/>
  <img src="https://img.shields.io/badge/Isolation-Multi--Tenant%20Enforced-8B5CF6?style=for-the-badge" alt="Multi-Tenant"/>
</p>

---

## 🔒 Security Posture Overview

SFYLO treats child safety telemetry as mission-critical private data. The platform implements an institutional defense-in-depth security model engineered around five core protection domains:

```
┌────────────────────────────────────────────────────────────────────────┐
│                      SFYLO DEFENSE-IN-DEPTH MATRIX                     │
├──────────────────────────┬─────────────────────────────────────────────┤
│ 1. Cryptographic Auth    │ Argon2id memory-hard password hashing &     │
│                          │ tamper-evident HMAC-SHA256 JWT sessions.    │
├──────────────────────────┼─────────────────────────────────────────────┤
│ 2. Multi-Tenant Walls    │ Cryptographically scoped queries; no cross- │
│                          │ account device enumeration or telemetry leak│
├──────────────────────────┼─────────────────────────────────────────────┤
│ 3. Zero-Knowledge Phone  │ No parent emails, master passwords, or card │
│                          │ tokens reside on the supervised hardware.   │
├──────────────────────────┼─────────────────────────────────────────────┤
│ 4. Rate-Limiting Defense │ IP and fingerprint throttles preventing     │
│                          │ brute-force password or pairing guessing.   │
├──────────────────────────┼─────────────────────────────────────────────┤
│ 5. Parallel Data Purge   │ Complete, irreversible database purge when  │
│                          │ a parent removes an unlinked device.        │
└──────────────────────────┴─────────────────────────────────────────────┘
```

---

## 🛡️ 1. Multi-Tenant Account Isolation

A critical vulnerability in legacy tracking software is insecure direct object referencing (IDOR), where an attacker modifies a device ID to view another family's coordinates.

SFYLO enforces **mandatory query scoping** across all database interactions:

```
Incoming Request ──> Verify JWT ──> Extract req.user.id
                                          │
    ┌─────────────────────────────────────┴─────────────────────────────────────┐
    ▼                                                                           ▼
Admin Role                                                          Standard Parent Account
Allow Global Telemetry Scope                                        Filter DB Queries by:
                                                                    { $or: [
                                                                        { userId: req.user.id },
                                                                        { deviceId: { $in: user.linkedDevices } }
                                                                    ] }
```

Parent accounts cannot:
- Enumerate devices registered to other parents.
- Dispatch remote commands to unlinked hardware.
- Access location history or keystrokes outside their linked inventory.

---

## 🔐 2. Cryptographic Primitives

| Purpose | Algorithm / Standard | Security Benefit |
|---|---|---|
| **Password Storage** | **Argon2id** | Winner of the Password Hashing Competition; resistant to GPU-accelerated and ASIC brute-force attacks. |
| **Payload Encryption** | **AES-256-GCM** | Authenticated encryption providing both high confidentiality and message integrity validation. |
| **Session Integrity** | **HMAC-SHA256 (JWT)** | Stateless, tamper-resistant authorization tokens with strict client expiration. |
| **Transport Security** | **TLS 1.3 / WSS** | Enforces modern cipher suites with perfect forward secrecy on all web and socket connections. |

---

## 🚪 3. Zero-Knowledge Stance on Target Hardware

A common threat vector in parental monitoring is physical inspection of the child's device. If a lost or stolen phone holds parent credentials, the entire family account could be compromised.

**SFYLO's Design Rules for Target Hardware:**
1. **No Account Credentials**: The companion daemon stores zero parental emails, passwords, or payment tokens.
2. **Device-Specific Device Tokens**: The phone communicates using an ephemeral hardware-bound token issued exclusively during pairing.
3. **No Administrative Console**: The Android application does not provide access to parent account settings, billing records, or siblings' locations.

---

## ⚡ 4. Rate Limiting & Anti-Brute-Force Engines

To defend against automated scanning and password spraying:
- **Authentication Throttling**: Limits failed login attempts per IP address, enforcing temporary backoff penalties.
- **Handshake PIN Protection**: 6-digit pairing codes are protected with rate-limiting barriers, rendering enumeration mathematically impractical within the 15-minute TTL window.
- **WebSocket Connection Throttling**: Limits rapid socket connection recycling to thwart denial-of-service attempts.

---

## 🗑️ 5. Data Privacy & Complete Deletion (Right to be Forgotten)

Parents retain full ownership and sovereign control over all collected telemetry. When a device is removed via the web dashboard:
1. **Atomic Parallel Cleanup**: Device records, GPS trails, keystroke sessions, and audio records are purged across MongoDB collections in parallel.
2. **Socket Termination**: Active socket links for the device are immediately disconnected and invalidated.
3. **Parent Account Unlinking**: The device identifier is pulled from the parent's `linkedDevices` array, unblocking capacity limits immediately.

---

## ⚖️ Ethical & Compliance Standards

SFYLO complies with international digital safety frameworks:
- **Parental Legal Guardianship**: Engineered strictly for legitimate minor child protection by legally recognized parents or legal guardians.
- **No Unauthorized Commercial Surveillance**: The platform is not sold or marketed for unconsented spousal monitoring or corporate espionage.

---

<p align="center">
  <a href="https://sfylo.online"><strong>Explore the Live Platform at sfylo.online →</strong></a>
</p>
