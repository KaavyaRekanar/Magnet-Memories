# NFC Specification

**Project:** Magnet Memories
**Document:** NFC Specification
**Version:** 1.0
**Status:** Draft

---

# Purpose

This document defines how Magnet Memories uses NFC tags to bridge the physical and digital worlds.

The NFC system is responsible for:

* identifying a Journey;
* launching the application;
* opening the correct memory automatically;
* remaining reliable for many years;
* requiring no manual navigation.

The NFC experience should feel magical, invisible and effortless.

---

# Design Principle

> **The magnet is the bookmark.**

The owner should never search for a Journey.

Instead:

1. Pick up a magnet.
2. Touch it to the tablet.
3. The leather-bound journal opens directly to that journey.

Nothing else should be required.

---

# User Experience

```text
Fridge Magnet
      ↓
Tap against tablet
      ↓
Tablet wakes (if sleeping)
      ↓
Magnet Memories launches
      ↓
Leather journal appears
      ↓
Journey opens
      ↓
Memory begins
```

There are:

* no menus
* no search
* no gallery
* no lists
* no folder browsing

The NFC tag is the navigation.

---

# Version 1 Goals

Version 1 supports:

* one NFC tag per Journey
* NTAG216 tags
* Android foreground NFC dispatch
* automatic app launch
* automatic Journey playback
* offline operation

Version 1 does **not** require:

* NFC writing from another device
* multiple tags for one Journey
* tag history
* tag ownership tracking
* cloud lookup

---

# Supported NFC Tags

Recommended:

* NTAG216

Specifications:

* NFC Forum Type 2
* ISO/IEC 14443 Type A
* 13.56 MHz
* approximately 888 bytes usable memory

Reasons:

* inexpensive
* widely available
* excellent Android compatibility
* enough capacity for URI records
* passive (no battery)
* long lifespan

NTAG215 and NTAG213 are technically supported but are not recommended for production.

---

# Why NTAG216

Each tag only stores a tiny identifier.

The photographs remain on the tablet.

Therefore every tag only needs to contain something similar to:

```text
magnetmemories://journey/new-york-2026
```

Even hundreds of journeys use only a tiny fraction of the available tag memory.

The larger NTAG216 provides additional flexibility for future expansion.

---

# NFC Philosophy

The NFC tag should never contain:

* photographs
* thumbnails
* stories
* metadata
* Journey JSON
* personal information

The tag contains only an identifier.

Everything else belongs inside the journal.

---

# URI Format

Version 1 uses a custom URI.

Example:

```text
magnetmemories://journey/new-york-2026
```

Structure:

```text
magnetmemories://journey/{journeySlug}
```

Examples:

```text
magnetmemories://journey/japan-2027

magnetmemories://journey/sweden-summer

magnetmemories://journey/our-first-road-trip
```

---

# Why URI Instead of Raw Text

Using a URI allows Android to:

* launch the application
* pass the Journey identifier
* support future deep links
* remain extensible

The URI becomes the application's public contract.

---

# Journey Slug

Every Journey has one permanent slug.

Example:

```text
Title

New York

↓

Slug

new-york-2026
```

Slug rules:

* lowercase
* numbers allowed
* hyphens only
* ASCII
* unique
* stable
* immutable after programming unless explicitly changed

---

# URI Validation

Valid examples:

```text
magnetmemories://journey/new-york-2026

magnetmemories://journey/japan

magnetmemories://journey/summer-road-trip
```

Invalid:

```text
magnetmemories://journey/

magnetmemories://journey/New York

magnetmemories://journey/My Trip!

magnetmemories://trip/new-york
```

---

# NFC Record Format

Write a single NFC Forum URI Record.

No additional records are required.

Avoid:

* custom binary payloads
* proprietary encoding
* JSON payloads
* XML
* multiple NDEF records

Keeping the tag simple improves compatibility.

---

# Android Behaviour

When the tag is scanned:

Android should:

1. detect NDEF
2. identify the URI
3. launch Magnet Memories
4. pass the URI
5. allow the app to resolve the Journey

If the application is already open:

* reuse the existing activity where appropriate
* navigate directly to the requested Journey
* avoid restarting playback unnecessarily

---

# NFC Flow

```text
Physical Tag
      ↓
Android NFC
      ↓
Intent
      ↓
Deep Link Parser
      ↓
Journey Slug
      ↓
Repository Lookup
      ↓
Journey Found?
      ↓
Yes
      ↓
Open Leather Journal
```

---

# Deep Link Parsing

Conceptual parser:

```kotlin
magnetmemories://journey/new-york-2026

↓

scheme = magnetmemories

host = journey

path = new-york-2026
```

The parser must reject malformed URIs safely.

---

# NFC Resolution

Conceptual flow:

```text
Read URI

↓

Validate Scheme

↓

Validate Host

↓

Extract Slug

↓

Lookup Journey

↓

Journey Found?

↓

Launch Playback
```

---

# Lookup

Lookup is performed entirely offline.

Repository query:

```text
slug

↓

Journey

↓

Playback
```

No network request should ever occur.

---

# Unknown Tag

If the slug cannot be found:

Display a calm message.

Example:

> This magnet isn't connected to a journey yet.

Offer:

* Program this magnet
* Return to Workshop

Do not show technical errors.

---

# Deleted Journey

If the Journey existed previously but has been removed:

Display:

> This journey is no longer in your journal.

Offer:

* Reconnect this magnet
* Create a new journey

---

# Invalid URI

If the URI is malformed:

Example:

```text
magnetmemories://xyz
```

Show:

> We couldn't understand this memory card.

Log diagnostic information internally.

---

# Unsupported NFC Content

If another NFC tag is scanned:

Examples:

