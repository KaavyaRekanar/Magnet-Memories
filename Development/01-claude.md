# CLAUDE.md

> **Engineering Handbook for Claude Code**

Project: **Magnet Memories**

Version: **1.0**

Status: **Living Document**

---

# Welcome

Welcome to the Magnet Memories project.

This document defines how this project should be implemented.

It is not a prompt.

It is not a coding style guide.

It is the engineering handbook for the project.

Before writing code, modifying existing functionality, or proposing architectural changes, read this document in full.

Every implementation decision should align with the principles described here.

---

# The Most Important Rule

> **The documentation is the source of truth. The code is an implementation of the documentation—not the other way around.**

The documentation represents deliberate architectural decisions made before implementation began.

If implementation appears easier by deviating from the documentation, **the implementation is wrong—not the documentation**.

Do not silently change the architecture.

Do not reinterpret the vision.

Do not optimise away intentional design decisions.

If you believe a documented decision should change:

1. Stop implementation.
2. Explain the conflict.
3. Reference the relevant documentation.
4. Propose a new Architecture Decision Record (ADR).
5. Wait for approval before proceeding.

Architectural integrity is more valuable than implementation speed.

---

# Project Overview

Magnet Memories is an offline-first Android application that transforms a collection of travel photographs into an interactive leather-bound journal.

Physical NFC-enabled fridge magnets represent journeys.

Scanning a magnet immediately opens the corresponding travel journal.

Each journal presents:

* a handcrafted Memory Card;
* photographs;
* Live Photos;
* personal stories;
* meaningful moments.

The application is not intended to be a media gallery.

It is intended to recreate the emotional experience of opening a treasured travel journal.

---

# Mission

The goal of Magnet Memories is not to organise photographs.

The goal is to preserve memories.

Everything in this project should reinforce that objective.

When faced with competing implementation choices, always ask:

> **Does this help families revisit memories, or does it simply manage files?**

Choose the former.

---

# Vision

The owner should forget they are using software.

Scanning a fridge magnet should feel like opening a beautifully crafted travel journal.

The interface should disappear.

Technology should become invisible.

Only the memories should remain.

Every implementation decision should support this experience.

---

# Engineering Philosophy

The project follows several core beliefs.

## Build for Decades

Family memories often outlive software.

Design every feature with long-term durability in mind.

Avoid decisions that optimise only for today's implementation.

---

## Simplicity Wins

Simple systems are:

* easier to understand;
* easier to maintain;
* easier to test;
* easier to recover;
* easier to secure.

Whenever two solutions provide equivalent value, choose the simpler one.

---

## Architecture Before Features

Never implement a feature simply because it is technically possible.

First determine whether it aligns with:

* the product vision;
* UX principles;
* architecture;
* security guarantees.

If not, the feature should not be implemented.

---

## Recovery Before Convenience

Users can tolerate inconvenience.

They cannot tolerate losing memories.

When forced to choose between convenience and recoverability, always choose recoverability.

---

## Privacy by Architecture

Privacy should emerge naturally from the system's design.

Avoid features that require collecting unnecessary information.

The safest personal information is information that never exists.

---

## Offline First

Every imported Journey should remain fully usable without:

* internet connectivity;
* cloud services;
* remote APIs;
* external storage.

Offline capability is a fundamental architectural property.

It is not optional.

---

# Decision Hierarchy

When multiple implementation approaches are possible, use the following order of precedence.

1. Product Vision
2. Architectural Guarantees
3. UX Principles
4. Security & Privacy Architecture
5. Architecture Decision Records
6. Product Specification
7. Testing Strategy
8. Simplicity
9. Performance Optimisation

Higher items always take precedence over lower items.

Example:

Streaming photographs from an external hard drive may reduce storage usage.

However, it violates the architectural guarantee that imported Journeys remain fully available offline.

Therefore, the optimisation must be rejected.

---

# What Success Looks Like

The project is successful if:

* the architecture remains coherent;
* Journeys remain recoverable;
* privacy remains intact;
* the application remains understandable;
* contributors can reason about the system easily;
* implementation follows documentation consistently.

Success is **not** measured by:

* number of features;
* lines of code;
* architectural complexity;
* use of fashionable technologies.

Quality always outweighs quantity.

---

# What This Project Is

Magnet Memories is:

* a travel journal;
* a storytelling platform;
* an offline-first application;
* a local-first application;
* a family memory archive;
* an emotionally driven product.

---

# What This Project Is Not

Magnet Memories is not:

* a photo gallery;
* a file manager;
* a cloud platform;
* a social network;
* a digital asset management system;
* a backup application;
* a media streaming service.

