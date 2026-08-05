# System Architecture

**Project:** Magnet Memories
**Document:** System Architecture
**Version:** 1.0
**Status:** Draft

---

# Purpose

This document defines the technical architecture for Magnet Memories Version 1.

It translates the product vision, UX principles and screen specifications into a maintainable Android software design.

The architecture must support the complete core experience:

```text
Tap magnet

↓

Identify journey

↓

Open digital journal

↓

Display title page and Memory Card

↓

Play photographs and Live Photos

↓

Close journal

↓

Return to waiting state
```

The implementation should remain:

* offline-first;
* reliable;
* private;
* testable;
* maintainable;
* extensible;
* suitable for long-term family use.

---

# Architectural North Star

> **A Journey is an ordered sequence of Moments, and an NFC magnet selects which Journey the Memory Player opens.**

This principle defines the core domain model.

A Journey does not directly contain screens, files or slideshow entries.

It contains Moments.

Each Moment represents one meaningful unit in the journal experience.

Examples include:

* Title Page
* Memory Card
* Still Photograph
* Live Photo
* Quote Page
* Closing Page
* Future video
* Future map
* Future ticket
* Future audio memory

The Memory Player is responsible for presenting these Moments in order.

---

# System Context

Magnet Memories operates primarily on one Android tablet.

The tablet provides:

* the colour display;
* the NFC reader;
* local storage;
* media playback;
* touch interaction;
* the application runtime.

The external 1 TB hard drive acts as the master archive.

It is not required during ordinary playback.

```text
┌──────────────────────────────┐
│ External Master Archive      │
│                              │
│ 1 TB Hard Drive              │
│ Original Photos and Videos   │
└──────────────┬───────────────┘
               │
               │ Import through Android
               │ Storage Access Framework
               ▼
┌──────────────────────────────┐
│ HMD T21 Tablet               │
│                              │
│ Magnet Memories App          │
│                              │
│ • NFC Reader                 │
│ • Room Database              │
│ • Local Media Storage        │
│ • Memory Player              │
│ • Workshop                   │
└──────────────────────────────┘
```

---

# Architectural Goals

The architecture must satisfy the following goals.

## Offline Operation

Once imported, every Journey must work without:

* internet access;
* Google Photos;
* cloud storage;
* external hard drives;
* external servers;
* third-party accounts.

---

## Long-Term Reliability

The system should remain understandable and maintainable for many years.

Avoid unnecessary dependencies on:

* proprietary cloud services;
* unstable APIs;
* opaque media formats;
* external runtime infrastructure;
* complex distributed systems.

---

## Clear Separation of Concerns

The architecture should separate:

* domain logic;
* persistence;
* media processing;
* NFC handling;
* playback;
* Workshop administration;
* user interface.

No screen should directly contain storage, database or NFC logic.

---

## Testability

Critical logic must be testable without:

* an NFC tag;
* the physical tablet;
* the external drive;
* real family photographs.

Use interfaces, repositories, fakes and deterministic state models.

---

## Extensibility

Version 1 supports still photographs and Live Photos.

The architecture should allow future Moment types without redesigning the entire application.

Examples include:

* standalone video;
* audio memories;
* maps;
* ticket scans;
* written notes;
* chapter dividers.

---

## Graceful Failure

One damaged file must not prevent the rest of a Journey from playing.

Failures should be isolated at the smallest practical boundary.

---

# Technology Stack

Magnet Memories should use a native Android stack.

Recommended technologies include:

* Kotlin;
* Jetpack Compose;
* Material 3 where appropriate in Workshop;
* Android NFC and NDEF APIs;
* Room;
* DataStore;
* Kotlin coroutines;
* Kotlin Flow;
* WorkManager where appropriate;
* Jetpack Media3;
* Coil or another maintained Compose-compatible image loader;
* Android Storage Access Framework;
* Android app-managed storage;
* dependency injection through Hilt or a lightweight equivalent.

The architecture should not depend on:

