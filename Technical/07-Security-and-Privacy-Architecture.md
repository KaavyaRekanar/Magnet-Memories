# Security and Privacy Architecture

**Project:** Magnet Memories
**Document:** Security and Privacy Architecture
**Version:** 1.0
**Status:** Draft

---

# 1. Purpose

## 1.1 Overview

This document defines the security and privacy architecture of **Magnet Memories**.

Unlike many software systems where security is primarily concerned with financial transactions or personal identity, Magnet Memories protects something fundamentally different:

> **Family memories.**

Photographs, stories, places, conversations and shared experiences often become more valuable with time. While they may not be financially sensitive, they are emotionally irreplaceable.

The purpose of this document is to explain **how the system protects those memories**, **why specific architectural decisions were made**, and **which security principles guide every implementation decision**.

This document serves as the primary architectural reference for all security- and privacy-related decisions throughout the project.

It is intended for:

* contributors;
* maintainers;
* future developers;
* security reviewers;
* researchers;
* AI coding assistants working on the codebase.

Rather than documenting isolated implementation details, this document explains the reasoning behind the system's design so that future changes continue to respect the original architectural intent.

---

## 1.2 Objectives

The security architecture has five primary objectives.

### Preserve Memories

Imported journeys, photographs and owner-written stories should remain available and recoverable for many years.

Security mechanisms should support long-term preservation rather than short-term convenience.

---

### Protect Privacy

The application should minimise the collection, storage and exposure of personal information.

Whenever possible, information should simply never be collected.

The safest personal information is information that never exists.

---

### Maintain Trust

Owners should always understand:

* where their memories are stored;
* what the application is doing;
* what information leaves the device;
* what information is retained.

The application should never surprise the owner.

---

### Remain Dependable Offline

Journey playback should never depend on:

* cloud services;
* internet connectivity;
* remote authentication;
* third-party APIs.

Once imported, every journey should remain fully usable without any external dependency.

---

### Enable Future Evolution

Security should not prevent future capabilities.

The architecture should support future additions such as:

* encrypted backups;
* cloud synchronisation;
* family sharing;
* multiple tablets;
* desktop companions.

These future capabilities should extend the architecture rather than replace it.

---

## 1.3 Intended Audience

This document assumes familiarity with software architecture but does not require specialist knowledge of Android internals.

It is written for:

### Application Developers

Developers implementing new features should understand the security constraints that govern the architecture.

---

### Contributors

Open-source contributors should understand why certain design decisions exist before proposing alternatives.

---

### Security Reviewers

Researchers and reviewers should be able to evaluate the project's threat model and implementation strategy.

---

### AI Coding Assistants

Large language models and automated coding systems should use this document as the authoritative reference when making implementation decisions.

Whenever implementation choices conflict with convenience, this document takes precedence.

---

# 2. Scope

## 2.1 In Scope

This document covers every architectural decision related to security and privacy within Magnet Memories Version 1.

Topics include:

* trust boundaries;
* threat modelling;
* NFC security;
* media import security;
* storage security;
* metadata handling;
* offline privacy;
* permission management;
* backup considerations;
* recovery;
* future security evolution.

---

## 2.2 Out of Scope

The following topics are intentionally documented elsewhere.

| Topic            | Document              |
| ---------------- | --------------------- |
| Media processing | Media and Live Photos |
| Import workflow  | Import Pipeline       |
| Database schema  | Data Model            |
| Storage layout   | Storage and Backup    |
| User experience  | UX Principles         |
| Screen behaviour | Screen Specification  |

This document references those specifications where appropriate but does not duplicate them.

---

## 2.3 Architectural Position

Security is treated as an architectural concern rather than an implementation detail.

Individual classes, methods or Android APIs may change over time.

The underlying security principles should not.

This document therefore focuses on:

* why decisions exist;
* trust assumptions;
* system boundaries;
* long-term design.

Implementation details belong in source code and Architecture Decision Records (ADRs).

---

# 3. Security Goals

Magnet Memories does not attempt to solve every security problem.

Instead, it defines a clear set of goals appropriate for a local-first family memory application.

Every technical decision should contribute toward one or more of these goals.

---

## Goal 1 — Preserve the Integrity of Family Memories

The system should prevent accidental corruption or loss of imported journeys.

This includes:

* photographs;
* Live Photos;
* Memory Cards;
* Journey ordering;
* owner-authored stories;
* playback metadata.

Integrity is considered more important than implementation simplicity.

---

## Goal 2 — Protect the Owner's Privacy

The application should retain only the information required to deliver its intended experience.

Features should not require unnecessary personal information.

Where possible:

* do not collect;
* do not retain;
* do not transmit.

Privacy should emerge naturally from architectural decisions rather than from optional settings.

---

## Goal 3 — Keep the Owner in Control

The owner should remain the ultimate authority over their memories.

The application should never:

* upload media automatically;
* delete journeys automatically;
* rewrite external archives;
* share data without explicit consent.

Ownership always belongs to the family.

---

## Goal 4 — Fail Safely

Unexpected situations should never result in silent data loss.

Examples include:

* interrupted imports;
* storage exhaustion;
* damaged media;
* corrupted databases;
* unexpected shutdowns.

The preferred outcome is graceful degradation rather than irreversible failure.

---

## Goal 5 — Minimise Trust

Every additional dependency introduces additional trust.

Magnet Memories therefore attempts to minimise trust placed in:

* cloud providers;
* external servers;
* online services;
* third-party infrastructure.

The system should continue functioning even when those services do not exist.

---

## Goal 6 — Remain Transparent

Security mechanisms should be understandable.

Owners should never need to wonder:

* Where are my photographs?
* Why is the app accessing storage?
* Why is the internet required?
* Why was something deleted?

The architecture should make answers obvious.

---

## Goal 7 — Build for Longevity

