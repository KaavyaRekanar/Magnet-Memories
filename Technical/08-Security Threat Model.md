# Security Threat Model

**Project:** Magnet Memories
**Document:** Security Threat Model
**Version:** 1.0
**Status:** Draft

---

# 1. Purpose

## 1.1 Overview

This document defines the formal security threat model for **Magnet Memories**.

Its purpose is to identify realistic threats to the application, evaluate their potential impact, and describe the architectural mechanisms used to reduce or eliminate those risks.

Unlike the Security and Privacy Architecture document, which explains **how the system is designed**, this document examines **how the system might fail or be misused**.

It therefore serves as the primary reference for:

* security reviews;
* architecture validation;
* implementation decisions;
* penetration testing;
* future feature planning.

Threat modelling is an ongoing engineering activity rather than a one-time design exercise.

As the application evolves, this document should evolve with it.

---

## 1.2 Goals

The objectives of this document are to:

* identify the assets requiring protection;
* identify realistic threat actors;
* document the assumptions under which the system operates;
* analyse threats using a structured methodology;
* describe existing mitigations;
* identify residual risks;
* guide future security improvements.

The document is intended to support engineering decisions rather than provide compliance documentation.

---

## 1.3 Relationship to Other Documents

This document builds upon several earlier architectural specifications.

In particular:

* **Security and Privacy Architecture** defines the security philosophy, principles, protected assets and trust boundaries.
* **Import Pipeline** describes the secure media import workflow.
* **NFC Specification** defines how physical Journey identifiers are programmed and validated.
* **Storage and Backup** defines long-term storage and recovery mechanisms.

Rather than repeating those specifications, this document evaluates them from the perspective of an attacker.

---

# 2. Scope

The threat model applies to Version 1 of Magnet Memories.

It includes:

* NFC interactions;
* media import;
* Journey playback;
* local storage;
* backup and restore workflows;
* metadata handling;
* application components;
* Android platform interactions.

The model assumes ordinary consumer hardware running a supported version of Android.

---

## 2.1 Out of Scope

The following are intentionally excluded from Version 1.

* Cloud infrastructure
* Multi-user authentication
* Remote APIs
* Online synchronisation
* Payment systems
* Account management
* Enterprise deployment
* Hardware attacks against secure elements
* Nation-state adversaries
* Compromised Android kernels

Future versions may expand the threat model if these capabilities are introduced.

---

# 3. Relationship to the Security Architecture

The Security and Privacy Architecture document answers four fundamental questions:

* What do we value?
* What principles guide the design?
* What assets are protected?
* Where are the trust boundaries?

This document begins where the architecture document ends.

It asks:

* Who might attack the system?
* Which assets are attractive targets?
* How could those attacks occur?
* What architectural protections already exist?
* What residual risks remain?

Every threat identified in this document should map back to one or more protected assets and trust boundaries defined in the Security Architecture.

This ensures that the threat model remains grounded in the system's actual design rather than becoming a generic security checklist.

---

# 4. Threat Modelling Methodology

Magnet Memories adopts a structured threat modelling approach based primarily on **STRIDE**.

STRIDE classifies threats into six categories:

* **Spoofing** — pretending to be something trusted.
* **Tampering** — modifying data or system state without authorisation.
* **Repudiation** — denying actions or preventing accountability.
* **Information Disclosure** — exposing information to unintended parties.
* **Denial of Service** — preventing normal operation.
* **Elevation of Privilege** — gaining capabilities beyond intended permissions.

STRIDE is used because it provides systematic coverage of the application's trust boundaries while remaining understandable to contributors who may not specialise in security engineering.

Where appropriate, threats are also evaluated in terms of:

* affected assets;
* affected trust boundaries;
* likelihood;
* impact;
* existing mitigations;
* residual risk.

The objective is not to eliminate every conceivable threat, but to understand and manage the risks that are realistic for a local-first family memory application.

---

# 5. Security Objectives

Every identified threat is evaluated against the project's core security objectives.

The primary objectives are:

1. Preserve the integrity of family memories.
2. Protect owner-authored stories and Journey structure.
3. Minimise unnecessary disclosure of personal information.
4. Ensure Journeys remain available without cloud services.
5. Prevent accidental corruption during import and playback.
6. Maintain owner control over all data.
7. Support long-term recoverability.

These objectives provide the basis for assessing whether a threat represents an acceptable risk or requires additional mitigation.

---

# 6. System Overview

From a security perspective, Magnet Memories consists of five major subsystems.

```text
                Owner
                  │
                  ▼
         Android Operating System
                  │
                  ▼
      ┌───────────────────────────┐
      │   Magnet Memories App     │
      │                           │
      │ UI                        │
      │ NFC                       │
      │ Import Pipeline           │
      │ Media Pipeline            │
      │ Playback Engine           │
      │ Room Database             │
      └─────────────┬─────────────┘
                    │
      ┌─────────────┴─────────────┐
      ▼                           ▼
External Sources          Local Journal
```

Threats are analysed at the boundaries between these components rather than inside individual implementation classes.

This keeps the model stable even as the implementation evolves.

---

## Closing Statement

A useful threat model is neither exhaustive nor alarmist.

It is a practical engineering tool that helps developers ask the right questions before writing code.

The following chapters identify the realistic actors, assumptions and threats relevant to Magnet Memories, providing the foundation for the detailed STRIDE analysis that follows.