* Flutter;
* React Native;
* Firebase;
* a browser-only application;
* a remote backend;
* Google Photos APIs;
* unrestricted file-system access;
* deprecated Android storage permissions.

Exact dependency versions should be selected at implementation time from current stable releases.

---

# Application Structure

A layered architecture is recommended.

```text
┌──────────────────────────────┐
│ Presentation Layer           │
│                              │
│ Compose UI                   │
│ ViewModels                   │
│ UI State                     │
│ Navigation and Mode Control  │
└──────────────┬───────────────┘
               │
┌──────────────▼───────────────┐
│ Domain Layer                 │
│                              │
│ Journey                      │
│ Moment                       │
│ Use Cases                    │
│ Playback Rules               │
│ NFC URI Rules                │
│ Import Rules                 │
└──────────────┬───────────────┘
               │
┌──────────────▼───────────────┐
│ Data Layer                   │
│                              │
│ Repositories                 │
│ Room Database                │
│ DataStore                    │
│ File Storage                 │
│ Media Metadata               │
└──────────────┬───────────────┘
               │
┌──────────────▼───────────────┐
│ Platform and Infrastructure  │
│                              │
│ NFC                          │
│ Media3                       │
│ Storage Access Framework     │
│ WorkManager                  │
│ Android Lifecycle            │
└──────────────────────────────┘
```

Dependencies should generally point inward.

The domain layer should not depend directly on Android UI classes.

---

# Recommended Module Structure

Version 1 may be implemented as a single Gradle application module with strongly separated packages.

If the codebase grows, it may later be split into feature and core modules.

Recommended initial structure:

```text
app/
└── src/main/java/.../
    ├── app/
    │   ├── MagnetMemoriesApplication.kt
    │   ├── MainActivity.kt
    │   └── AppState.kt
    │
    ├── domain/
    │   ├── model/
    │   ├── repository/
    │   ├── usecase/
    │   └── validation/
    │
    ├── data/
    │   ├── database/
    │   ├── repository/
    │   ├── storage/
    │   ├── importer/
    │   └── preferences/
    │
    ├── nfc/
    │   ├── NfcController.kt
    │   ├── NfcParser.kt
    │   ├── NfcWriter.kt
    │   └── NfcModels.kt
    │
    ├── media/
    │   ├── image/
    │   ├── livephoto/
    │   ├── metadata/
    │   ├── playback/
    │   └── processing/
    │
    ├── player/
    │   ├── MemoryPlayer.kt
    │   ├── PlaybackCoordinator.kt
    │   ├── PlaybackState.kt
    │   └── renderer/
    │
    ├── ui/
    │   ├── cover/
    │   ├── journey/
    │   ├── workshop/
    │   ├── onboarding/
    │   ├── components/
    │   └── theme/
    │
    ├── worker/
    │   ├── JourneyImportWorker.kt
    │   └── MediaOptimisationWorker.kt
    │
    └── diagnostics/
        ├── NfcDiagnostics.kt
        └── StorageDiagnostics.kt
```

---

# Core Domain Model

The domain model should describe the family journal rather than Android implementation details.

---

## Journal

The Journal represents the complete local collection.

Version 1 supports one Journal.

The Journal contains many Journeys.

Future versions may support multiple volumes.

```text
Journal

└── Journeys
```

The Journal does not need to be persisted as a dedicated entity in Version 1 unless configuration requires it.

---

## Journey

A Journey represents one travel experience.

Examples:

* New York 2026
* Sweden 2025
* Japan 2028

A Journey contains:

* identity;
* title;
* location;
* dates;
* Memory Card content;
* cover reference;
* ordered Moments;
* NFC-compatible slug;
* playback preferences;
* local storage metadata.

Conceptually:

```kotlin
data class Journey(
    val id: JourneyId,
    val slug: JourneySlug,
    val title: String,
    val location: String?,
    val startDate: LocalDate?,
    val endDate: LocalDate?,
    val memoryCard: MemoryCard,
    val coverMomentId: MomentId?,
    val moments: List<Moment>,
    val createdAt: Instant,
    val updatedAt: Instant
)
```