Family memories often outlive software platforms.

Architectural choices should therefore favour:

* portability;
* documented formats;
* recoverability;
* explicit data models;
* stable identifiers.

The goal is not merely to build an application, but to build something that can still be understood and recovered many years from now.

---

# 4. Non-Goals

Understanding what the system intentionally does **not** protect is equally important.

Attempting to solve every possible security problem often produces unnecessary complexity.

Magnet Memories deliberately limits its scope.

---

## 4.1 Not a Secure Vault

Magnet Memories is not intended to function as a digital safe.

It is not designed to store:

* government secrets;
* classified material;
* corporate intellectual property;
* financial records;
* highly regulated personal data.

The application protects ordinary family memories using strong engineering practices, but it is not intended to satisfy specialised security certification standards.

---

## 4.2 Not an Enterprise Security Product

The application is not intended to defend against highly resourced adversaries capable of:

* compromising the operating system;
* extracting hardware-backed secrets;
* physically modifying the device;
* exploiting zero-day vulnerabilities.

The project focuses on protecting memories against realistic risks encountered by ordinary families.

---

## 4.3 Not an Identity Platform

Version 1 intentionally avoids:

* user accounts;
* authentication servers;
* identity providers;
* password management;
* account recovery workflows.

The absence of an account system significantly reduces the application's attack surface.

---

## 4.4 Not a Cloud Service

Version 1 does not provide:

* cloud storage;
* cloud synchronisation;
* remote APIs;
* web dashboards;
* browser access.

Future versions may introduce optional cloud capabilities.

Those features must integrate with, rather than replace, the local-first architecture.

---

## 4.5 Not a Digital Rights Management System

Magnet Memories does not attempt to:

* prevent screenshots;
* watermark photographs;
* restrict exports;
* prevent copying of authorised media.

Once the owner imports their own memories, they retain ownership.

The application should not interfere with legitimate personal use.

---

## 4.6 Not a Forensic Archive

The application is designed to preserve experiences, not legal evidence.

Media processing may include:

* orientation normalisation;
* resizing;
* compatible transcoding;
* metadata reduction.

The original archive remains the authoritative source for preservation.

The tablet stores an optimised viewing copy.

---

## 4.7 Not an Anti-Theft Solution

If the physical tablet is stolen, Version 1 relies primarily on Android's existing device security.

The application does not attempt to implement:

* custom encryption layers;
* anti-tamper mechanisms;
* remote wipe;
* location tracking.

Future versions may support optional application-level protection.

These capabilities are intentionally outside the scope of Version 1.

---

## 4.8 Not a General File Manager

The application manages only its own journal.

It is not intended to browse, organise or modify arbitrary files stored on the device.

This narrow focus significantly reduces both complexity and security risk.

---

## Closing Statement

The boundaries defined above are intentional.

By resisting the temptation to become a cloud platform, social network, media manager or enterprise security product, Magnet Memories can remain focused on its true purpose:

> **Quietly protecting family memories while allowing them to be enjoyed for decades without unnecessary complexity or unnecessary trust.**
---

# 5. Security Philosophy

## 5.1 Introduction

Every software system reflects a set of assumptions about the world.

Some systems are designed around efficiency.

Others prioritise scalability, automation or availability.

Magnet Memories begins from a different assumption:

> **The most valuable things stored by this application are not files—they are memories.**

A photograph is more than an image.

It captures a place that may never be visited again.

A child's first holiday.

A conversation now impossible to recreate.

A parent who is no longer alive.

A handwritten Memory Card explaining why that particular day mattered.

Unlike most digital assets, these memories often become **more valuable with time**.

Twenty years from now, a photograph stored today may become one of the few remaining records of an entire chapter of a family's life.

Because of this, Magnet Memories approaches security differently from many conventional software systems.

Rather than asking:

> *"How do we protect data?"*

The project asks:

> **"How do we preserve memories without asking families to place unnecessary trust in technology?"**

Every architectural decision described throughout this repository originates from that single question.

---

## 5.2 Trust Must Be Earned

Many modern applications ask users to trust them immediately.

They request permissions they do not yet need.

They upload personal data before users understand why.

They rely on cloud infrastructure that owners cannot inspect.

They silently synchronise information across devices.

They collect analytics "just in case."

Over time these behaviours have become common enough that many users no longer notice them.

Magnet Memories deliberately chooses a different path.

Trust should never be assumed.

It should be earned through predictable behaviour.

The application should be understandable enough that an owner can answer simple questions with confidence:

* Where are my photographs stored?
* Why did the application access storage?
* Does anything leave my tablet?
* What happens if I disconnect the hard drive?
* What happens if there is no internet?
* Can I recover everything years from now?

Good security reduces uncertainty.

The application should behave in ways that are easy to explain because the simplest systems are usually the easiest to trust.

---

## 5.3 Local First

The most important architectural decision in Magnet Memories is that **every Journey becomes completely independent after import.**

Once imported, a Journey should never require:

* an internet connection;
* a cloud account;
* a remote API;
* an external database;
* a vendor-operated service.

The tablet becomes a self-contained journal.

This decision provides several benefits simultaneously.

### Reliability

Journeys remain available:

* during travel;
* on airplanes;
* in remote locations;
* years after a cloud provider changes its services.

### Privacy

Memories remain inside the owner's home unless the owner explicitly chooses otherwise.

### Longevity

Future changes to online services cannot make existing Journeys inaccessible.

### Simplicity

Removing network dependencies removes entire classes of failure and attack.

Offline-first is therefore both a usability decision and a security decision.

---

## 5.4 The Archive Is Sacred

The external archive represents the family's permanent collection.

It is treated as immutable.

Magnet Memories may read from it.

It must never modify it.

This rule exists because mistakes happen.

Software evolves.

Bugs exist.

