# WIMSE: Verifiable Geofencing & Residency Proofs for Cybersecure Workloads

[![IETF Draft](https://img.shields.io/badge/IETF-Draft-blue.svg)](https://datatracker.ietf.org/doc/draft-lkspa-wimse-verifiable-geo-fence/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

## Overview

This repository contains the IETF Internet-Draft for **Verifiable Geofencing**, a framework within the **Workload Identity in Multi-Service Environments (WIMSE)** working group for modernizing workload security through cryptographically verifiable geofencing, proof-of-possession, and protocol-aware residency enforcement.

By binding workload identity to both geographic and host attributes, and supplementing bearer tokens with verifiable, location- and host-bound claims, the framework addresses the challenges of bearer token theft, proof-of-possession and trust-in-transit for all networking protocols.

## The Problem

Modern cloud and distributed environments face significant risks from stolen bearer tokens, protocol replay, and trust gaps in transit. Current geofencing and location verification solutions face challenges across different data states, location sources, and authentication mechanisms.

## The Solution: Verifiable Geofencing

The framework leverages trusted hardware (TPM), attestation protocols, and geolocation services to cryptographically bind workload identity to both platform and geographic attributes. Key elements include:

- **Trusted Hardware Roots** — TPMs, GNSS sensors, and mobile network modules ensuring device integrity and authentic location data.
- **Remote Attestation** — TPM-backed attestation proving integrity and residency to a remote Workload Identity Manager.
- **Composite Location Claims** — Multiple location sources fused into a quality-scored, cryptographically signed claim.
- **Policy Enforcement** — Geofencing and data residency policies enforced via verifiable claims.
- **Continuous Monitoring** — Periodic re-attestation detecting changes like SIM swaps or sensor removal.

## Related Work

This draft complements and builds upon:

- **[Transitive Attestation](draft-mw-wimse-transitive-attestation-00.md)** — Proof of Residency (PoR) via hardware-rooted Workload Identity Agents.
- **CNCF SPIFFE/SPIRE** — Formalizing the binding for SPIRE's node-to-workload attestation.
- **Confidential Computing Consortium (CCC)** — Grounding residency in TEE execution models.

## Building the Draft

The draft is written in Markdown and uses `mmark` and `xml2rfc` for conversion.

### Prerequisites
- [mmark](https://github.com/mmark-md/mmark)
- [xml2rfc](https://pypi.org/project/xml2rfc/)

### Build Commands
```bash
# Generate TXT, HTML, and XML outputs
make

# Clean build artifacts
make clean
```

## Contributing

This is an active IETF submission. Feedback is welcome via GitHub issues or the [WIMSE mailing list](https://www.ietf.org/mailman/listinfo/wimse).
