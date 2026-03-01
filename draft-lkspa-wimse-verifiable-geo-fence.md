%%%
title = "Privacy Preserving Verifiable Geofencing with Residency Proofs for Sovereign Workloads"
abbrev = "RATS-GEO-POR"
category = "info"
docName = "draft-lkspa-wimse-verifiable-geo-fence-04"
ipr = "trust200902"
area = "Security"
workgroup = "RATS"
keyword = ["geofencing", "attestation", "workload identity", "residency", "TPM", "GNSS"]

[seriesInfo]
name = "Internet-Draft"
value = "draft-lkspa-wimse-verifiable-geo-fence-04"
stream = "IETF"
status = "informational"

[[author]]
initials = "R."
surname = "Krishnan"
fullname = "Ram Krishnan"
organization = "JPMorgan Chase & Co."
  [author.address]
  email = "ramkri123@gmail.com"

[[author]]
initials = "N."
surname = "Smith"
fullname = "Ned Smith"
organization = "Intel"
  [author.address]
  email = "ned.smith@intel.com"

[[author]]
initials = "D."
surname = "Lopez"
fullname = "Diego R. Lopez"
organization = "Telefonica"
  [author.address]
  email = "diego.r.lopez@telefonica.com"

[[author]]
initials = "A."
surname = "Prasad"
fullname = "A Prasad"
organization = "Oracle"
  [author.address]
  email = "a.prasad@oracle.com"

[[author]]
initials = "S."
surname = "Addepalli"
fullname = "Srinivasa Addepalli"
organization = "Aryaka"
  [author.address]
  email = "srinivasa.addepalli@aryaka.com"

[[contributor]]
initials = "B."
surname = "Malepati"
fullname = "Bala Siva Sai Akhil Malepati"
organization = "Independent"
  [contributor.address]
  email = "saiakhil2012@yahoo.com"

[[contributor]]
initials = "G."
surname = "Arfaoui"
fullname = "Ghada Arfaoui"
organization = "Orange"
  [contributor.address]
  email = "ghada.arfaoui@orange.com"

[[contributor]]
initials = "M."
surname = "Epley"
fullname = "Michael Epley"
organization = "Red Hat"
  [contributor.address]
  email = "mepley@redhat.com"

[[contributor]]
initials = "V."
surname = "Masilamani"
fullname = "Vijay Masilamani"
organization = "Independent"
  [contributor.address]
  email = "saanvijay20@gmail.com"

%%%

.# Abstract

Modern cloud and distributed computing rely heavily on software-only identities and bearer tokens that are easily stolen, replayed, or used from unauthorized locations. Furthermore, traditional methods of location verification - such as IP-address-based geolocation - are easily spoofed via VPNs or proxies and significantly compromises infrastructure security and privacy for **Sovereign Workloads** and high-assurance environments. This document defines a **High-Assurance Profile** designed to solve these challenges through hardware-rooted cryptographic verifiability. 

A host machine runs a workload identity agent for managing the workload identities on that platform. This proposal replaces implicit trust and spoofable indicators with cryptographically verifiable hardware-rooted evidence of integrity and location for this agent. Critically, this framework prioritizes **Location Privacy** by utilizing Zero-Knowledge Proofs (ZKP), allowing a workload to prove it is within a compliant "Sovereign Zone" without disclosing precise coordinates that could be used for tracking or exploitation.

By binding software identities to persistent silicon identities and verified physical residency, this solution establishes a "Silicon-to-Workload" chain of trust. It ensures that sensitive operations are only performed by authorized workloads running on untampered hardware in cryptographically verified, privacy-preserving geographic boundaries, fulfilling the high-assurance requirements of the **WIMSE Architecture** [[I-D.ietf-wimse-architecture]].

{mainmatter}

# Introduction

The **Workload Identity Agent** (e.g., SPIRE Agent) acts as the local-on-host intermediary responsible for managing and issuing identities to workloads. It serves as a "vetting" mechanism, ensuring that a workload's execution environment meets required security and residency policies before granting it the cryptographic credentials necessary for network communication. This **High-Assurance Profile** (a specialized RATS Profile) provides the technical mechanics to cryptographically bind this agent to the underlying hardware-verified platform and its privacy preserving physical location.