The persisted Room representation may differ from the pure domain model.

---

## Memory Card

The Memory Card is structured Journey content.

It should not be stored as a synthetic image.

Conceptually:

```kotlin
data class MemoryCard(
    val story: String,
    val favouriteQuote: String?
)
```

The title, location and dates belong to Journey but are displayed alongside the Memory Card.

The Memory Card is generated as a Moment by the playback sequence builder.

---

## Moment

A Moment is the smallest ordered unit presented by the Memory Player.

Conceptually:

```kotlin
sealed interface Moment {
    val id: MomentId
    val journeyId: JourneyId
    val displayOrder: Int
}
```

Version 1 Moment types should include:

```kotlin
data class TitlePageMoment(...)
data class MemoryCardMoment(...)
data class StillPhotoMoment(...)
data class LivePhotoMoment(...)
data class ClosingPageMoment(...)
```

Optional quote pages may also be supported if included in Version 1 scope.

The domain should not model each Moment as an arbitrary unvalidated payload map.

Prefer strongly typed models.

---

# Persisted Model Versus Playback Model

The database should store durable content.

The Memory Player should consume a generated playback sequence.

These are related but not identical.

For example, the database may store:

* one Journey;
* one Memory Card;
* 120 imported media records.

The playback sequence builder may generate:

```text
1. Title Page
2. Memory Card
3. Media Moment 1
4. Media Moment 2
5. Media Moment 3
...
123. Closing Page
```

Title and Closing pages do not necessarily require database rows.

They may be generated at runtime.

This avoids storing purely presentational records as permanent data.

---

# Playback Sequence Builder

Create a dedicated domain component responsible for generating the ordered Journey sequence.

Conceptual interface:

```kotlin
interface JourneySequenceBuilder {
    suspend fun build(journeyId: JourneyId): JourneySequence
}
```

Responsibilities:

* load Journey metadata;
* load ordered media;
* generate Title Page;
* generate Memory Card;
* convert media records into typed Moments;
* append Closing Page;
* exclude unavailable or invalid media where appropriate;
* preserve explicit owner-defined order.

The UI should not construct this sequence directly.

---

# Memory Player Architecture

The Memory Player is the playback engine for Journeys.

It should not be implemented as a simple timer embedded inside a Compose screen.

It should be a stateful coordinator with explicit inputs and outputs.

---

## Responsibilities

The Memory Player should:

* load a Journey sequence;
* track the current Moment;
* advance automatically;
* move backwards and forwards;
* pause and resume;
* coordinate Live Photo playback;
* respond to a new NFC Journey;
* handle missing Moments;
* store temporary resume position;
* expose deterministic UI state;
* return to the Book Cover when complete.

---

## Conceptual State

```kotlin
sealed interface PlayerState {
    data object Closed : PlayerState
    data class Opening(val journeyId: JourneyId) : PlayerState
    data class ShowingTitle(val journeyId: JourneyId) : PlayerState
    data class ShowingMoment(
        val journeyId: JourneyId,
        val sequence: JourneySequence,
        val index: Int,
        val isPaused: Boolean
    ) : PlayerState
    data class Closing(val journeyId: JourneyId) : PlayerState
    data class Error(val recoverable: Boolean) : PlayerState
}
```

The actual implementation may use a more detailed state model.

---

## Playback Coordinator

A `PlaybackCoordinator` should own:

* timers;
* automatic progression;
* pause state;
* active Moment;
* Live Photo completion events;
* page-change commands;
* Journey replacement;
* resume rules.

Compose should observe immutable state through `StateFlow`.

---

## Moment Renderers

Each Moment type should have a renderer.

Conceptual approach:

```kotlin
interface MomentRenderer<T : Moment> {
    @Composable
    fun Render(
        moment: T,
        state: MomentPlaybackState,
        callbacks: MomentCallbacks
    )
}
```

The implementation may use sealed-type dispatch rather than a runtime registry.

The important requirement is that Still Photos and Live Photos do not become mixed into one large screen with unrelated conditional logic.

---

# Live Photo Architecture

