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

# 7. Threat Actors

## 7.1 Overview

A threat model is only meaningful if it considers realistic adversaries.

Many security documents begin by assuming sophisticated attackers with extensive technical capabilities.

While those adversaries certainly exist, they are not the primary concern for Magnet Memories.

The purpose of this application is to preserve family memories on a locally owned Android tablet.

Consequently, the most probable threats arise not from determined attackers, but from ordinary events such as accidental mistakes, damaged media, interrupted imports, lost hardware, or malformed external input.

This chapter defines the actors considered throughout the remainder of the threat model.

Each actor is characterised by:

* motivation;
* capability;
* opportunity;
* likely attack surface.

Threats are evaluated based on realistic assumptions rather than worst-case speculation.

---

# 7.2 Threat Actor Classification

Threat actors are grouped into six categories.

```text id="2rq5vr"
              Threat Actors
                     │
 ┌──────────┬─────────┼──────────┬──────────┬──────────┐
 ▼          ▼         ▼          ▼          ▼          ▼
Owner   Curious    External   Malicious  Hardware   Software
Mistakes Person     Media       Input      Failure    Failure
```

Not every threat actor is malicious.

Some represent environmental conditions rather than people.

The architecture is intentionally designed to handle both.

---

# 7.3 Threat Actor A — The Owner

## Description

The owner is the primary user of Magnet Memories.

The owner acts with good intentions.

However, like any human, they may accidentally perform actions with unintended consequences.

Examples include:

* importing the wrong folder;
* deleting a Journey unintentionally;
* disconnecting the external drive during import;
* overwriting an NFC tag;
* renaming folders unexpectedly;
* restoring the wrong backup.

These are considered **accidental threats**, not malicious behaviour.

---

## Motivation

The owner wishes to preserve memories.

There is no adversarial intent.

---

## Capability

The owner has legitimate access to:

* the tablet;
* NFC tags;
* the external archive;
* Workshop functions;
* Journey management.

---

## Architectural Response

The application should reduce the consequences of accidental actions through:

* confirmation dialogs;
* immutable archives;
* preview screens;
* validation;
* backups;
* deterministic imports;
* recoverable workflows.

The goal is not to prevent owner actions but to make mistakes easy to recover from.

---

# 7.4 Threat Actor B — Curious Person

## Description

A curious person has temporary physical access to the tablet or an NFC magnet but no specialised technical expertise.

Examples include:

* friends;
* relatives;
* visitors;
* children.

They may:

* scan NFC magnets;
* explore the application;
* browse available Journeys.

Their curiosity is not necessarily malicious.

---

## Motivation

Examples include:

* curiosity;
* exploration;
* accidental interaction.

---

## Capability

Limited.

The actor typically lacks:

* debugging tools;
* developer knowledge;
* system-level access.

---

## Architectural Response

The application should:

* avoid exposing unnecessary information through NFC tags;
* minimise retained personal metadata;
* avoid displaying internal diagnostics during normal operation;
* ensure NFC tags reveal only Journey identifiers.

Future versions may optionally provide Workshop protection if desired.

---

# 7.5 Threat Actor C — External Media

## Description

External storage should always be considered an untrusted participant.

Examples include:

* USB drives;
* external hard disks;
* SD cards;
* exported folders;
* copied Journey archives.

Even media created by the owner may become corrupted.

The application should therefore treat every imported file as potentially unsafe.

---

## Motivation

None.

External media is not malicious.

Risk arises from:

* corruption;
* incomplete copies;
* damaged filesystems;
* unsupported formats;
* interrupted transfers.

---

## Capability

External media can supply:

* malformed files;
* unexpected directory structures;
* damaged metadata;
* incompatible codecs;
* oversized media.

---

## Architectural Response

Every imported object must pass through:

* discovery;
* validation;
* metadata extraction;
* verification;
* controlled processing.

No imported object becomes trusted immediately.

---

# 7.6 Threat Actor D — Malicious Input

## Description

Although Magnet Memories is designed for personal use, it should still assume that deliberately malformed inputs may eventually appear.

Examples include:

* crafted HEIC files;
* corrupted MOV files;
* malformed JSON;
* invalid NFC payloads;
* malicious deep links;
* unexpected filenames.

The application should not rely on good intentions.

---

## Motivation

Possible motivations include:

* causing crashes;
* corrupting Journeys;
* testing application robustness;
* triggering undefined behaviour.

---

## Capability

Potential capabilities include:

* malformed metadata;
* oversized dimensions;
* invalid URIs;
* path traversal attempts;
* unsupported encodings.

---

## Architectural Response

The application validates every external object before it crosses a trust boundary.

Malformed inputs should result in:

* rejection;
* graceful degradation;
* owner notification where appropriate.

They should never compromise the integrity of existing Journeys.

---

# 7.7 Threat Actor E — Hardware Failure

## Description

Hardware failure is treated as a first-class threat actor.

Examples include:

* storage corruption;
* failing flash memory;
* damaged NFC tags;
* USB cable failures;
* sudden power loss;
* battery exhaustion.

These failures are statistically more likely than deliberate attacks for most families.

---

## Motivation

None.

Hardware failures occur naturally over time.

---

## Capability

Examples include:

* interrupted writes;
* unreadable media;
* disappearing storage;
* incomplete imports;
* damaged playback files.

---

## Architectural Response

The architecture addresses hardware failure through:

* immutable archives;
* temporary workspaces;
* verification before commit;
* backup support;
* integrity checking;
* deterministic reconstruction.

---

# 7.8 Threat Actor F — Software Failure

## Description

Software itself can become a source of threats.

Examples include:

* implementation bugs;
* migration failures;
* library regressions;
* codec incompatibilities;
* interrupted updates.

Although these are not attacks in the traditional sense, they may threaten the integrity of family memories.

---

## Motivation

None.

Failures arise unintentionally.

---

## Capability

Examples include:

* incorrect imports;
* database inconsistency;
* decoding failures;
* thumbnail corruption;
* playback regressions.

---

## Architectural Response

The application mitigates software failures through:

* extensive validation;
* automated testing;
* deterministic imports;
* versioned database migrations;
* Architecture Decision Records;
* clear recovery procedures.

---

# 7.9 Threat Actor G — Future Contributors

## Description

Open-source contributors represent a unique class of threat actor.

The overwhelming majority contribute with good intentions.

However, architectural misunderstandings may unintentionally introduce vulnerabilities.

Examples include:

* bypassing validation;
* storing additional metadata;
* weakening NFC validation;
* introducing unnecessary permissions.

---

## Motivation

Positive.

The contributor wishes to improve the project.

Risk arises from misunderstanding rather than malicious intent.

---

## Architectural Response

The repository itself acts as a security control.

Comprehensive documentation—including the Security & Privacy Architecture, this Threat Model, ADRs, and development guidelines—helps contributors understand *why* the architecture exists before changing it.

Security documentation is therefore considered part of the system's defensive strategy.

---

# 7.10 Threat Actor H — Future Features

## Description

New functionality can introduce new attack surfaces.

Examples include:

* cloud synchronisation;
* desktop applications;
* encrypted exports;
* family sharing;
* collaborative journals.

Features are therefore treated as potential future threat actors until they are formally analysed.

---

## Architectural Response

Every significant feature proposal should include:

* updated trust boundaries;
* updated threat model;
* updated security decisions.

No feature should be accepted solely because it appears useful.

Security implications should be considered from the outset.

---

# 7.11 Threat Actor Summary

| Threat Actor     | Motivation            | Capability               | Primary Risk        |
| ---------------- | --------------------- | ------------------------ | ------------------- |
| Owner            | Good intentions       | Full application access  | Accidental mistakes |
| Curious Person   | Curiosity             | Limited physical access  | Privacy exposure    |
| External Media   | None                  | Supplies untrusted files | Malformed input     |
| Malicious Input  | Deliberate disruption | Crafted data             | Validation failures |
| Hardware Failure | None                  | Storage interruption     | Data loss           |
| Software Failure | None                  | Bugs and regressions     | Integrity loss      |
| Contributors     | Improvement           | Source code changes      | Architectural drift |
| Future Features  | New capabilities      | Expanded attack surface  | New vulnerabilities |

---

# 7.12 Security Prioritisation

Threat actors are not equally probable.

Version 1 prioritises protection in the following order:

1. Owner mistakes
2. Hardware failure
3. External media corruption
4. Software defects
5. Malicious input
6. Curious physical access
7. Future architectural drift
8. Highly sophisticated attackers

This prioritisation reflects the application's intended deployment as a locally owned family memory journal.

Security resources should therefore be invested where they provide the greatest practical benefit.

---

## Closing Statement

Threat modelling is not about imagining the most powerful possible attacker.

It is about understanding the **most realistic** ways in which valuable assets could be lost, corrupted or exposed.

For Magnet Memories, the greatest risks are rarely sophisticated cyberattacks.

They are ordinary events:

a failing hard drive,

a damaged Live Photo,

an interrupted import,

an accidental deletion,

or a software bug that silently changes a family's history.

The architecture has therefore been designed to treat these everyday failures with the same seriousness that many systems reserve for external attackers.

The following chapters analyse these risks systematically using the STRIDE threat modelling framework.