The architecture follows the **RATS Architecture [[RFC9334]]**, defining the interactions between **Provers**, **Verifiers**, and **Relying Parties** to generate and validate **High-Confidence Evidence** regarding the **Workload Identity Agent's** status. It provides the hardware-rooted "Evidence Layer" required by the high-level **WIMSE Architecture [[I-D.ietf-wimse-architecture]]**, establishing a **"Silicon-to-Workload"** chain of trust that ensures sensitive data is only processed by authorized workloads in approved, integral environments.

To maintain **Location Privacy** while providing cryptographic verifiability, this profile leverages **Transparent Zero-Knowledge Proofs (ZKPs)**. Unlike traditional ZKP systems, transparent ZKPs require no "Trusted Third Party" or complex "Trusted Setup" phase. They achieved mathematical transparency through non-interactive, hash-based protocols, allowing a platform to prove it is resident within an approved geographic boundary without disclosing the exact coordinates of the underlying hardware.

# Conventions and Definitions

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in BCP 14 [[RFC2119]] [[RFC8174]] when, and only when, they appear in all capitals, as shown here.



## Abbreviations

- **AK**: Attestation Key
- **BMC**: Baseboard Management Controller
- **DAA**: Direct Anonymous Attestation
- **EAT**: Entity Attestation Token
- **EK**: Endorsement Key
- **GNSS**: Global Navigation Satellite System
- **IMA**: Integrity Measurement Architecture
- **IMEI**: International Mobile Equipment Identity
- **IMSI**: International Mobile Subscriber Identity
- **LAH**: Location Anchor Host
- **OOB**: Out-of-Band
- **PCR**: Platform Configuration Register
- **PoR**: Proof of Residency
- **SPDM**: Security Protocol and Data Model
- **STARK**: Scalable Transparent ARgument of Knowledge
- **SVID**: SPIFFE Verifiable Identity Document
- **TEE**: Trusted Execution Environment
- **TPM**: Trusted Platform Module
- **V-GAP**: Verifiable Geofencing Attestation Profile
- **ZKP**: Zero-Knowledge Proof

# Key Terms

Data Residency:
: Requirement that data processing and storage remain within an approved geographic boundary.

Geofencing:
: Enforcement that workloads execute only on approved hosts within an approved geographic boundary.

Workload Identity Agent:
: On-host component that issues workload identities (for example, SVIDs) to local workloads, subject to verifier-approved evidence.

Location Anchor Host (LAH):
: Trusted host or device that produces location evidence used to establish residency within a geofence.

Workload Host:
: Physical or virtual machine running the Workload Identity Agent and workloads; produces platform (outer) evidence. For now, we assume the Workload Host is the same as the Location Anchor Host.

Composite Geolocation:
: Location estimate fused from multiple sources and accompanied by a quality indicator.

Proof of Residency (PoR) / Co-location:
: Evidence that binds a workload (or Workload Host) to an approved local environment and geofence for a specific attestation interval.

Silicon Root of Trust:
: Hardware trust anchor that supports measured boot and protects attestation keys.

Transparent Zero-Knowledge Proof:
: ZKP that does not require a trusted setup; used to prove "inside an approved zone" without revealing precise coordinates.

Workload Identity Management Plane:
: Issues and validates workload identities and trust bundles based on verifier results and policy.

Host Identity Management Plane:
: Verifies platform integrity and residency evidence, and manages attestation key registration and platform health state (often via OOB paths).

V-GAP (Verifiable Geofencing Attestation Profile):
: Nested evidence format defined in this document for binding identity to verified platform integrity and verified residency.

N_platform (Platform Quote Nonce):
: Fresh nonce used for platform attestation. For OOB, this is delivered via a path that does not require the Workload Host OS to observe it.

N_fusion (Workload Fusion Nonce):
: Fresh nonce used to bind identity issuance of the workload identity agent to a specific attestation interval.

# Use Cases

This profile supports attested data residency and geofencing for workloads and (optionally) users. Common use cases fall into: server-centric enforcement, user-centric enforcement, and compliance and risk reduction.
## Server-centric Enforcement

Enterprises need cryptographic proof that workloads run only on approved hosts within an approved geographic boundary, and that data flows only between approved boundaries.


Workload-to-workload (general): Relying parties accept workload identities only when the issuing host attests platform integrity and “in-zone” residency, preventing credentials from being used outside the approved boundary.

Agentic AI workloads: An AI agent may access sensitive data or perform sensitive actions only when its Workload Identity Agent presents hardware-rooted integrity evidence and a verifiable “in-zone” proof (optionally privacy-preserving), binding identity to both platform state and residency.