A Live Photo is one logical Moment composed of two local files:

* still image;
* motion video.

```text
LivePhotoMoment

├── Still Asset
└── Motion Asset
```

The motion asset must not appear as a separate Journey Moment.

---

## Live Photo Playback Controller

Create a dedicated controller around Media3.

Responsibilities:

* prepare the local motion file;
* play once;
* pause and resume;
* stop when leaving the Moment;
* release resources;
* expose playback state;
* report failures;
* fall back to the still image.

Conceptual state:

```kotlin
sealed interface LivePhotoState {
    data object Still : LivePhotoState
    data object Preparing : LivePhotoState
    data object Playing : LivePhotoState
    data object Completed : LivePhotoState
    data class Failed(val reason: FailureReason) : LivePhotoState
}
```

The Memory Player should advance only according to the Live Photo playback policy.

---

## Player Ownership

Do not create one ExoPlayer instance per imported Live Photo.

Prefer:

* one lifecycle-managed player instance;
* or a small controlled pool only if profiling demonstrates a need.

The player should belong to the Journey playback scope, not to individual database entities.

---

# NFC Architecture

NFC is the primary public navigation mechanism.

The NFC subsystem should be isolated from UI and domain persistence.

---

## NFC URI Format

Use:

```text
magnetmemories://journey/{slug}
```

Example:

```text
magnetmemories://journey/new-york-2026
```

The earlier working format `memoryatlas://trip/...` should no longer be used.

The scheme and host must remain stable once physical tags have been written.

---

## NFC Components

Recommended components:

### NfcController

Coordinates Android NFC lifecycle and reader behaviour.

### NfcParser

Converts NDEF records into validated domain commands.

### NfcWriter

Writes Journey URIs to compatible tags.

### NfcDebouncer

Prevents repeated processing while a tag remains near the device.

### NfcDiagnosticService

Supports antenna-location testing and tag diagnostics.

---

## Parsed Command

NFC parsing should produce a domain-safe result.

```kotlin
sealed interface NfcCommand {
    data class OpenJourney(val slug: JourneySlug) : NfcCommand
    data class Unsupported(val reason: String) : NfcCommand
    data object Empty : NfcCommand
}
```

The parser should not access Room directly.

---

## NFC Journey Resolution Flow

```text
Android NFC Event

↓

NfcController

↓

NfcParser

↓

OpenJourney(slug)

↓

OpenJourneyFromMagnetUseCase

↓

JourneyRepository

↓

PlaybackCoordinator

↓

Journey Opens
```

---

## New Magnet During Playback

A recognised NFC event must take priority over current playback.

Recommended flow:

1. Stop current automatic progression.
2. Stop and release active Live Photo playback.
3. Save temporary resume position if permitted.
4. Enter Closing state.
5. Load new Journey.
6. Enter Opening state.
7. Begin new Journey.

The UI should not show a confirmation dialog.

---

# Journey Repository

The domain layer should access Journey data through a repository interface.

```kotlin
interface JourneyRepository {
    fun observeJourneys(): Flow<List<JourneySummary>>
    fun observeJourney(id: JourneyId): Flow<Journey?>
    suspend fun getJourneyBySlug(slug: JourneySlug): Journey?
    suspend fun createJourney(draft: JourneyDraft): JourneyId
    suspend fun updateJourney(update: JourneyUpdate)
    suspend fun deleteJourney(id: JourneyId)
}
```

The exact methods may evolve, but screens should not call Room DAOs directly.

---

# Moment Repository

```kotlin
interface MomentRepository {
    fun observeMoments(journeyId: JourneyId): Flow<List<StoredMoment>>
    suspend fun reorderMoments(
        journeyId: JourneyId,
        orderedIds: List<MomentId>
    )
    suspend fun removeMoment(id: MomentId)
    suspend fun addImportedMoments(
        journeyId: JourneyId,
        moments: List<ImportedMoment>
    )
}
```

Deleting a Live Photo Moment must remove both managed files.

---

# Media Storage Architecture

Imported media should be copied into app-managed storage.