Hardware fails.

A mistake in the tablet application should never destroy the family's original archive.

Instead, the application creates a carefully prepared local copy that is optimised for playback while preserving the archive exactly as it was.

This separation between **archive** and **journal** is one of the most important safety mechanisms in the entire system.

The archive preserves history.

The journal brings that history to life.

Neither replaces the other.

---

## 5.5 Ownership Always Belongs to the Family

Many digital platforms subtly shift ownership over time.

Data becomes dependent on:

* subscriptions;
* accounts;
* vendor services;
* proprietary formats;
* online authentication.

Magnet Memories intentionally avoids these dependencies.

The owner should always remain in control of:

* their photographs;
* their stories;
* their Journey organisation;
* their NFC magnets;
* their backups.

Nothing in the application should make families feel locked into a particular service or platform.

If one day the tablet is replaced, the memories should remain theirs.

If the project eventually disappears, the memories should remain accessible.

Software should serve memories.

Memories should never become dependent on software.

---

## 5.6 Calm Security

Security is often associated with warnings, passwords and interruptions.

For a family memory journal, this would create the wrong experience.

The application should feel calm.

Security should exist quietly beneath the surface.

Examples include:

* validating every imported file without alarming the owner;
* detecting corruption before playback;
* refusing unsafe imports politely;
* recovering from interruption automatically;
* preventing accidental data loss.

The owner should rarely think about security because the system has already considered it.

The absence of visible security mechanisms should not be mistaken for their absence.

The best security often goes unnoticed.

---

## 5.7 Simplicity Is a Security Feature

Complex systems are harder to understand.

Harder systems are more likely to contain mistakes.

Magnet Memories therefore values simplicity as a security principle.

Examples include:

* one Journey identifier;
* one NFC URI;
* one local database;
* one owner;
* one archive;
* one journal.

Where several designs provide equivalent functionality, the simpler design should generally be preferred.

This does not mean sacrificing capability.

It means reducing unnecessary complexity.

Every additional moving part introduces another opportunity for misunderstanding or failure.

---

## 5.8 Explicit Over Implicit

The application should never perform significant actions silently.

Examples:

Imports begin only after confirmation.

Deleting a Journey always requires owner intent.

Changing a Journey slug warns that magnets will need to be reprogrammed.

Cloud services are never contacted automatically.

Storage requirements are shown before importing.

Wherever possible, the owner should understand:

* what is happening;
* why it is happening;
* what the consequences will be.

Predictable behaviour builds long-term trust.

---

## 5.9 Privacy Through Absence

Many privacy features are implemented by protecting collected information.

Magnet Memories prefers a simpler strategy.

Do not collect the information in the first place.

Version 1 intentionally avoids:

* user accounts;
* advertising identifiers;
* analytics;
* behavioural tracking;
* social profiles;
* unnecessary metadata;
* cloud copies.

Every piece of information that does not exist is one less thing that requires protection.

Privacy therefore becomes an architectural property rather than a configurable option.

---

## 5.10 Recovery Over Perfection

No software system is perfect.

Disks fail.

Power is interrupted.

Media becomes corrupted.

People make mistakes.

The architecture therefore assumes that failures will eventually occur.

Rather than attempting to prevent every possible failure, the system is designed so that failures remain recoverable.

Examples include:

* temporary workspaces during import;
* verification before committing Journeys;
* immutable external archives;
* deterministic file organisation;
* explicit backup support;
* integrity verification.

Success is measured not only by avoiding failure, but by making recovery straightforward when failure eventually occurs.

---

## 5.11 Technology Should Fade Into the Background

The owner should remember:

the trip,

the people,

the laughter,

the story,

—not the software.

Scanning an NFC magnet should feel like opening a favourite travel journal.

Turning pages should feel natural.

Live Photos should feel like memories gently coming back to life.

At no point should the owner feel as though they are operating a complex software system.

Good engineering disappears behind good experiences.

---

## 5.12 The Long View

Software projects often optimise for today's hardware and today's operating systems.

Magnet Memories is designed with a much longer horizon.

The memories created today may still matter decades from now.

This perspective changes engineering priorities.

Portability becomes more valuable than optimisation.

Recoverability becomes more valuable than convenience.

Documentation becomes more valuable than cleverness.

Stable identifiers become more valuable than rapidly changing implementations.

Every architectural decision should therefore ask not only:

> "Will this work today?"

but also:

> **"Will someone still understand and recover these memories many years from now?"**

---

## 5.13 Philosophy in Practice

The philosophy described above is not intended to be inspirational prose.

It exists to guide engineering decisions.

Whenever contributors face competing implementation options, they should evaluate them against the principles established here.

Questions worth asking include:

* Does this require unnecessary trust?
* Does this reduce owner control?
* Does this improve recoverability?
* Does this preserve privacy by default?
* Does this make the system easier to understand?
* Does this keep the archive immutable?
* Does this increase long-term portability?
* Does this make failure safer?

If an implementation satisfies these questions, it is likely aligned with the architecture.

If not, the implementation should be reconsidered before it is accepted.

---

## Closing Thought

> **Technology changes quickly. Families do not.**

> **Magnet Memories is designed around the belief that software should quietly protect the stories people care about, remain understandable for years to come, and never ask for more trust than it genuinely needs.**

# 6. Security Principles

## 6.1 Overview

The Security Philosophy describes the values that guide Magnet Memories.

This chapter translates those values into practical engineering principles.

Every architectural decision, code review, feature proposal and pull request should be evaluated against these principles.

Whenever implementation choices conflict, contributors should prefer the option that best aligns with the principles defined here.

These principles are intended to remain stable even as implementation details evolve.

---

## 6.2 Principle 1 — Least Privilege

> **Only request the minimum access required to complete the current task.**

Every additional permission increases the application's attack surface and the amount of trust required from the owner.