Federated / edge AI (key or model release): High-value artifacts (e.g., decryption keys or model weights in federated learning) are released only when the partner/edge host attests it is integral and resident within the required boundary. This is useful for intermittently connected sites.

User-to-server: Clients validate that the server endpoint is operating within an approved boundary (e.g., by policy tied to the server’s attested identity and residency evidence).


## User-centric enforcement
Enterprises may also need trustworthy location signals for user-facing access decisions.
Geofenced access control: User access is permitted only when the user (or user device) proves it is within an allowed boundary, ideally without requiring precise location disclosure.


On-premises boundaries: Customer-premises equipment can define an enterprise boundary, with a network or enterprise infrastructure providing supporting evidence for policy enforcement.


Restricted support geographies: Administrative or support actions can be allowed only when the operator proves presence within allowed geographies, reducing policy and insider-risk exposure.


## Compliance and risk reduction
Geofence attestation provides audit-ready evidence to support data residency and sovereignty controls, and it can also reduce non-compliance risk from misconfiguration or spoofable signals. Even when not mandated, “in-zone” proofs help address: configuration drift, edge relocation/proxying, contractual residency requirements, and location-privacy minimization (proving “inside the zone” without storing coordinates).

# Motivation and Gaps

Operators need to enforce *where* sensitive workloads run without relying on signals that are easy to spoof (IP geolocation, region labels) or credentials that are easy to steal (bearer tokens). In many systems today, platform integrity and residency are inferred from configuration and control-plane metadata rather than verified with cryptographic evidence.

Key gaps include:

- **Unverifiable location metadata (data-at-rest / data-generation):** Location tags for arbitrary data objects are not standardized and are typically unsigned, making provenance and integrity difficult to validate.
- **Token theft and replay (data-in-use):** Bearer tokens can be copied and replayed from unauthorized hosts or locations; stronger mechanisms exist but are not consistently deployed and can add operational overhead.
- **Implicit trust in “region” and transit:** A relying party often cannot cryptographically verify a server’s physical residency, and requests may traverse intermediaries (e.g., proxies) that expand the effective trust boundary.

# What This Profile Provides

This document defines a High-Assurance Profile (a specialized RATS profile) that makes **platform integrity** and **geofence residency** verifiable inputs to authorization and credential issuance, while supporting privacy-preserving “in-zone” proofs where available.

At a high level, the profile enables a relying party (or identity issuer) to require evidence that:
1. the Workload Identity Agent is running on an approved, measured platform; and
2. that platform is resident within an approved geographic boundary (optionally without revealing coordinates).

# Composition with Transitive Attestation and WIMSE

This profile is designed to compose with [[I-D.mw-wimse-transitive-attestation]] and the **WIMSE Architecture** [[I-D.ietf-wimse-architecture]].

- **[[I-D.mw-wimse-transitive-attestation]] (Layer 1):** Binds a workload to a *local* Workload Identity Agent (co-location / PoR), treating the agent as a trust anchor.
- **This document (Layers 2 and 3):** Defines how that Workload Identity Agent is itself verified:
  - **Layer 2 — Platform integrity:** Hardware-rooted evidence of the host state (e.g., TPM-based attestation).
  - **Layer 3 — Residency:** Cryptographically verifiable proof the attested host is inside an approved boundary (optionally privacy-preserving).

| Layer | Document | Responsibility |
| :--- | :--- | :--- |
| **Layer 1** | [[I-D.mw-wimse-transitive-attestation]] | Bind workload to a local Workload Identity Agent (co-location / PoR). |
| **Layer 2** | This document | Verify Workload Host integrity for the Workload Identity Agent (platform evidence). |
| **Layer 3** | This document | Verify Workload Host residency within an approved boundary (location evidence). |

# Operational Use: Gating Credentials on Verified Evidence

This profile assumes two cooperating control planes:

- **Host Identity Management Plane:** Verifies platform integrity and residency evidence and produces an attestation result.
- **Workload Identity Management Plane:** Issues or renews workload identities (e.g., SVIDs) only when the attestation result satisfies policy.

To prevent mix-and-match and replay, attestation results SHOULD be fresh and SHOULD be bound to the identity issuance event (e.g., by cryptographically binding freshness values used for platform quotes and workload credential issuance within the verifier result).