The database should contain metadata and relative references.

It should not contain image or video bytes.

Recommended logical structure:

```text
files/
└── journeys/
    └── {journey-id}/
        ├── manifest/
        ├── stills/
        ├── motion/
        └── thumbnails/
```

Example:

```text
journeys/
└── 8a7d.../
    ├── stills/
    │   ├── m001.jpg
    │   └── m002.jpg
    ├── motion/
    │   └── m002.mp4
    └── thumbnails/
        ├── m001.webp
        └── m002.webp
```

The exact local motion format may remain MOV if reliably supported or be converted when required.

---

## Storage Abstraction

Use a storage interface.

```kotlin
interface JourneyStorage {
    suspend fun createJourneyArea(journeyId: JourneyId)
    suspend fun writeStill(...)
    suspend fun writeMotion(...)
    suspend fun writeThumbnail(...)
    suspend fun deleteMomentFiles(momentId: MomentId)
    suspend fun deleteJourneyFiles(journeyId: JourneyId)
    suspend fun verifyFile(reference: LocalMediaReference): Boolean
}
```

This keeps file-system operations outside repositories and ViewModels.

---

# Import Architecture

The import pipeline converts an external source folder into durable local Journey content.

It should be modelled as a multi-stage process rather than one large function.

```text
Folder Selection

↓

Discovery

↓

Metadata Extraction

↓

Live Photo Pairing

↓

Validation

↓

Preview

↓

Storage Estimation

↓

User Confirmation

↓

Media Processing

↓

Local File Commit

↓

Database Commit

↓

Verification
```

---

## Import Components

Recommended components include:

### SourceFolderReader

Reads the selected Storage Access Framework tree.

### MediaDiscoveryService

Finds supported files.

### MetadataExtractor

Reads:

* MIME type;
* dimensions;
* EXIF orientation;
* capture time;
* relevant Live Photo identifiers.

### LivePhotoPairingEngine

Pairs still images and motion files.

### ImportPlanner

Creates a deterministic import plan.

### StorageEstimator

Estimates required local space.

### MediaProcessor

Creates display-optimised assets.

### ImportCommitter

Commits local files and database rows.

### ImportVerifier

Confirms that imported content can be read.

---

# Import Transaction Strategy

File storage and Room cannot participate in one native atomic transaction.

Therefore, the import process must implement an application-level transaction.

Recommended strategy:

1. Create a temporary import workspace.
2. Process media into temporary files.
3. Track every generated file in an import journal.
4. Validate processed files.
5. Start Room transaction.
6. Create Journey and Moment rows.
7. Move or promote temporary files into final locations.
8. Mark import as completed.
9. Clean up the temporary workspace.

If any stage fails:

* roll back database changes where possible;
* remove temporary files;
* remove partially promoted files;
* record a recoverable failure;
* preserve original source files.

The import process must be restart-safe.

---

# Background Work

Large imports should not depend on an Activity remaining open.

Use WorkManager or another lifecycle-safe background mechanism where appropriate.

Work should support:

* progress updates;
* cancellation;
* retry for transient failures;
* cleanup after failure;
* recovery after process death.

Do not use background execution for NFC-triggered playback.

Playback must remain immediate and foreground-controlled.

---

# Room Database Architecture

Room should store:

* Journey metadata;
* media metadata;
* ordering;
* file references;
* import state;
* resume state where appropriate;
* NFC slug association.

Suggested entities will be defined in the Data Model document.

DAOs should be small and focused.

Recommended DAO groups:

* JourneyDao;
* MomentDao;
* ImportDao;
* PlaybackStateDao where durable playback state is required.

Avoid one global DAO with unrelated operations.

---

# DataStore Architecture

Use DataStore for lightweight preferences.

Examples:

* default photo duration;
* Memory Card duration;
* Live Photo mode;
* Live Photo audio enabled;
* reduced motion override;
* onboarding completed;
* Book Cover instruction hidden;
* owner PIN configuration;
* resume timeout;
* evening brightness preference.