Features that move the project toward these categories should be treated with caution.

---

# When Unsure

If uncertainty exists:

Do not guess.

Do not invent behaviour.

Do not optimise prematurely.

Instead:

1. Review the documentation.
2. Review the relevant ADRs.
3. Explain the uncertainty.
4. Ask for clarification before implementing.

Incorrect implementation is more expensive than delayed implementation.

---

# First Principle

Whenever you write code, remember this sentence:

> **You are not building an Android application. You are building a physical object that happens to be made of software—a family travel journal that people will continue opening for many years.**

Every line of code should make that journal more dependable, more understandable, and more enjoyable to revisit.
# Repository Structure

The repository is organised around architecture rather than technology.

Every directory should have a single, clearly defined responsibility.

Avoid creating folders based solely on implementation details.

The project should remain understandable to a new contributor without requiring knowledge of Android internals.

The high-level structure is expected to resemble:

```text
app/
    ui/
    domain/
    data/
    media/
    import/
    nfc/
    storage/
    backup/
    workshop/
    common/

docs/
    Product/
    UX/
    Technical/

tests/

assets/
```

Documentation and implementation should evolve together.

If the architecture changes, update the documentation before or alongside the implementation.

---

# Architectural Layers

The application follows a layered architecture.

```text
UI

↓

Presentation

↓

Domain

↓

Data

↓

Platform
```

Each layer has one responsibility.

## User Interface

Responsible only for:

* displaying information;
* collecting user interaction;
* rendering animations.

The UI must never contain business logic.

---

## Presentation Layer

Responsible for:

* state management;
* coordinating user interactions;
* preparing data for presentation.

The presentation layer should not perform media processing or storage operations directly.

---

## Domain Layer

The domain layer contains the application's business rules.

Examples include:

* Journey organisation;
* Memory Card behaviour;
* playback sequencing;
* import workflows;
* NFC resolution.

The domain layer should remain independent of Android-specific APIs whenever practical.

---

## Data Layer

Responsible for:

* Room;
* repositories;
* storage;
* import persistence;
* backup persistence.

It should not contain user interface behaviour.

---

## Platform Layer

Responsible for interacting with Android.

Examples include:

* NFC
* Storage Access Framework
* MediaStore
* filesystem
* notifications
* lifecycle

Platform-specific code should remain isolated from the rest of the architecture.

---

# Separation of Responsibilities

Every class should answer one question.

> What single responsibility does this class own?

If the answer contains "and", the class probably has multiple responsibilities.

For example:

Bad

JourneyImporter

* imports photographs
* validates metadata
* generates thumbnails
* writes database records
* creates NFC mappings

Good

JourneyDiscovery

JourneyValidator

MediaProcessor

ThumbnailGenerator

JourneyRepository

JourneyImporter

Each component becomes:

* easier to understand;
* easier to test;
* easier to replace.

---

# Package Design

Packages should be organised around features and responsibilities rather than utility classes.

Prefer:

```text
journey/
media/
import/
nfc/
backup/
workshop/
```

Avoid:

```text
helpers/
utils/
misc/
common2/
new/
```

Large "utility" packages often indicate architectural drift.

---

# Dependency Rules

Dependencies always point downward through the architecture.

```text
UI

↓

Presentation

↓

Domain

↓

Data

↓

Platform
```

Lower layers must never depend on higher layers.

Examples:

The domain layer must never depend on Compose.

Repositories must never know about UI state.

Media processing must never know about screen navigation.

This separation preserves maintainability.

---

# State Management

The application should maintain a single source of truth.

Every piece of state should have one owner.

Examples include:

Journey playback state

Import progress

Current page

Selected playback mode

Workshop editing state

Avoid duplicated state whenever possible.

Duplicated state inevitably becomes inconsistent.

---

# Data Flow

Data should move in one direction.

```text
User Action

↓

Presentation

↓

Domain

↓

Repository

↓

Database

↓

Presentation

↓

UI
```

Avoid circular data flow.

Avoid hidden mutations.

Changes should be explicit and predictable.

---

# Naming Conventions

Names should prioritise clarity over brevity.

Prefer:

```text
JourneyPlaybackController
```

Instead of:

```text
JPCtrl
```

Methods should describe actions.

Good:

```text
importJourney()

generatePlaybackCopy()

validateNfcTag()
```

Avoid vague names such as:

```text
handle()

process()

update()

manage()

doStuff()
```

The name should communicate intent without requiring implementation details.

---

# Code Style

Write code that reads like prose.

Prefer small, focused functions.

Avoid deeply nested logic.

Use early returns where appropriate.

Avoid excessive abstraction.

Every abstraction should solve an actual problem rather than anticipate hypothetical future needs.