Magnet Memories should therefore request permissions only when they are genuinely needed.

Examples include:

* requesting access only to the Journey folder selected through the Storage Access Framework;
* avoiding broad storage permissions;
* using NFC permissions only for Journey programming and playback;
* avoiding unnecessary network permissions;
* avoiding background services that are unrelated to the current task.

Least privilege applies not only to Android permissions but also to software components, APIs and data access.

Every module should receive only the information required to perform its responsibility.

---

## 6.3 Principle 2 — Explicit Trust

> **Trust should never be assumed. It should be established deliberately.**

Every external input should be treated as untrusted until validated.

Examples include:

* NFC tags;
* imported media;
* metadata;
* filenames;
* JSON manifests;
* deep links;
* storage paths.

Validation should occur before the information enters the application's trusted domain.

Trust is therefore a property that is earned through verification rather than granted automatically.

---

## 6.4 Principle 3 — Immutable Archive

> **The family archive is read-only.**

The external archive is the family's permanent collection.

The application may read from it.

It must never modify it.

This protects the owner's originals against:

* implementation bugs;
* interrupted imports;
* accidental deletion;
* future migration errors.

The archive should always remain a reliable source from which the journal can be rebuilt.

---

## 6.5 Principle 4 — Local Ownership

> **Imported memories belong to the owner, not the application.**

The application should never create unnecessary dependencies between memories and the software itself.

Journeys should remain:

* portable;
* recoverable;
* understandable.

Whenever practical, data should use:

* documented formats;
* stable identifiers;
* predictable storage.

This principle supports long-term ownership rather than platform lock-in.

---

## 6.6 Principle 5 — Privacy by Default

> **Privacy should exist before the owner changes a single setting.**

The safest privacy feature is one that requires no configuration.

Examples include:

* no analytics;
* no advertising identifiers;
* no telemetry;
* no automatic cloud upload;
* no behavioural tracking;
* metadata minimisation.

Owners should not need to discover hidden settings to obtain reasonable privacy.

The default experience should already respect it.

---

## 6.7 Principle 6 — Minimise Persistent Data

> **Store only what has lasting value.**

Every stored item becomes something that must later be:

* protected;
* migrated;
* backed up;
* restored;
* maintained.

The application should therefore avoid storing information that can be recreated safely.

Examples:

Retain:

* Memory Cards;
* Journey titles;
* ordering;
* favourite quotes;
* playback preferences.

Avoid retaining:

* temporary import state after completion;
* discarded metadata;
* duplicate caches;
* unnecessary diagnostic information.

Persistent storage should represent intentional knowledge rather than incidental implementation details.

---

## 6.8 Principle 7 — Validate Before Trust

> **Every external input is hostile until proven otherwise.**

This principle applies uniformly to:

* media files;
* NFC payloads;
* filenames;
* URIs;
* metadata;
* backup packages;
* import manifests.

Validation should occur as early as possible.

No downstream component should assume that previous layers have already performed validation unless explicitly documented.

Where multiple validation stages exist, each stage should verify the assumptions relevant to its own responsibility.

---

## 6.9 Principle 8 — Fail Safely

> **Unexpected situations should never silently damage memories.**

Failures are inevitable.

Silent corruption is not.

Whenever an operation cannot complete safely, the preferred outcomes are:

* rollback;
* graceful degradation;
* owner confirmation;
* recoverable failure.

The application should never prioritise apparent success over data integrity.

---

## 6.10 Principle 9 — Verify Before Commit

> **Nothing becomes permanent until it has been verified.**

Importing a Journey is intentionally divided into multiple stages.

Media should be:

1. processed;
2. verified;
3. promoted;
4. committed.

Verification includes:

* successful decoding;
* orientation correctness;
* playback preparation;
* database consistency;
* storage integrity.

Only verified content should become part of the permanent journal.

---

## 6.11 Principle 10 — Deterministic Behaviour

> **The same input should produce the same result.**

Given the same source folder and configuration, the import pipeline should produce:

* identical Journey identifiers where appropriate;
* identical ordering;
* identical pairing;
* identical playback copies;
* identical metadata.

Deterministic behaviour improves:

* testing;
* debugging;
* reproducibility;
* recovery.

It also makes unexpected behaviour easier to identify.

---

## 6.12 Principle 11 — Defense in Depth

> **Multiple independent protections are stronger than one.**

No single mechanism should be solely responsible for protecting memories.

For example:

A Journey import relies on:

* Storage Access Framework;
* file validation;
* metadata validation;
* pairing validation;
* output verification;
* Room transactions;
* storage integrity checks.

Each layer assumes that another layer may eventually fail.

---

## 6.13 Principle 12 — Separation of Responsibilities

> **Every component should have one clear responsibility.**

Examples include:

The NFC layer identifies Journeys.

The Import Pipeline prepares Journeys.

The Media Pipeline processes files.

Room stores metadata.

The playback engine presents memories.

Responsibilities should remain distinct.

This separation reduces coupling and simplifies both testing and security review.

---

## 6.14 Principle 13 — Stable Identity

> **Every important object should have one stable identity.**

Journeys have one slug.

Moments have one identifier.

Media belongs to one Moment.

Files belong to one Journey.

Stable identity simplifies:

* repair;
* backup;
* restoration;
* duplicate detection;
* NFC programming.

Changing identifiers should be an exceptional event rather than routine behaviour.

---

## 6.15 Principle 14 — Relative Over Absolute

> **References should describe relationships, not device-specific locations.**

Absolute paths are fragile.

They depend on:

* operating system layout;
* storage devices;
* mount locations;
* migration history.

Relative references remain portable.

The database should therefore reference:

```text id="4grn5q"
journeys/{journeyId}/stills/{momentId}.jpg
```

rather than:

```text id="r5j7vk"
/storage/emulated/0/Android/...
```

This improves portability and simplifies restoration.

---

## 6.16 Principle 15 — Explicit User Intent

> **Significant actions require deliberate owner choice.**

The application should never silently:

* delete Journeys;
* rewrite NFC tags;
* overwrite backups;
* import new media;
* change Journey slugs.

Where destructive or irreversible actions exist, they should require clear confirmation.

The owner should always understand the consequences of the action they are taking.

---

## 6.17 Principle 16 — Secure Defaults

> **The safest reasonable behaviour should be the default behaviour.**

Examples include:

* offline operation;
* no analytics;
* automatic validation;
* conservative pairing;
* read-only archive;
* local storage.

Advanced users may later customise behaviour, but default settings should already represent good security practice.

---

## 6.18 Principle 17 — Recoverability

> **Recovery is part of security.**

Security is not only preventing failure.

It is also enabling recovery after failure occurs.

Examples include:

* import manifests;
* temporary workspaces;
* deterministic storage;
* integrity verification;
* backup support;
* restore workflows.

A system that cannot recover from mistakes is not a dependable system.

---

## 6.19 Principle 18 — Transparency

> **The application should explain what it is doing whenever the owner might reasonably ask why.**

Transparency builds trust.

Examples include:

* showing estimated storage before import;
* explaining why NFC permissions are needed;
* displaying import progress;
* reporting skipped files;
* warning when Journey slugs change.

The application should avoid surprising behaviour.

---

## 6.20 Principle 19 — Long-Term Portability

> **Design for decades rather than devices.**

Hardware changes.

Android versions change.

Storage technologies evolve.

Family memories should remain understandable despite those changes.

This principle favours:

* documented file formats;
* standard media formats;
* predictable directory structures;
* stable identifiers;
* comprehensive documentation.

Future maintainers should be able to reconstruct the journal even if today's implementation no longer exists.

---

## 6.21 Principle 20 — Human-Centred Security

> **Security exists to protect people, not to impress engineers.**

Every safeguard should ultimately improve the owner's experience.

Security mechanisms that introduce unnecessary confusion, friction or anxiety should be reconsidered.

The ideal security feature is one that quietly protects memories while allowing families to focus entirely on the stories those memories contain.

Technology should remain in the background.

The memories should remain in the foreground.

---

## 6.22 Applying These Principles

No individual principle should be interpreted in isolation.

Engineering decisions often require balancing several principles simultaneously.

For example:

Choosing to copy imported media into app-managed storage supports:

* Principle 3 — Immutable Archive
* Principle 4 — Local Ownership
* Principle 10 — Deterministic Behaviour
* Principle 17 — Recoverability
* Principle 19 — Long-Term Portability

Similarly, storing only relative file references supports:

* Principle 4 — Local Ownership
* Principle 14 — Relative Over Absolute
* Principle 17 — Recoverability
* Principle 19 — Long-Term Portability

Contributors should evaluate implementation decisions holistically rather than optimising for a single principle at the expense of others.

---

## Closing Statement

The principles described in this chapter are intended to remain stable throughout the lifetime of the project.

Implementation techniques will evolve.

Android APIs will change.

Libraries will be replaced.

New features will be introduced.

These principles should remain the foundation upon which those changes are built.

> **Every line of code should make the journal a little more dependable, a little more understandable, and a little more worthy of the trust families place in it.**
# 7. Protected Assets

## 7.1 Overview

Before a system can protect something, it must first understand **what** it is protecting.

Traditional software systems typically focus on assets such as:

* databases;
* authentication credentials;
* financial records;
* network infrastructure.

Magnet Memories protects a different class of assets.

The primary purpose of the application is not to secure information in the abstract, but to preserve the experiences and stories represented by that information.

For this reason, the project classifies protected assets into four categories:

1. Physical Assets
2. Digital Assets
3. Personal Assets
4. Emotional Assets

Together, these represent everything the owner entrusts to the application.

---

## 7.2 Asset Relationships

The following diagram illustrates how the different asset categories relate to one another.

```text id="b2qt3d"
                 Family Memories
                        │
      ┌─────────────────┼─────────────────┐
      │                 │                 │
      ▼                 ▼                 ▼
Physical Assets    Digital Assets   Emotional Assets
      │                 │                 │
      └────────────┬────┴────────────┬────┘
                   ▼                 ▼
             Security           Privacy
```

Physical assets enable the system.

Digital assets store the information.

Personal assets describe the owner.

Emotional assets give the entire system its purpose.

---

# 7.3 Physical Assets

Physical assets are tangible objects that participate in the Magnet Memories ecosystem.

Although they contain relatively little information themselves, their availability and integrity directly influence the owner's experience.

---

## Tablet

The Android tablet is the primary journal.

It stores:

* imported Journeys;
* processed media;
* Room database;
* preferences;
* playback state;
* thumbnails.

Threats include:

* loss;
* theft;
* hardware failure;
* storage corruption;
* accidental damage.

Security objective:

Maintain the integrity of locally stored Journeys and support future restoration.

---

## External Archive Drive

The external archive stores the family's permanent originals.

Examples include:

* original HEIC photographs;
* Live Photos;
* original videos;
* exported Journey folders;
* optional metadata.

The archive is considered immutable.

Threats include:

* accidental deletion;
* hardware failure;
* filesystem corruption;
* improper modification by software.

Security objective:

Ensure the application never modifies the archive.

---

## NFC Magnets

Each fridge magnet contains an NFC tag.

The tag identifies a Journey.

It intentionally stores only:

* a URI;
* a Journey identifier.

Threats include:

* physical damage;
* accidental rewriting;
* replacement;
* cloning;
* loss.

The compromise of an NFC tag should never expose personal memories.

---

## USB Cables and Accessories