Do not store Journey collections or Moment metadata in DataStore.

---

# Application Modes

The application has three top-level modes:

```kotlin
sealed interface AppMode {
    data object BookCover : AppMode
    data class Journey(val journeyId: JourneyId) : AppMode
    data object Workshop : AppMode
}
```

A central `AppState` or coordinator should control mode changes.

Mode changes should not be scattered across individual composables.

---

# Navigation Architecture

Traditional navigation is used only where necessary.

---

## Public Experience

Normal use has no journey list and no browsing navigation.

Flow:

```text
Book Cover

↓

NFC event

↓

Journey

↓

Book Cover
```

---

## Workshop

Workshop may use Navigation Compose for:

* Workshop dashboard;
* Journey manager;
* Journey editor;
* import flow;
* NFC writing;
* preferences;
* storage management.

Workshop navigation should remain isolated from Journey playback navigation.

---

# Entering Workshop

Workshop should require an intentional owner action.

Possible Version 1 entry method:

* long press on a concealed or defined cover area;
* followed by owner PIN.

The exact gesture must be documented in the Screen Specification and tested for discoverability versus accidental access.

NFC magnet scanning must continue to work while on the Book Cover.

---

# ViewModel Boundaries

Recommended ViewModels:

* BookCoverViewModel;
* JourneyPlayerViewModel;
* WorkshopHomeViewModel;
* JourneyEditorViewModel;
* JourneyImportViewModel;
* MagnetWriterViewModel;
* PreferencesViewModel;
* StorageViewModel;
* NfcDiagnosticsViewModel.

Avoid one application-wide ViewModel containing all state.

Shared global state should be limited to:

* current AppMode;
* active Journey command;
* critical application events.

---

# Use Cases

Domain behaviour should be expressed through focused use cases.

Examples:

```text
OpenJourneyFromMagnet
BuildJourneySequence
CreateJourney
UpdateJourneyMemoryCard
ImportJourney
RemoveJourneyFromTablet
WriteJourneyToMagnet
VerifyMagnet
ResumeJourney
RestartJourney
EstimateJourneyStorage
PairLivePhotos
```

Use cases should contain business rules but not Compose UI logic.

---

# Error Architecture

Errors should be classified.

Recommended categories:

```kotlin
sealed interface AppError {
    data class RecoverableMediaError(...)
    data class ImportError(...)
    data class StorageError(...)
    data class NfcError(...)
    data class DatabaseError(...)
    data class UnsupportedFormat(...)
    data class CorruptContent(...)
}
```

Do not display raw exceptions to users.

Map technical failures to user-facing language at the presentation boundary.

---

# Error Boundaries

Use separate failure boundaries for:

* application startup;
* NFC parsing;
* Journey resolution;
* Journey sequence building;
* individual Moment rendering;
* Live Photo playback;
* import;
* database writes;
* file deletion.

An error in one Moment should not crash the entire Memory Player.

---

# Logging

Logging should support debugging without exposing private family data.

Do not log:

* full local file paths;
* image filenames where avoidable;
* Memory Card text;
* NFC raw payloads containing unexpected data;
* EXIF GPS coordinates;
* personally identifying metadata.

Logs may include:

* Journey IDs;
* Moment IDs;
* error categories;
* operation durations;
* import counts;
* anonymised state transitions.

Production logging should be minimal.

---

# Privacy Architecture

The system should not perform automatic network operations.

Version 1 should require no internet permission unless a specific justified feature needs it.

All family content should remain local.

Original external files must remain unchanged.

The application should not retain unrestricted long-term access to source folders unless the owner explicitly chooses that behaviour.

---

# Security Boundaries

Workshop is the only mode that allows data modification.

Journey mode must not expose:

* edit controls;
* delete controls;
* file locations;
* tag-writing actions;
* preferences.

Owner protection may begin with a simple local PIN.

The PIN should not be treated as high-security authentication.

Its purpose is to prevent accidental or casual access.

---

# Lifecycle Behaviour

The architecture must explicitly handle Android lifecycle events.

---

## Process Restart

After process restart:

* initialise database;
* verify critical storage availability;
* return to Book Cover;
* restore preferences;
* enable NFC handling.

Do not automatically resume a Journey after a cold restart in Version 1.

---

## App Backgrounded

During Journey playback:

* pause Live Photo motion;
* pause automatic progression;
* retain current position temporarily.

When foregrounded:

* resume according to player state and timeout rules.

---

## Screen Sleep

During Journey mode:

* keep the screen awake.

During Book Cover:

* follow configured idle and screen-off preferences.

During Workshop:

* use ordinary Android behaviour unless an import is running.

---

# Resume Architecture

Temporary Journey position may be retained.

Recommended model:

```kotlin
data class JourneyResumeState(
    val journeyId: JourneyId,
    val momentId: MomentId,
    val savedAt: Instant
)
```

Rules:

* reopening the same Journey within the configured timeout may resume;
* after timeout, restart at Title Page;
* opening a different Journey does not delete the previous temporary state immediately;
* completed Journeys clear their resume state;
* the owner may disable resume behaviour.

The exact user experience should remain invisible.

---

# Performance Architecture

The application should optimise for fast NFC-to-title-page response.

---

## NFC Opening Strategy

When a Journey slug is received:

1. Resolve the Journey immediately.
2. show Opening state;
3. load essential Journey metadata;
4. prepare the first Moments;
5. begin title sequence;
6. load later media lazily.

Do not block opening while every photograph is decoded.

---

## Media Loading

Use:

* thumbnails for Workshop;
* display-optimised images for playback;
* bounded prefetching;
* image-loader memory and disk caching;
* one current and one next Live Photo preparation where practical.

Avoid:

* decoding full camera originals;
* loading the entire Journey into memory;
* creating one video player per Moment;
* unbounded bitmap caching.

---

# Data Integrity

The system must maintain consistency between:

* Room records;
* local still files;
* local motion files;
* thumbnails;
* ordering;
* Journey state.

Recommended integrity mechanisms:

* foreign keys;
* unique Journey slug constraint;
* unique display order within a Journey where practical;
* import-state tracking;
* local-file verification;
* cleanup jobs for abandoned temporary files;
* migration tests.

---

# File Reference Strategy

Persist relative app-managed references rather than absolute paths where practical.

For example:

```text
journeys/{journeyId}/stills/{momentId}.jpg
```

A storage resolver should convert references into runtime files.

This improves portability if the app-managed root changes.

---

# Dependency Injection

Use dependency injection to provide:

* repositories;
* DAOs;
* storage services;
* import components;
* NFC components;
* playback controllers;
* sequence builders;
* clocks and dispatchers for testing.

Do not instantiate repositories or databases directly inside ViewModels.

---

# Coroutine Dispatchers

Inject coroutine dispatchers or a dispatcher provider.

Conceptual interface:

```kotlin
interface AppDispatchers {
    val main: CoroutineDispatcher
    val io: CoroutineDispatcher
    val default: CoroutineDispatcher
}
```

Use:

* Main for UI state;
* IO for file and database work;
* Default for CPU-heavy image or metadata processing.

This also improves testability.

---

# Clock Abstraction

Time affects:

* resume timeout;
* import timestamps;
* playback timing;
* cache behaviour.

Use an injectable clock where practical.

This allows deterministic tests.

---

# Testing Architecture

The architecture should support several testing levels.

---

## Unit Tests

Test:

* NFC parsing;
* Journey slug validation;
* sequence building;
* resume rules;
* playback state transitions;
* Live Photo pairing;
* import planning;
* storage estimation;
* error mapping.

---

## Repository Tests

Use an in-memory Room database and temporary file storage.

Test:

* Journey creation;
* ordering;
* deletion;
* rollback behaviour;
* missing-file handling;
* Live Photo paired-file deletion.

---

## UI Tests

Use fake repositories and playback controllers.

Test:

* Book Cover;
* Journey opening;
* Memory Card progression;
* pause and resume;
* unknown magnet handling;
* Workshop access;
* import preview.

---