Readability always outweighs cleverness.

---

# Error Handling

Errors should be expected rather than exceptional.

Every recoverable operation should return meaningful failure information.

Avoid:

* silent failures;
* swallowed exceptions;
* empty catch blocks.

Every failure should either:

* recover automatically;
* provide actionable information;
* guide the owner toward recovery.

The application should never crash because of malformed Journey data.

---

# Logging

Logs exist for developers.

They do not exist to record owner behaviour.

Never log:

* Memory Card contents;
* personal stories;
* GPS information;
* owner names;
* photographs.

Prefer logging:

* Journey identifiers;
* import stage;
* validation result;
* processing duration;
* error category.

Logs should support debugging without compromising privacy.

---

# Performance Philosophy

Performance should improve the owner's experience without compromising the architecture.

Optimise only after measuring.

Do not introduce architectural complexity for hypothetical performance gains.

Prioritise:

* smooth page turns;
* responsive Journey loading;
* efficient thumbnail generation;
* predictable import behaviour.

The owner should perceive the journal as effortless.

---

# Accessibility

Accessibility is a core design requirement.

It is not an optional enhancement.

Every feature should consider:

* readable typography;
* sufficient contrast;
* scalable layouts;
* TalkBack compatibility;
* reduced motion preferences;
* generous touch targets.

A feature that excludes users is incomplete.

---

# Documentation

Documentation is part of the implementation.

Whenever architectural behaviour changes:

* update the relevant documentation;
* update ADRs if required;
* update diagrams where necessary.

Code should never become the only source of truth.

---

# Testing Expectations

Every meaningful implementation should include corresponding tests.

Minimum expectation:

* business logic → unit tests;
* cross-component behaviour → integration tests;
* owner workflows → end-to-end tests.

When fixing a defect:

1. reproduce the issue;
2. write a failing test;
3. implement the fix;
4. verify the test passes.

Every bug should strengthen the test suite.

---

# Pull Request Expectations

Every pull request should:

* solve one clearly defined problem;
* preserve architectural guarantees;
* include appropriate tests;
* update documentation where necessary;
* remain understandable during review.

Large, unrelated changes should be split into smaller pull requests whenever practical.

---

# Definition of Good Code

Good code in Magnet Memories is code that is:

* understandable;
* deterministic;
* testable;
* recoverable;
* well documented;
* privacy conscious;
* architecturally consistent.

It is **not** necessarily the shortest or the most clever solution.

Future contributors should be able to understand the reasoning behind the implementation with minimal effort.

---

# Engineering Mindset

Before completing any implementation, ask yourself:

* Does this preserve the project vision?
* Does it respect the architectural guarantees?
* Does it strengthen the journal metaphor?
* Does it improve recoverability?
* Does it minimise unnecessary complexity?
* Can another contributor understand this in six months?
* Would I still make this decision if this application needed to be maintained for the next twenty years?

If the answer to any of these questions is "no", reconsider the implementation before merging it.

---
# Decision Framework

Whenever implementing a feature, follow this process.

## Step 1

Understand the problem.

Do not begin writing code immediately.

Read the relevant documentation.

Understand why the feature exists.

---

## Step 2

Identify the affected architecture.

Determine which documents are relevant.

Examples:

- UX
- Data Model
- Security Architecture
- ADRs
- Testing Strategy

---

## Step 3

Evaluate alternatives.

Never implement the first solution automatically.

Compare at least two possible implementations.

Choose the one that best satisfies the architectural principles.

---

## Step 4

Think long term.

Prefer the implementation that remains understandable in five years.

Avoid short-term optimisation that increases long-term complexity.

---

## Step 5

Implement.

Only begin implementation after the previous steps are complete.

---

## Step 6

Review.

Ask:

• Does this preserve the vision?

• Does this preserve the journal metaphor?

• Does it introduce unnecessary complexity?

• Does it require a new ADR?

• Does it require documentation changes?

• Does it require new tests?

If any answer is yes, complete those tasks before considering the feature finished.

# Final Message

Magnet Memories is not an experiment.

It is not a collection of features.

It is not a demonstration project.

It is a carefully designed product whose purpose is to preserve family memories for many years.

Every architectural decision, every line of code, every animation, every import workflow and every interaction should reinforce that purpose.

If implementation ever conflicts with the documented vision, stop.

Re-read the documentation.

Understand the intent.

Then build the software that the documentation describes.

Never the other way around.

## Final Reminder

The purpose of this repository is not simply to produce working software.

It is to build a product whose design, implementation, documentation and architecture remain aligned throughout its lifetime.

Every commit should move the project one step closer to that goal.