Where policy requires it, the verifier can additionally require that an agent software measurement (e.g., image digest) is covered by validated platform evidence, reducing the risk that a modified or unauthorized agent obtains credentials.

In intermittently connected edge deployments, local operation can continue during outages, while centralized policy can be enforced on renewal and on release of high-value secrets once connectivity is available.

# Deployment Patterns (Informative)

This profile supports multiple ways to collect and verify evidence for Layers 2 and 3:

- **In-band host attestation:** Evidence collected by host software (e.g., Keylime-style deployments).
- **Out-of-band management:** Evidence collected via a management controller / BMC path (e.g., HPE iLO-class OOB management).
- **Cloud-hosted attestation environments:** Provider mechanisms that expose measured boot / TPM-backed claims (e.g., Nitro-class or shielded-instance approaches).

# High-Assurance Profile - Verifiable Geofencing Attestation Profile (V-GAP)

V-GAP is a RATS/WIMSE attestation profile that binds a **Workload Identity Agent** to (1) hardware-rooted host integrity and (2) verified residency within a configured geofence. It does this with a **evidence bundle**  from a **Location Anchor Host (LAH)**.

## Evidence Model

V-GAP evidence is a cryptographic bundle:

- **Location Anchor Host (LAH) bundle (`lah-bundle`)**: LAH identity, location proof (or hash), freshness, and an LAH seal.

## Encoding and Canonicalization

- All binary values (hashes, public keys, signatures) **MUST** be Base64URL encoded.
- When hashing JSON objects, implementations **MUST** use the JSON Canonicalization Scheme (JCS) [[RFC8785]].
- The seals are TPM quotes:
  - `inner-seal` MUST be a TPM quote whose signed `qualifyingData` includes `SHA-256(JCS(lah-bundle-without-inner-seal))`.
  - `outer-seal` MUST be a TPM quote whose signed `qualifyingData` includes `SHA-256(JCS(v-gap-profile-without-outer-seal))`.
  - The Workload Host quote MUST cover the PCRs needed to validate the agent measurement claim (e.g., PCRs that include the agent image digest measurement).

The seals bind the canonicalized bundle contents to the TPM quote via qualifyingData, so any modification or swapping of fields is detectable by the verifier.

## Field Summary

- `host-tpm-ak`: Workload Host AK public key.
- `agent-image-digest`: Digest of the Workload Identity Agent image/config being asserted.
- `host-proximity-proof-hash`: Hash of deployment-specific proximity evidence showing the Workload Host is co-located with the LAH. For Unified Hosts (LAH == Workload Host), set to `SHA-256("V-GAP-LOCAL-BUS-PROXIMITY")`.
- `lah-bundle`: LAH evidence and seal.
- `n_platform`: Freshness value from the Host Identity Management Plane (for the LAH evidence).
- `n_fusion`: Freshness value from the Workload Identity Management Plane (to bind evidence to identity issuance/renewal).
- `timestamp`: Unix timestamp (informative; freshness is primarily provided by nonces).

## Formal Data Structure (JSON Schema)

The schema below is informative; the normative requirements are in the text above.

```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "V-GAP Nested Evidence Bundle",
  "type": "object",
  "properties": {
    "host-tpm-ak": { "type": "string" },
    "host-proximity-proof-hash": { "type": "string" },
    "agent-image-digest": { "type": "string" },
    "lah-bundle": {
      "type": "object",
      "properties": {
        "lah-tpm-ak": { "type": "string" },
        "lah-geolocation-proof-hash": { "type": "string" },
        "privacy-technique": { "type": "integer", "enum": [0, 1] },
        "n_platform": { "type": "string" },
        "timestamp": { "type": "integer" },
        "inner-seal": { "type": "string" }
      },
      "required": [
        "lah-tpm-ak",
        "lah-geolocation-proof-hash",
        "privacy-technique",
        "n_platform",
        "timestamp",
        "inner-seal"
      ]
    },
    "n_fusion": { "type": "string" },
    "timestamp": { "type": "integer" },
    "outer-seal": { "type": "string" }
  },
  "required": [
    "host-tpm-ak",
    "host-proximity-proof-hash",
    "agent-image-digest",
    "lah-bundle",
    "n_fusion",
    "timestamp",
    "outer-seal"
  ]
}
```

## Evidence Production (Informative)