## Physical Device Tests

The following require the HMD T21:

* NFC detection;
* antenna location;
* tag writing;
* NFC debounce;
* HEIC decoding;
* Live Photo codec playback;
* page-turn performance;
* long-running Book Cover behaviour;
* storage performance;
* immersive mode.

---

# Future Extension Points

The architecture should allow future growth without prematurely implementing it.

---

## Additional Moment Types

Future Moment types should be addable through:

* a new domain model;
* persistence support;
* a renderer;
* optional import support;
* sequence-builder integration.

---

## Multiple Volumes

The future data model may become:

```text
Journal

└── Volumes

    └── Journeys

        └── Moments
```

Version 1 should not implement Volume behaviour.

Avoid assumptions that make it impossible later.

---

## Cloud Backup

Cloud backup is outside Version 1.

Repositories and storage abstractions should not be distorted merely to support hypothetical cloud synchronisation.

Offline simplicity remains the priority.

---

## Multiple Devices

Multi-device synchronisation is outside Version 1.

The local-first model should remain authoritative.

---

# Architectural Constraints

The following constraints are intentional.

* No backend server.
* No mandatory internet.
* No Google Photos integration.
* No journey browsing during normal use.
* No full-resolution master archive on the tablet.
* No public social features.
* No automatic uploads.
* No general-purpose video gallery in Version 1.
* No arbitrary dynamic plugin system for Moment types.
* No microservices.
* No cross-platform framework.

---

# Architectural Decision Records

Important technical choices should be recorded in:

```text
docs/adr/
```

Suggested initial ADRs:

```text
ADR-001-Native-Android.md
ADR-002-Offline-First.md
ADR-003-Journey-and-Moment-Domain.md
ADR-004-App-Managed-Media-Storage.md
ADR-005-NFC-URI-Format.md
ADR-006-Live-Photo-Representation.md
ADR-007-Room-and-DataStore.md
ADR-008-Single-Activity-Compose.md
```

Each ADR should describe:

* context;
* decision;
* alternatives;
* consequences.

---

# Recommended Implementation Sequence

The architecture should be implemented incrementally.

## Stage 1 — Application Skeleton

* Compose application;
* theme;
* Book Cover;
* Workshop shell;
* dependency injection;
* Room setup;
* DataStore setup.

---

## Stage 2 — Core Domain

* Journey model;
* Moment model;
* repositories;
* sequence builder;
* playback state machine.

---

## Stage 3 — Still Photo Journey

* folder import;
* image processing;
* local storage;
* still-photo playback;
* title and Memory Card;
* closing sequence.

---

## Stage 4 — NFC

* URI parser;
* NFC reading;
* Journey resolution;
* unknown-tag handling;
* tag writing;
* verification;
* diagnostics.

---

## Stage 5 — Live Photos

* HEIC support;
* metadata extraction;
* still-motion pairing;
* local motion storage;
* Media3 playback;
* fallback behaviour.

---

## Stage 6 — Reliability

* import rollback;
* low-storage handling;
* process-death recovery;
* integrity checks;
* deletion;
* test coverage.

---

## Stage 7 — Visual Completion

* cover animation;
* page-turn animation;
* reduced motion;
* long-duration device testing;
* physical-frame safe areas.

---

# Acceptance Criteria

The architecture is considered successfully implemented when:

* UI code does not directly access Room or the file system;
* NFC parsing is independent from Journey lookup;
* Journeys are modelled as ordered Moments;
* title and closing pages can be generated by the sequence builder;
* still photographs and Live Photos use distinct typed models;
* the Memory Player exposes deterministic state;
* import work can survive Activity recreation;
* imported Journeys play entirely offline;
* local deletion never modifies the master archive;
* one corrupt Moment does not terminate the Journey;
* major domain rules can be tested without the physical tablet;
* future Moment types can be added without rewriting the entire player;
* normal use requires no journey list or digital browsing interface.

---

# Final Principle

> **The architecture should be invisible for the same reason the interface is invisible: the family should experience the story, not the machinery preserving it.**