Although often overlooked, import reliability depends on:

* USB connections;
* adapters;
* external storage interfaces.

Unexpected disconnection during import is treated as a recoverable operational event rather than a catastrophic failure.

---

# 7.4 Digital Assets

Digital assets represent the information managed by the application.

Unlike physical assets, they can usually be copied, backed up and restored.

---

## Journey Metadata

Journey metadata includes:

* title;
* slug;
* dates;
* location;
* cover selection;
* ordering;
* playback configuration.

This metadata provides structure to the journal.

Without it, photographs become an unordered collection rather than a meaningful story.

Threats include:

* corruption;
* accidental modification;
* inconsistent identifiers.

---

## Memory Cards

Memory Cards contain owner-authored narrative.

Examples include:

* personal reflections;
* favourite moments;
* family stories;
* travel memories;
* contextual notes.

These entries often cannot be reconstructed from photographs alone.

For many Journeys, they represent the most valuable digital content in the application.

---

## Photographs

Processed photographs are the primary visual content presented during Journey playback.

The application stores:

* optimised playback copies;
* thumbnails.

The originals remain inside the external archive.

Threats include:

* corruption;
* deletion;
* decode failure;
* incomplete import.

---

## Live Photos

Live Photos consist of:

* one still image;
* one motion component.

Together they form a single Moment.

Threats include:

* pairing failure;
* codec incompatibility;
* motion corruption;
* incomplete playback.

The application should preserve the logical relationship between both components.

---

## Room Database

The Room database contains the journal's organisational knowledge.

Examples include:

* Journey records;
* Moment records;
* ordering;
* references;
* playback state.

The database intentionally avoids storing media binaries.

Threats include:

* migration failure;
* corruption;
* inconsistent references.

---

## Preferences

Preferences contain application behaviour rather than family content.

Examples include:

* playback settings;
* Live Photo preferences;
* onboarding completion;
* Workshop configuration.

Although replaceable, preserving preferences contributes to a consistent owner experience.

---

## Backup Packages

Future backup packages contain:

* metadata;
* preferences;
* local media;
* manifests.

Threats include:

* incomplete creation;
* corruption;
* version mismatch.

---

# 7.5 Personal Assets

Personal assets describe information that relates directly to the owner or their family.

The application intentionally minimises the amount of personal information it retains.

Nevertheless, some information is necessary to deliver the intended experience.

---

## Journey Stories

Stories may contain:

* names;
* relationships;
* events;
* reflections;
* family history.

Unlike technical metadata, these stories often possess long-term sentimental value.

They should therefore receive stronger protection than ordinary application settings.

---

## Travel History

A Journey may indirectly reveal:

* places visited;
* approximate dates;
* travel chronology.

The application should avoid exposing more location information than necessary.

Exact GPS coordinates are intentionally excluded from persistent storage by default.

---

## Family Relationships

Photographs and Memory Cards may implicitly reveal:

* parents;
* children;
* relatives;
* friends.

The application should never attempt to infer or classify these relationships automatically.

Family meaning belongs to the owner.

---

# 7.6 Emotional Assets

This category is unique to Magnet Memories.

Although emotional assets cannot be represented directly as files, they are the reason the application exists.

Engineering decisions should acknowledge their importance.

---

## Family History

Every Journey contributes to a larger family narrative.

The application preserves:

* milestones;
* traditions;
* holidays;
* celebrations;
* everyday moments.

Losing this chronology would diminish the value of the journal even if every photograph survived.

---

## Context

A photograph rarely explains itself.

Context comes from:

* Memory Cards;
* ordering;
* favourite quotes;
* captions;
* surrounding photographs.

Context transforms media into memories.

Protecting that context is as important as protecting the photographs themselves.

---

## Storytelling

The application is designed around narrative rather than storage.

Its purpose is to help families revisit experiences rather than browse files.

Journey structure therefore becomes an asset in its own right.

---

## Continuity

Families often revisit memories years later.

The ability to open a magnet and immediately experience an entire Journey exactly as intended has lasting emotional value.

Preserving that continuity is one of the system's primary responsibilities.

---

# 7.7 Asset Classification

Not every asset requires the same level of protection.

The following classification reflects their relative importance.

| Asset            | Availability |   Integrity  | Confidentiality |
| ---------------- | :----------: | :----------: | :-------------: |
| Memory Cards     |     High     | **Critical** |       High      |
| Journey Metadata |     High     | **Critical** |      Medium     |
| Photographs      |     High     | **Critical** |       High      |
| Live Photos      |     High     | **Critical** |       High      |
| Room Database    |     High     | **Critical** |      Medium     |
| Preferences      |    Medium    |    Medium    |       Low       |
| NFC Tags         |    Medium    |    Medium    |       Low       |
| Temporary Cache  |      Low     |      Low     |       Low       |

The application prioritises **integrity** because memories that silently change or become corrupted may be impossible to reconstruct.

---

# 7.8 Asset Ownership

Every protected asset has a single owner.

The owner is always the family using the application.

The software never claims ownership of:

* photographs;
* stories;
* metadata;
* backups;
* NFC mappings.

This distinction is important.

The application manages these assets.

It does not possess them.

---

# 7.9 Asset Lifecycle

Protected assets progress through predictable lifecycle stages.

```text id="w1k6hr"
Captured
      │
      ▼
Archived
      │
      ▼
Imported
      │
      ▼
Organised
      │
      ▼
Experienced
      │
      ▼
Backed Up
      │
      ▼
Restored (if required)
```

Every transition should preserve:

* integrity;
* continuity;
* recoverability.

No lifecycle stage should permanently reduce the owner's ability to recover their memories.

---

# 7.10 Security Priorities

When security decisions require trade-offs, assets should generally be prioritised in the following order:

1. Memory Cards and owner-authored stories
2. Journey integrity
3. Original photographs
4. Live Photo relationships
5. Journey organisation
6. Backup integrity
7. Preferences
8. Temporary data

This ordering reflects the principle that **owner-created meaning is often less recoverable than media itself**.

A lost thumbnail can be regenerated.

A lost story written by a parent years ago cannot.

---

# 7.11 Asset Protection Strategy

Every asset category receives protection through different architectural mechanisms.

| Asset Category   | Primary Protection                                                  |
| ---------------- | ------------------------------------------------------------------- |
| Physical Assets  | Android security, immutable archive, backup strategy                |
| Digital Assets   | Validation, verification, deterministic storage, integrity checks   |
| Personal Assets  | Metadata minimisation, local-first architecture, privacy-by-default |
| Emotional Assets | Stable identifiers, Memory Cards, Journey structure, recoverability |

This layered approach recognises that not all assets are protected in the same way.

Different risks require different architectural responses.

---

## Closing Statement

The assets described in this chapter define **what matters** within Magnet Memories.

Subsequent chapters build upon this understanding by asking a different question:

> **Given these assets, what realistic threats exist, and how should the architecture defend against them?**

The next chapter introduces the project's formal threat model and applies it systematically across every trust boundary in the system.
# 8. Trust Boundaries

## 8.1 Overview

Every software system operates across multiple environments.

Some components are fully controlled by the application.

Others are partially trusted.

Many are completely outside the application's control.

Security depends on recognising the differences between these environments.

A **trust boundary** is the point at which information moves from one trust level to another.

Whenever information crosses a trust boundary, it must be treated as potentially unsafe until it has been validated.

In Magnet Memories, nearly every security mechanism ultimately exists to protect one of these boundaries.

---

## 8.2 Why Trust Boundaries Matter

Trust is not binary.

It is contextual.

For example:

* a photograph stored inside the application's verified local journal is considerably more trustworthy than a photograph presented by an unknown USB drive;
* an NFC URI generated by Workshop is more trustworthy than an arbitrary NFC tag discovered in the wild;
* a validated Room entity is more trustworthy than raw JSON read from external storage.

Treating every input as equally trustworthy inevitably leads to security weaknesses.

Instead, the architecture defines where trust begins, where it ends, and how information is permitted to move between those regions.

---

## 8.3 Trust Zones

The system is divided into five primary trust zones.

```text id="8d2w3k"
                 ┌─────────────────────────────┐
                 │        Owner                │
                 └─────────────┬───────────────┘
                               │
                               ▼
                 ┌─────────────────────────────┐
                 │     Android Operating       │
                 │         System              │
                 └─────────────┬───────────────┘
                               │
                               ▼
      ┌────────────────────────────────────────────────┐
      │            Magnet Memories Application          │
      │                                                │
      │  UI  │ Domain │ Import │ Media │ Database │ NFC │
      └─────────────┬───────────────────────────────────┘
                    │
          ┌─────────┴──────────┐
          ▼                    ▼
External Sources        Local Journal Storage
(Untrusted)               (Trusted)
```

Each zone has different security assumptions.

Information becomes more trusted only after passing through controlled validation.

---

# 8.4 Trust Zone 1 — The Owner

The owner represents the highest level of trust within the system.

Examples include:

* creating Memory Cards;
* importing Journeys;
* programming NFC magnets;
* editing Journey information;
* confirming destructive operations.

Although owners may accidentally make mistakes, they are never considered malicious actors within the Version 1 threat model.

The application should therefore optimise for:

* clarity;
* confirmation;
* recoverability.

Rather than preventing owner actions, the system should help prevent accidental consequences.

---

# 8.5 Trust Zone 2 — Android

The application relies on Android to provide:

* process isolation;
* application sandboxing;
* permission enforcement;
* NFC routing;
* Storage Access Framework;
* app-private storage;
* lifecycle management.

Android is considered a trusted platform.

However, the application should avoid depending on undocumented platform behaviour.

Where Android provides security guarantees, the application should use them rather than attempting to recreate them.

Examples include:

* app-private directories;
* Storage Access Framework permissions;
* foreground NFC dispatch.

The application assumes the operating system itself has not been compromised.

Protection against a compromised operating system is outside the Version 1 threat model.

---

# 8.6 Trust Zone 3 — Magnet Memories

Everything inside the application's controlled architecture is considered trusted **only after validation has completed.**

This includes:

* Room entities;
* validated Journey metadata;
* verified playback copies;
* processed Live Photos;
* NFC mappings;
* application preferences.

Trust inside the application is earned rather than assumed.

No component should accept unvalidated external information simply because another layer received it.

Each architectural layer is responsible for validating the assumptions relevant to its own responsibilities.

---

# 8.7 Trust Zone 4 — Local Journal Storage

The local journal represents the application's trusted long-term storage.

Contents include:

* processed photographs;
* processed Live Photos;
* thumbnails;
* Room database;
* preferences;
* playback metadata.

Files stored here have already passed:

* discovery;
* validation;
* verification;
* import processing.

Consequently, playback may assume these files satisfy the application's integrity requirements.

If corruption is later detected, the affected Journey should transition back into a recovery workflow rather than continuing to assume trust.

---

# 8.8 Trust Zone 5 — External Sources

Every external source is considered **untrusted**.

Examples include:

* USB drives;
* external hard drives;
* imported folders;
* NFC tags;
* JSON manifests;
* media files;
* backup packages.

Even when these sources originate from the owner's own devices, they should still be validated.

Reasons include:

* accidental corruption;
* interrupted copy operations;
* damaged storage;
* export inconsistencies;
* future compatibility issues.

The application should never bypass validation merely because the owner selected the source.

---

# 8.9 Boundary A — Owner → Application

The first trust boundary occurs whenever the owner provides input.

Examples include:

* entering Journey titles;
* writing Memory Cards;
* editing dates;
* selecting folders;
* confirming imports.

Although owner input is trusted in intent, it is not automatically trusted in format.

Validation should include:

* string length;
* required fields;
* slug rules;
* unsupported characters;
* duplicate identifiers.

The purpose of validation here is reliability rather than defence against malicious behaviour.

---

# 8.10 Boundary B — External Source → Import Pipeline

This is the largest trust boundary in the system.

Everything imported from external storage crosses this boundary.

Examples include:

* photographs;
* HEIC files;
* Live Photos;
* videos;
* metadata;
* JSON files;
* folder structures.

Every imported object must pass:

1. discovery;
2. format validation;
3. metadata extraction;
4. structural validation;
5. processing;
6. output verification.

Only after all stages complete successfully should the object become part of the trusted journal.

---

# 8.11 Boundary C — NFC Tag → Application

An NFC tag should always be considered untrusted.

Even tags previously programmed by Magnet Memories may have been:

* replaced;
* rewritten;
* damaged;
* cloned.

Validation therefore includes:

* NDEF decoding;
* URI parsing;
* scheme validation;
* host validation;
* slug validation;
* Journey lookup.

Only after successful validation should Journey playback begin.

The NFC tag itself is never considered proof that a Journey exists.

---

# 8.12 Boundary D — Import Pipeline → Local Journal

This boundary separates temporary work from permanent storage.

Before crossing it:

* files remain disposable;
* processing may be retried;
* failures remain recoverable.

After crossing it:

* the Journey becomes part of the owner's permanent journal.

Promotion therefore occurs only after:

* verification;
* integrity checks;
* database preparation;
* successful output generation.

This boundary protects the journal from incomplete imports.

---

# 8.13 Boundary E — Database → Playback

Playback trusts the Room database only because earlier components have validated its contents.

Nevertheless, playback should still verify assumptions relevant to rendering.

Examples include:

* referenced files exist;
* dimensions remain valid;
* playback copies decode;
* motion files prepare successfully.

Trust should never eliminate defensive programming.

---

# 8.14 Boundary F — Backup → Restore

Backup packages originate outside the running application.

Even backups created by earlier versions of Magnet Memories should undergo validation before restoration.

Checks include:

* schema version;
* manifest integrity;
* required directories;
* metadata consistency;
* file availability.

Restore operations should never blindly overwrite existing data.

---

# 8.15 Boundary G — Internal Module Boundaries

Trust boundaries also exist within the application.

For example:

```text id="m2vq7r"
UI

↓

ViewModel

↓

Domain

↓

Repository

↓

Database
```

Each layer should expose only validated domain objects.

Lower layers should not require knowledge of:

* UI state;
* Android components;
* temporary parsing structures.

This separation limits the propagation of invalid state.

---

# 8.16 Data Flow Across Boundaries

Every significant data flow follows the same lifecycle.

```text id="a7p8zs"
External Input

↓

Validation

↓

Transformation

↓

Verification

↓

Trusted Representation

↓

Persistence

↓

Playback
```

Trust is established gradually rather than immediately.

---

# 8.17 Temporary Trust

Some information is trusted only briefly.

Examples include:

* import manifests;
* decoded metadata;
* temporary thumbnails;
* transcoding outputs.

Temporary trust should expire when the operation completes.

Temporary artefacts should then be:

* deleted;
* replaced;
* regenerated when necessary.

---

# 8.18 Trust Revocation

Trust is not permanent.

Previously trusted data may lose trust if:

* corruption is detected;
* files disappear;
* integrity verification fails;
* database inconsistencies emerge;
* migration fails.

When trust is revoked, the application should transition the affected Journey into a recoverable state rather than continuing normal playback.

---

# 8.19 Boundary Protection Principles

Every trust boundary should satisfy the following rules.

### Validate Before Crossing

No external object enters a trusted zone without validation.

---

### Verify Before Persistence

Nothing becomes permanent until verification succeeds.

---

### Minimise Boundary Width

Only the information required for the next stage should cross the boundary.

Avoid passing raw structures unnecessarily.

---

### Preserve Provenance

Where useful, retain information describing:

* origin;
* processing;
* validation status;
* import method.

This improves diagnostics and recovery.

---

### Reject Ambiguity

When uncertainty exists, the application should request clarification rather than making irreversible assumptions.

Examples include:

* ambiguous Live Photo pairing;
* duplicate Journey slugs;
* uncertain metadata.

---

# 8.20 Trust Boundary Summary

| Boundary                  | Trust Change               | Primary Protection       |
| ------------------------- | -------------------------- | ------------------------ |
| Owner → Application       | Intent → Structured Input  | Input validation         |
| External Storage → Import | Untrusted → Temporary      | Discovery & validation   |
| NFC → Application         | Untrusted → Journey Lookup | URI validation           |
| Import → Local Journal    | Temporary → Trusted        | Verification & promotion |
| Database → Playback       | Trusted → Runtime          | Integrity checks         |
| Backup → Restore          | External → Trusted         | Manifest validation      |

This table summarises the architectural boundaries that protect the journal throughout its lifecycle.

---

# 8.21 Relationship to Threat Modelling

Trust boundaries define **where** threats can occur.

The next chapter analyses **what** those threats are.

Every threat identified in the STRIDE model will reference one or more boundaries established in this chapter.

This ensures that the threat model remains grounded in the architecture rather than becoming an abstract security exercise.

---

## Closing Statement

Trust is one of the most valuable resources in any software system.

Magnet Memories deliberately grants that trust slowly.

Information begins outside the system as unknown.

It becomes trusted only through validation, verification and careful architectural boundaries.

> **Every boundary crossed without validation is an opportunity for failure. Every boundary crossed deliberately is an opportunity to preserve the integrity of a family's memories.**