- The Host Identity Management Plane provides `n_platform` to the LAH (directly or via an OOB path).
- The LAH produces `lah-bundle` and `inner-seal`.
- The Workload Host assembles the outer bundle, including the full `lah-bundle`, and produces `outer-seal`.
- The Workload Identity Management Plane provides `n_fusion` and evaluates the verifier result before issuing or renewing identities.

---

## Verification (Informative)

A verifier (or relying party) validates:

- `inner-seal` against `lah-tpm-ak` and freshness (`n_platform`, timestamp policy).
- `outer-seal` against `host-tpm-ak` and freshness (`n_fusion`, timestamp policy).
- That the Workload Host quote covers the PCRs needed to support the asserted `agent-image-digest`.
- That `host-proximity-proof-hash` and `lah-geolocation-proof-hash` satisfy domain policy (e.g., allowlist, thresholding, or other policy mechanisms).
- That the outer seal staples the inner bundle (by verifying the `qualifyingData` binding to the canonicalized outer bundle).

---

## Scalable Fleet Management

Large deployments need lifecycle management for the attestation keys referenced by V-GAP (e.g., `host-tpm-ak` and `lah-tpm-ak`) and for the policies that authorize them.

---

## Key Registry and Synchronization

- A Cloud (central) Host Identity Management Plane maintains a registry of accepted AK public keys and associated metadata (e.g., EK certificate chain, hardware identity, and status).
- An Edge Host Identity Management Plane **MAY** maintain a local registry to support disconnected operation and periodically synchronizes updates to the central registry.

---

## Key Rotation

To prevent rogue key injection during rotation:

- The central registry **MUST** accept a new AK only if the edge plane provides a rotation proof that chains the new AK to previously accepted state.
- A rotation proof **MUST** be a JCS-canonicalized object signed by the previously accepted AK (or, if available, validated by a fresh hardware-rooted OOB quote).

### Example Rotation Proof (Informative)

```json
{
 "new-ak-pub": "Base64URL_Encoded_Public_Key",
 "serial-number": "AK_Serial_XYZ",
 "timestamp": 1708845600,
 "hardware-uuid": "Host_Hardware_UUID",
 "signature": "Base64URL_Signature_from_Previous_AK"
}
```

## Credential Activation and Re-Verification

Credential activation (e.g., `TPM2_MakeCredential`) is expensive to run on every request. Verifiers **SHOULD** perform it on events such as:

- Initial onboarding  
- Reboot / reset detection (e.g., TPM clock/reset counters)  
- Policy violations or drift signals (e.g., firmware or inventory changes)  
- Failure of proximity or location evidence checks  
- Explicit elevation to higher assurance policy  

Between full activations, verifiers **MAY** accept fresh quotes from registered AKs as proof of continued compliance, subject to policy.

---

## Revocation and Health Signals

- The edge plane **SHOULD** maintain a per-node health signal (e.g., tamper, firmware policy violations).
- On severe health signals, the verifier **MUST** revoke the relevant AK(s) and reject identities derived from them according to policy.

---

## Disconnected Operation (Leased Identity)

For intermittent connectivity, the verifier **MAY** issue identities with extended validity (a lease) under policy. If a lease is used:

- The edge plane **MUST** revoke or refuse renewal locally on tamper/drift signals.
- The workload **MUST** re-attest and satisfy current policy on reconnection before renewal or release of high-value secrets.

# Verification Procedure

This section defines the required verification checks for a V-GAP evidence bundle.

A verifier (or a relying party acting on verifier output) MUST:

1. **Parse evidence**: Extract the V-GAP bundle (embedded or referenced) and identify the `host-tpm-ak`, `lah-tpm-ak`, `outer-seal`, `inner-seal`, and associated nonces/timestamps.
2. **Verify LAH seal**: Verify `inner-seal` using `lah-tpm-ak` and confirm freshness per verifier policy (for example, `n_platform` and/or timestamp window).
3. **Verify Workload Host seal**: Verify `outer-seal` using `host-tpm-ak` and confirm freshness per verifier policy (for example, `n_fusion` and/or timestamp window).
4. **Verify stapling**: Confirm the outer seal cryptographically binds (staples) the exact `lah-bundle` included in the evidence so that location evidence cannot be swapped.
5. **Verify agent measurement**: Confirm the `agent-image-digest` is consistent with the PCR(s) quoted by the Workload Host, per the platform profile (for example, PCRs populated by measured boot and/or IMA). If this check fails, the verifier MUST reject the bundle.
6. **Verify proximity binding**: Evaluate `host-proximity-proof-hash` according to the deployment policy:
   - For **Unified Host** deployments (LAH == Workload Host), `host-proximity-proof-hash` MUST equal `SHA-256("V-GAP-LOCAL-BUS-PROXIMITY")`.
   - For non-unified deployments, the verifier MUST validate the hash against policy (for example, allowlist membership, expected peer pairing, and freshness binding).