* business card
* URL
* Wi-Fi tag
* payment tag

Show:

> This isn't a Magnet Memories tag.

Do not attempt to interpret unrelated NFC payloads.

---

# Programming Tags

Programming occurs only inside Workshop.

Flow:

```text
Choose Journey

↓

Program Magnet

↓

Hold Tag Near Tablet

↓

Write URI

↓

Verify

↓

Done
```

The owner should never manually enter the URI.

---

# Write Verification

After writing:

Immediately read the tag again.

Verify:

* NDEF readable
* URI valid
* slug matches selected Journey
* checksum (if implemented)
* no write failure

Only then display:

> Magnet ready.

---

# Rewriting Tags

Owners may reuse tags.

Programming a second time should:

* overwrite previous NDEF record
* verify new record
* update Workshop

No Journey database change is required unless the slug changes.

---

# Tag Locking

Version 1 should **not** permanently lock NFC tags.

Reasons:

* journeys evolve
* magnets may be reused
* mistakes happen

Locking may become an advanced option later.

---

# Multiple Tags

Version 1:

```text
One Journey

↓

One Magnet
```

Future versions may support:

```text
Journey

↓

Kitchen Magnet

↓

Office Magnet

↓

Travel Album Magnet
```

The architecture should allow this later without changing URI format.

---

# NFC Programming Screen

Workshop should display:

Journey title

↓

Current status

↓

Program Magnet

↓

Hold tag near tablet

↓

Verification

↓

Success

Nothing else is required.

---

# Reading During Playback

If another magnet is scanned while playback is active:

Current Journey

↓

New NFC Scan

↓

Gracefully stop playback

↓

Page closes

↓

Open new Journey

The owner should not need to exit manually.

---

# Duplicate Scans

Repeated scans of the same tag within a short period should be ignored.

Suggested debounce:

```text
1000 ms
```

This prevents accidental repeated launches.

---

# NFC State Machine

```kotlin
sealed interface NfcState {

    object Idle

    object WaitingForTag

    object Reading

    object Validating

    object ResolvingJourney

    object LaunchingJourney

    object Writing

    object Verifying

    data class Error(...)
}
```

---

# Programming State Machine

```text
Waiting

↓

Tag Detected

↓

Write URI

↓

Read Back

↓

Verify

↓

Complete
```

Failure returns safely to:

Waiting for Tag

---

# NFC Service

Recommended abstraction:

```kotlin
interface NfcManager {

    suspend fun readTag()

    suspend fun writeJourney()

    suspend fun verifyTag()

}
```

UI should never manipulate Android NFC APIs directly.

---

# Deep Link Resolver

```kotlin
interface JourneyDeepLinkResolver {

    suspend fun resolve(
        uri: Uri
    ): JourneyResolutionResult

}
```

Possible results:

* Success
* JourneyNotFound
* InvalidUri
* UnsupportedTag

---

# Journey Resolution

```kotlin
sealed interface JourneyResolutionResult {

    data class Success(
        val journeyId: JourneyId
    )

    object JourneyNotFound

    object InvalidUri

    object UnsupportedScheme
}
```

---

# NFC Security

The NFC tag is public.

Anyone can read it.

Therefore it must never contain:

* names
* stories
* photographs
* GPS
* personal information

Only the Journey slug.

---

# Slug Guessing

Journey slugs should not expose unnecessary information.

Prefer:

```text
japan-2027
```

Avoid:

```text
kaavya-family-japan-trip-december-2027-with-ira
```

If additional privacy is ever required, Version 2 may replace public slugs with opaque identifiers.

---

# Future Token Support

Possible future format:

```text
magnetmemories://journey/9b3d2ef8
```

The application can internally map the token to the Journey.

Version 1 keeps human-readable slugs for simplicity.

---

# Error Handling

Recoverable:

* unknown Journey
* unreadable tag
* duplicate scan
* cancelled write
* write verification failure

Non-recoverable:

* unsupported NFC hardware
* NFC disabled
* permanently damaged tag

---

# NFC Disabled

If NFC is disabled:

Show:

> NFC is turned off.

Offer:

* Open Settings

Do not continuously prompt.

---

# Unsupported Device

If the tablet lacks NFC hardware:

Workshop should disable magnet programming.

Journey playback remains fully functional.

---

# Logging

Record:

* scan timestamp
* Journey ID
* result
* write success
* verification result

Do not record:

* raw NFC dumps
* owner stories
* personal metadata

---

# Testing

Unit Tests:

* URI parsing
* slug validation
* duplicate scans
* unknown Journey
* malformed URI
* write verification

Integration Tests:

* program tag
* read tag
* overwrite tag
* deleted Journey
* invalid URI
* unsupported tag
* rapid repeated scans

Physical Device Tests:

* NTAG216
* screen off
* screen on
* app closed
* app backgrounded
* Journey already open
* 100 consecutive scans
* read distance
* write reliability

---

# Acceptance Criteria

The NFC system is complete when:

* NTAG216 tags can be programmed
* one tag maps to one Journey
* tags contain only a URI
* scanning launches the correct Journey
* playback begins automatically
* all behaviour works offline
* repeated scans are ignored briefly
* deleted Journeys are handled gracefully
* invalid tags never crash the app
* no personal information is stored on the NFC tag

---

# Future Enhancements

Possible Version 2 features:

* multiple magnets per Journey
* NFC tag alias history
* encrypted identifiers
* desktop NFC writer
* tag artwork printing
* programmable travel souvenirs
* NFC-enabled printed photo books

These enhancements must remain compatible with the Version 1 URI scheme whenever possible.

---

# Final Principle

> **A fridge magnet should never feel like technology. It should feel like picking up a memory and watching a beloved travel journal open itself to exactly the right page.**