7. **Verify residency**: Validate `lah-geolocation-proof-hash` according to policy:
   - If `privacy-technique == 1`, the verifier MUST validate the ZKP (or a verifier-approved proof result) and confirm it is bound to the current attestation session (freshness values and the geolocation hash).
   - If `privacy-technique == 0`, the verifier MUST validate the location evidence (or its hash) and confirm the host is inside the configured boundary.
8. **Produce an attestation result**: Output a signed result containing the decision (pass/fail), the verified claims (integrity, residency, and relevant identifiers), and the freshness window used. This result is the input to identity issuance and downstream authorization.

Freshness: Verifiers MUST use nonces and MUST enforce a bounded acceptance window. If freshness checks fail, the verifier SHOULD require a re-attestation and MAY revoke previously accepted results according to policy.

# Evidence Sources and Attestation Mechanics

This section summarizes how platforms produce integrity and residency evidence. It is primarily informative; normative requirements are stated where needed for interoperability.

## Platform Integrity Evidence (Layer 2)

Platform integrity evidence binds the Workload Identity Agent to an approved host state.

At minimum, a conforming implementation MUST support:

- **Hardware-rooted attestation**: Evidence anchored in a hardware root of trust (for example, TPM-based attestation keys and quotes).
- **Measured state**: A verifier policy that checks measurements for the boot chain and for the Workload Identity Agent (for example, IMA measurements or equivalent).
- **Agent binding**: The `outer-seal` MUST quote PCRs sufficient to evaluate the asserted `agent-image-digest` claim.

## Residency Evidence (Layer 3)

Residency evidence asserts that the attested host is within an approved geographic boundary.

- The LAH provides `lah-bundle` containing a geolocation proof (or hash), freshness, and `inner-seal`.
- Location sources and corroboration are deployment-specific (for example, GNSS, modem/network-assisted location, or other trusted sources). This document standardizes the evidence interface and verification checks, not the sensor protocol details.

## Privacy-Preserving Residency (ZKP)

Where supported, V-GAP MAY use `privacy-technique == 1` and carry a ZKP-derived hash.

A ZKP-based design MUST ensure:

- The verifier learns only the compliance outcome (inside/outside the boundary), not coordinates.
- The proof is **session-bound** (freshness values and the geolocation hash).
- The proof is conveyed efficiently (for example, referenced by hash/URI rather than embedded in size-constrained credentials).

## Deployment Patterns (Informative)

Implementations commonly fall into these patterns:

- **In-band host attestation**: Evidence collected by host software (for example, Keylime-style deployments).
- **Out-of-band management**: Evidence collected via a management controller/BMC path (for example, iLO-class OOB management).
- **Cloud-hosted attestation environments**: Provider mechanisms exposing measured boot and TPM-backed claims (for example, Nitro-class or shielded-instance approaches, including OCI-style shielded instances).

# Operational Considerations

## Distributed Identity Issuance and Scaling

To support edge deployments and intermittent connectivity, identity issuance may be distributed within a sovereign boundary.

- **Edge issuance**: Workload identities (for example, SVIDs) MAY be issued by an issuer deployed within the same boundary as the workloads.
- **Scoping**: Issued identities SHOULD be scoped so they are not accepted outside the intended deployment boundary (for example, via trust bundle partitioning and policy).
- **Renewal gating**: Issuers SHOULD renew short-lived identities only when the verifier result for integrity and residency is valid for the requested freshness window.

## Mobility and Sovereign Handover (Informative)

When a workload moves between anchors or boundaries, the Workload Identity Agent MUST obtain a new V-GAP bundle that reflects the new LAH and current residency.

Verifiers SHOULD treat this as a normal re-attestation event:
- platform integrity continuity can remain stable, but
- proximity and residency checks MUST be re-evaluated for the new anchor/boundary.

## Location Anchor Hosts (Informative)

To scale location sensing, a deployment may use dedicated anchors:

- **End-user anchors**: A user device (for example, a phone) can serve as an LAH for a nearby client device using a proximity mechanism (for example, BLE/UWB). The proximity mechanism is out of scope; only the resulting proximity evidence hash is standardized.
- **Data center anchors**: A small set of hosts can act as LAHs for a cluster. Proximity measurement mechanisms may vary; if a PTP-derived mechanism is used, its protocol details should be profiled separately (see [[I-D.ramki-ptp-hardware-rooted-attestation]]).

# Policy Use (Informative)

Relying parties and identity issuers can use V-GAP results as inputs to authorization.

- **ABAC**: Residency and integrity can be mandatory claims for sensitive operations.
- **KMS gatekeeping**: Release of high-value assets (for example, decryption keys) SHOULD depend on a recent successful verification result.
- **Fail closed**: If V-GAP evidence is carried in an X.509 extension and marked CRITICAL, any implementation that does not understand the extension will reject the credential.

# Security Considerations

V-GAP reduces reliance on spoofable location signals and stolen tokens by making integrity and residency verifiable. Implementers still need to address:

- **Replay and mix-and-match**: Use nonces and stapling so old location evidence cannot be combined with a fresh platform quote (or vice versa).
- **Location spoofing**: Treat sensor and network inputs as adversarial. Prefer multiple sources or corroboration where feasible, and apply conservative policy when evidence quality degrades.
- **Relay and displacement**: Proximity mechanisms can be relayed or anchors can be moved. Mitigate with tight proximity windows where possible, anchor health checks, and disagreement policies when multiple anchors are observed.
- **Management plane compromise**: OOB paths reduce dependence on the host OS but introduce dependence on the management controller and its network. Protect this plane with secure boot/updates, strong authentication, segmentation, and audit logging.
- **Time and freshness**: Verifiers MUST enforce bounded freshness windows and define recovery behavior (re-attestation, quarantine, revocation) when clocks drift or evidence is stale.
- **Registry and allowlist integrity**: Protect key registries and policy stores against tampering; treat them as high-value assets.
- **Privacy**: Avoid unnecessary collection or retention of precise location. Prefer “in-zone” proofs (ZKP) where policy permits.

## Proximity and Anchor Displacement (Informative)

Proximity-based designs (for example, phone-as-anchor or ranging-based methods) are vulnerable to relay and displacement. Mitigations include:
- tight RTT-based acceptance windows where applicable,
- requiring anchor attestation and health checks, and
- policy that treats inconsistent anchor observations as unverified.

# IANA Considerations

IANA is requested to register the following Object Identifier (OID) in the "SMI Numbers" registry under the "SMI Private Enterprise Numbers" (1.3.6.1.4.1) branch, or as appropriate for the V-GAP profile.

**Mandatory Criticality:** Implementations of this profile MUST mark the X.509 extension containing the V-GAP Evidence Bundle as **CRITICAL**. This ensures that non-compliant gateways fail closed rather than granting access to residency-constrained workloads.

- **OID**: `1.3.6.1.4.1.60265.1.1`
- **Description**: Verifiable Geofencing Attestation Profile (V-GAP) Evidence Bundle
- **Reference**: This document.

# Appendix - Items to Follow Up

## OPEN ISSUE: IMA restart behavior

Define an interoperable way to detect and handle Workload Identity Agent restarts without requiring a host reboot, while preserving measurement integrity.

## OPEN ISSUE: Location privacy options

Clarify the set of supported privacy techniques and how policy selects between precise location, coarse location, and ZKP-based “in-zone” proofs.

## OPEN ISSUE: Proximity profiles

Specify one or more proximity evidence profiles (for example, PTP-derived, BLE/UWB-derived, or other mechanisms) as separate documents, while keeping V-GAP evidence fields stable.

## OPEN ISSUE: Geotagging textual data

There no widely deployed standard for geotagging arbitrary textual data objects.

## OPEN ISSUE: Attesting geotags

There is no widely deployed standard for signing geotags to prevent manipulation.

# Appendix - Public References for Strict Data Residency Rules

India -- Reserve Bank of India (RBI): Payment System Data Localization (2018): From RBI Circular RBI/2017-18/153 (April 6, 2018): "All system providers shall ensure that the entire data relating to payment systems operated by them are stored in a system only in India. This data should include the full end-to-end transaction details / information collected / carried / processed as part of the message / payment instruction."

South Korea's Data Localization Regulations -- Geospatial Information Management Act (Spatial Data Act): Article 16, Paragraph 1: Prohibits the export of state-led survey data.

{backmatter}
