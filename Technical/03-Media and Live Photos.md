# Media and Live Photos

**Project:** Magnet Memories
**Document:** Media and Live Photos
**Version:** 1.0
**Status:** Draft

---

# Purpose

This document defines how Magnet Memories discovers, validates, imports, processes, stores and plays visual media.

It covers:

* supported image formats;
* HEIC and HEIF handling;
* Apple Live Photo representation;
* still-and-motion pairing;
* metadata extraction;
* media validation;
* image optimisation;
* video compatibility;
* transcoding;
* thumbnail generation;
* playback;
* error recovery;
* privacy;
* device testing.

Media processing is one of the most important technical areas in Magnet Memories because the application must preserve the emotional quality of family photographs while remaining reliable on a permanently mounted Android tablet.

---

# Media Principles

All media handling should follow these principles.

## Preserve the Memory

Processing should never noticeably damage a photograph or Live Photo.

The purpose of optimisation is to make media reliable and efficient on the tablet, not to reduce quality unnecessarily.

---

## Preserve the Original

Files on the external archive must never be modified.

All conversions, resizing, metadata handling and transcoding must produce new app-managed files.

---

## Optimise for the Display

The HMD T21 does not need to decode full-resolution camera originals during every page turn.

Imported files should be optimised for:

* the target display;
* smooth playback;
* predictable memory use;
* offline availability;
* acceptable storage usage.

---

## Treat Live Photos as One Memory

A Live Photo is one logical `Moment`.

Its still image and motion component must never appear as separate pages.

---

## Fail Gracefully

An unsupported, damaged or missing media component must not terminate an entire Journey.

The application should show the best available representation and continue.

---

## Prefer Native Compatibility

Use Android platform decoding and Jetpack Media3 where they provide reliable support.

Add custom conversion or third-party decoding only when target-device testing demonstrates a real compatibility gap.

---

# Supported Media in Version 1

Version 1 supports the following still-image formats:

| Format | Common Extensions | Version 1 Requirement |
| ------ | ----------------- | --------------------- |
| JPEG   | `.jpg`, `.jpeg`   | Required              |
| PNG    | `.png`            | Required              |
| WebP   | `.webp`           | Required              |
| HEIC   | `.heic`           | Required              |
| HEIF   | `.heif`           | Required              |

Version 1 also supports Apple Live Photos exported as:

* one still-image file;
* one corresponding motion-video file.

Standalone video playback is outside Version 1.

A motion video is imported only when it is confidently associated with a Live Photo.

---

# Android HEIC and HEIF Support

Android platform documentation lists HEIF decoding support from Android 8.0 onward, including `.heic` and `.heif` files. Actual reliability may still depend on device codecs, image profile and vendor implementation, so the HMD T21 must be tested with real iPhone files before the import strategy is finalised.

Android's `ImageDecoder` API can decode images from app files or `ContentResolver` URIs and can be configured for target sizing during decoding. Decoding may be expensive and should run away from the main thread.

Magnet Memories should therefore use a capability-based approach:

1. Inspect the file.
2. Attempt platform decoding.
3. Verify the decoded dimensions and visual output.
4. Create an app-managed display copy.
5. Fall back to a controlled conversion path if the device cannot decode the source reliably.

The application must not assume that the filename alone proves the file is decodable.

---

# Apple Live Photo Representation

Apple describes a Live Photo as a still image paired with a short movie containing motion and sound around the captured moment. When an unmodified Live Photo is exported from Apple Photos on macOS, it is exported as two separate files: a still-image file and a video file.

The Live Photo movie normally contains a QuickTime content identifier that associates it with a corresponding identifier in the still image's metadata.

Magnet Memories should preserve this conceptual structure:

```text
Live Photo Moment
├── Still component
└── Motion component
```

The still component is the primary representation.

The motion component enriches it.

If the motion component cannot be played, the still photograph remains a valid memory.

---

# Export Expectations

The most reliable source workflow is:

1. Export original or unmodified media from Apple Photos where possible.
2. Preserve both the still and motion files.
3. Keep paired files in the same Journey source folder.
4. Avoid renaming only one component of a pair.
5. Copy the folder to the external master archive.
6. Import the folder into Magnet Memories.

Exporting through other services may:

* convert HEIC to JPEG;
* remove Live Photo metadata;
* omit the motion component;
* rename components independently;
* flatten a Live Photo into a standalone video;
* preserve only the still image.

The importer must therefore support both:

* fully identifiable Live Photo pairs;
* less complete exports requiring filename or timestamp fallback matching.

---

# Source Folder Example

```text
new-york-2026/
├── trip.json
└── photos/
    ├── IMG_1001.HEIC
    ├── IMG_1001.MOV
    ├── IMG_1002.JPG
    ├── IMG_1003.HEIC
    ├── IMG_1003.MOV
    ├── IMG_1004.PNG
    └── IMG_1005.WEBP
```

Expected imported Moments:

```text
Moment 1 — Live Photo
    IMG_1001.HEIC
    IMG_1001.MOV

Moment 2 — Still Photo
    IMG_1002.JPG

Moment 3 — Live Photo
    IMG_1003.HEIC
    IMG_1003.MOV

Moment 4 — Still Photo
    IMG_1004.PNG

Moment 5 — Still Photo
    IMG_1005.WEBP
```

The Journey should contain five Moments, not seven source files.

---

# Media Discovery

The importer should not trust file extensions alone.

For each source document, discovery should inspect:

* display name;
* extension;
* reported MIME type;
* magic bytes or container signature where practical;
* file size;
* readability;
* metadata availability.

---

## Candidate Still Extensions

Recognise, case-insensitively:

```text
.jpg
.jpeg
.png
.webp
.heic
.heif
```

---

## Candidate Motion Extensions

Recognise, case-insensitively:

```text
.mov
.mp4
.m4v
```

A candidate motion file must not automatically become a Live Photo motion component.

It must pass pairing and media validation.

---

## Ignored Files

Ignore safely:

* hidden operating-system files;
* thumbnails generated by desktop applications;
* sidecar files not used by Version 1;
* unsupported RAW files;
* unrelated documents;
* standalone videos;
* duplicate temporary files;
* zero-byte files.

Examples may include:

```text
.DS_Store
Thumbs.db
._IMG_1001.HEIC
```

Ignored files should appear in an optional import report when useful, but they should not block the Journey.

---

# Media Discovery Result

Conceptual model:

```kotlin
sealed interface DiscoveredMedia {

    data class StillCandidate(
        val source: SourceDocument,
        val detectedFormat: StillFormat,
        val metadata: PreliminaryMetadata
    ) : DiscoveredMedia

    data class MotionCandidate(
        val source: SourceDocument,
        val detectedFormat: MotionFormat,
        val metadata: PreliminaryMetadata
    ) : DiscoveredMedia

    data class Unsupported(
        val source: SourceDocument,
        val reason: UnsupportedReason
    ) : DiscoveredMedia

    data class Unreadable(
        val source: SourceDocument,
        val reason: ReadFailure
    ) : DiscoveredMedia
}
```

Discovery should not copy or convert files.

It should build a safe description of the source folder.

---

# Metadata Extraction

Metadata extraction should be isolated behind a dedicated interface.

```kotlin
interface MediaMetadataExtractor {
    suspend fun inspectStill(
        source: SourceDocument
    ): StillMetadataResult

    suspend fun inspectMotion(
        source: SourceDocument
    ): MotionMetadataResult
}
```

---

# Still Metadata

Extract where available:

* detected format;
* MIME type;
* pixel width;
* pixel height;
* orientation;
* capture timestamp;
* camera make and model;
* Live Photo content identifier;
* colour profile;
* HDR characteristics;
* file size.

Version 1 does not need to store camera make or model after import.

They may be used temporarily for diagnostics.

---

# Motion Metadata

Extract where available:

* container format;
* video codec;
* audio codec;
* width;
* height;
* duration;
* frame rate;
* rotation metadata;
* creation timestamp;
* QuickTime content identifier;
* still-image-time metadata;
* file size.

Apple's Live Photo motion asset contains metadata that identifies the corresponding still asset and a timed metadata sample corresponding to the still-image position in the movie.

Version 1 does not have to reproduce Apple's exact still-image timing behaviour during playback, but it should retain extracted timing metadata when available so that future playback improvements remain possible.

---

# Metadata Safety

Metadata parsing must be treated as untrusted input.

Requirements:

* bound string lengths;
* catch malformed metadata;
* reject impossible dimensions;
* reject negative duration;
* avoid loading whole files into memory unnecessarily;
* never execute metadata content;
* sanitise log output;
* do not trust embedded filenames or paths.

A metadata extraction failure should not automatically reject an otherwise decodable still image.

---

# Orientation

Correct orientation is mandatory.

The application should:

1. read orientation metadata;
2. decode the source correctly;
3. render the resulting image upright;
4. create the local display copy with a normalised orientation;
5. record the final display dimensions.

Preferred local strategy:

* rotate pixels during import when creating the app-managed display copy;
* store the optimised file in its final upright orientation;
* set the stored orientation to zero unless retention is required for diagnostics.

This avoids repeating orientation logic during every page display.

The original archive file remains untouched.

---

# Capture Dates

Capture dates may help with:

* initial suggested ordering;
* duplicate detection;
* pairing;
* import preview;
* future timeline features.

Possible sources include:

* EXIF original date;
* HEIF metadata;
* QuickTime creation date;
* file-system modification date.

Priority should be documented and deterministic.

Suggested order:

1. reliable embedded capture date;
2. paired component metadata;
3. file creation or modification date;
4. no date.

File-system dates should be treated as weak evidence because copying and exporting may alter them.

---

# GPS Metadata

Version 1 does not require precise GPS coordinates.

During import:

* GPS may be read temporarily if required for future-compatible metadata inspection;
* it should not be persisted by default;
* it should not be logged;
* it should not be embedded into converted display copies unless explicitly retained.

This follows the project's privacy-by-minimisation principle.

A future map feature must introduce an explicit owner-facing GPS retention policy.

---

# Live Photo Pairing

Pairing is a staged decision process.

The importer should use the strongest available evidence first.

Recommended priority:

1. embedded content identifier;
2. explicit source manifest;
3. exact normalised base filename;
4. compatible capture timestamp;
5. additional media plausibility checks;
6. owner confirmation.

---

# Pairing Stage 1 — Embedded Identifier

The strongest automatic method is matching:

* the Live Photo identifier in the still image;
* the QuickTime content identifier in the motion file.

When both identifiers are available and equal:

```text
pairingMethod = CONTENT_IDENTIFIER
pairingConfidence = HIGH
```

The importer should still validate that both files are readable.

---

# Pairing Stage 2 — Source Manifest

A future or manually prepared source manifest may explicitly associate two files.

Example:

```json
{
  "livePhotos": [
    {
      "still": "IMG_1001.HEIC",
      "motion": "IMG_1001.MOV"
    }
  ]
}
```

Manifest associations must be validated.

A manifest must never be allowed to reference files outside the selected Journey folder.

---

# Pairing Stage 3 — Filename

Normalise filenames by:

* removing the extension;
* applying case-insensitive comparison;
* normalising Unicode;
* removing common export suffixes only when documented.

Examples:

```text
IMG_1001.HEIC
IMG_1001.MOV
```

This is a strong candidate pair.

Potential export variations may include:

```text
IMG_1001.HEIC
IMG_1001_1.MOV
```

Such suffix rules should be conservative.

Do not pair based on a partial substring such as `IMG_10`.

Filename pairing should generally result in:

```text
pairingMethod = FILE_NAME
pairingConfidence = MEDIUM
```

unless reinforced by matching timestamps and plausible duration.

---

# Pairing Stage 4 — Timestamp

Timestamp pairing is a fallback.

Possible rule:

* still and motion capture times differ by no more than a small configured tolerance;
* no other equally plausible candidate exists;
* motion duration is plausible;
* image and motion dimensions are reasonably compatible;
* filenames are not contradictory.

Suggested initial tolerance:

```text
±3 seconds
```

The tolerance must be configurable in code and validated with real exports.

Timestamp-only pairing should never produce `HIGH` confidence.

---

# Pairing Stage 5 — Plausibility

A motion candidate should be plausible as a Live Photo component.

Possible checks:

* short duration;
* has a video track;
* dimensions are compatible with the still;
* creation time is close;
* metadata identifies a still-image time;
* codec is decodable or transcodable;
* no other still is a stronger match.

Do not use one universal hard duration limit as the sole pairing criterion.

Edited or exported Live Photos may differ from typical capture duration.

---

# Pairing Ambiguity

When two or more stills could plausibly match one motion file, the importer must not choose arbitrarily.

Result:

```kotlin
data class AmbiguousPairing(
    val motion: MotionCandidate,
    val stillCandidates: List<StillCandidate>,
    val evidence: List<PairingEvidence>
)
```

The import preview should allow the owner to:

* choose the correct still;
* import the still without motion;
* ignore the motion file.

---

# Unmatched Motion Files

An unmatched motion file should:

* not become a standalone Journey page;
* appear in the import report;
* remain untouched in the source archive;
* not fail the Journey import;
* optionally be available for manual association.

Example message:

> One motion file could not be matched to a photograph and will not be added.

---

# Missing Motion Component

When a still photograph contains Live Photo metadata but no motion file is present:

* import it as a Still Photo;
* record an optional diagnostic note;
* do not show a playback error during the Journey;
* do not represent it as a broken Live Photo.

---

# Missing Still Component

A motion file without a still component is not a Version 1 Moment.

It should remain unmatched.

The importer must not generate an arbitrary still frame unless the owner explicitly requests a future recovery feature.

---

# Pairing Engine

Conceptual interface:

```kotlin
interface LivePhotoPairingEngine {

    suspend fun pair(
        stills: List<StillCandidate>,
        motions: List<MotionCandidate>
    ): LivePhotoPairingResult
}
```

Conceptual result:

```kotlin
data class LivePhotoPairingResult(
    val pairs: List<LivePhotoPair>,
    val unpairedStills: List<StillCandidate>,
    val unpairedMotions: List<MotionCandidate>,
    val ambiguous: List<AmbiguousPairing>
)
```

The pairing engine must be deterministic.

The same source set and configuration should produce the same plan.

---

# Pairing Evidence

```kotlin
sealed interface PairingEvidence {

    data class MatchingIdentifier(
        val identifierHash: String
    ) : PairingEvidence

    data object MatchingBaseName : PairingEvidence

    data class TimestampDifference(
        val milliseconds: Long
    ) : PairingEvidence

    data class CompatibleDimensions(
        val stillWidth: Int,
        val stillHeight: Int,
        val motionWidth: Int,
        val motionHeight: Int
    ) : PairingEvidence

    data class PlausibleDuration(
        val durationMs: Long
    ) : PairingEvidence
}
```

Do not store the full embedded identifier in production logs.

A hash or redacted representation is sufficient for diagnostics.

---

# Pairing Confidence

Suggested rules:

## High

Use when:

* embedded identifiers match;
* or an explicit validated manifest associates the pair.

## Medium

Use when:

* base filenames match;
* timestamps are compatible;
* no ambiguity exists;
* media characteristics are plausible.

## Manually Confirmed

Use when:

* the owner resolves an ambiguous pairing.

Low-confidence automatic pairing should not be committed without confirmation.

---

# Source Validation

Before processing a source still:

* open the stream;
* inspect format;
* decode bounds or header;
* verify positive dimensions;
* attempt a controlled decode;
* verify orientation handling;
* reject unreasonable memory requirements.

Before processing motion:

* inspect tracks;
* verify a video track exists;
* read duration;
* read dimensions;
* inspect codec;
* check for audio;
* attempt a short prepare operation where practical.

---

# Still Image Processing

The still-image pipeline should create:

1. a playback-quality display image;
2. a smaller Workshop thumbnail.

The source original remains only in the external archive.

---

# Playback Image Target

The playback copy should be large enough for:

* full-screen landscape display;
* portrait display with margins;
* modest page-turn scaling;
* future small zoom or pan if introduced.

It should not retain unnecessary multi-megapixel resolution.

Recommended strategy:

* determine the tablet's physical display resolution at runtime;
* generate a target whose long edge is approximately the display's long edge multiplied by a modest quality margin;
* avoid upscaling smaller originals;
* preserve aspect ratio.

Suggested starting quality margin:

```text
1.25× to 1.5× display pixel dimensions
```

Final values must be selected after HMD T21 testing.

---

# Maximum Decode Bounds

The importer should enforce a defensive upper bound for decoded pixel count.

This protects against:

* extremely large panoramas;
* corrupt dimension metadata;
* decompression bombs;
* accidental RAW-like exports.

The exact limit should be chosen through memory profiling.

Large panoramas may need tiled or sampled decoding rather than outright rejection.

---

# Image Processing Pipeline

```text
Source image
    ↓
Format inspection
    ↓
Metadata extraction
    ↓
Orientation resolution
    ↓
Sampled decode
    ↓
Colour conversion where required
    ↓
Resize
    ↓
Encode display copy
    ↓
Decode verification
    ↓
Generate thumbnail
    ↓
Commit temporary output
```

Each stage should report a structured failure.

---

# Local Still Format

Version 1 should favour a widely supported local display format.

Recommended default:

```text
JPEG for opaque photographs
```

Potential alternatives:

* WebP for thumbnails;
* PNG for source images requiring transparency;
* retaining HEIC when device testing proves decoding is consistently reliable.

For family photographs, converting imported HEIC to a high-quality JPEG display copy may simplify playback and reduce device-specific risk.

The original HEIC remains in the master archive.

Suggested policy:

| Source                           | Local Playback Copy                                      |
| -------------------------------- | -------------------------------------------------------- |
| JPEG                             | Optimised JPEG                                           |
| HEIC/HEIF                        | High-quality JPEG unless reliable retention is preferred |
| PNG without transparency         | Optimised JPEG or PNG                                    |
| PNG with meaningful transparency | PNG                                                      |
| WebP                             | Optimised WebP or JPEG after testing                     |

The final decision should be documented in an ADR after HMD T21 testing.

---

# JPEG Encoding

When JPEG is selected:

* use high quality;
* avoid repeated recompression;
* remove unused embedded previews;
* normalise orientation;
* retain colour fidelity;
* verify output by decoding it.

Suggested initial quality:

```text
90–94
```

The exact value should be selected by comparing:

* visible quality;
* file size;
* import speed;
* page-turn performance.

Do not choose quality based only on numeric compression ratios.

---

# Colour Space

The importer should detect and handle colour profiles carefully.

Version 1 should aim for a consistent standard dynamic range output suitable for the tablet.

Possible concerns include:

* Display P3 iPhone photographs;
* HDR HEIF images;
* unsupported embedded profiles;
* tone mapping.

Requirements:

* avoid severe colour shifts;
* avoid washed-out images;
* test skin tones, skies and saturated colours;
* record when HDR content is converted to SDR;
* use platform-supported colour management where reliable.

HDR preservation is not a hard Version 1 requirement.

Reliable, visually faithful SDR output is preferable to inconsistent HDR handling.

---

# Transparency

JPEG does not support transparency.

For source PNG or WebP files with meaningful transparency:

* preserve transparency in PNG or WebP;
* render against the intended paper background;
* verify edges and alpha quality.

Ordinary iPhone photographs will normally be opaque.

---

# Thumbnail Generation

Workshop should never load playback-sized images for every Journey card.

Generate thumbnails during import.

Suggested thumbnail properties:

* approximately 320–640 pixels on the long edge;
* preserve aspect ratio;
* WebP or JPEG;
* sufficient quality for Workshop cards;
* no embedded private metadata;
* normalised orientation.

Thumbnails should be regenerated if:

* their source display copy changes;
* the format becomes unreadable;
* a future schema changes thumbnail requirements.

---

# Motion Compatibility

Live Photo motion is commonly stored in a QuickTime-compatible movie container.

Container compatibility does not guarantee codec compatibility.

The importer must inspect:

* video codec;
* audio codec;
* profile;
* level;
* dimensions;
* frame rate;
* duration.

Jetpack Media3 is the recommended Android media stack for local playback. Media3 includes ExoPlayer for audio and video playback and Transformer for media transformation workflows.

---

# Motion Playback Strategy

Preferred order:

1. Attempt direct local playback using Media3.
2. Confirm prepare succeeds.
3. Confirm initial frames render.
4. Confirm audio is supported.
5. Retain the motion file if reliable.
6. Transcode only when necessary.

Avoid transcoding every motion component automatically if the source plays reliably.

Unnecessary transcoding increases:

* import time;
* power use;
* storage writes;
* generation loss;
* implementation complexity.

---

# Compatible Transcoding

Android provides system-level compatible media transcoding in some contexts on Android 12 and later, but Magnet Memories should not depend on implicit system behaviour for its permanent app-managed files. The application needs deterministic offline compatibility after import.

When conversion is necessary, use a controlled import-time pipeline.

Recommended compatible target:

* MP4 container;
* AVC/H.264 video;
* AAC audio;
* original or sensibly reduced resolution;
* original frame rate where practical;
* preserved rotation;
* moderate bitrate suitable for short clips.

The exact target profile must be tested on the HMD T21.

---

# Media3 Transformer

If Media3 Transformer is selected for motion conversion, encapsulate it behind an application interface.

```kotlin
interface MotionTranscoder {
    suspend fun transcode(
        source: SourceDocument,
        target: TemporaryMediaTarget,
        profile: MotionCompatibilityProfile
    ): MotionTranscodeResult
}
```

The domain and import planner should not depend on Media3 classes directly.

---

# Transcoding Rules

Transcode when:

* direct playback preparation fails;
* the codec is unsupported;
* the source profile is unreliable on the target;
* rotation renders incorrectly;
* audio is unsupported;
* the source file is disproportionately large;
* device testing identifies stutter.

Do not transcode merely because the source uses a MOV extension.

---

# Motion Optimisation

Live Photo motion clips are short, but many clips can consume significant storage.

Optimisation should balance:

* visual fidelity;
* audio quality;
* storage;
* decoding efficiency;
* import time.

Potential processing:

* preserve source resolution when close to display needs;
* reduce very high resolution to a tablet-appropriate target;
* preserve aspect ratio;
* avoid increasing frame rate;
* preserve audio unless the owner chooses mute-only imports;
* remove unrelated metadata;
* verify output playback.

---

# Motion Duration

Store duration from the processed local motion file, not only from the source.

After transcoding:

* re-read duration;
* verify it is greater than zero;
* update the import plan;
* store the final duration in Room.

---

# Audio

Live Photo audio is part of the memory and should be retained.

Default playback:

* audio enabled;
* moderate volume;
* controlled by app preference and system volume;
* no interface sound layered over it.

If the motion file has no audio:

* play silently;
* do not show an error.

If audio decoding fails but video succeeds:

* play video silently;
* record a recoverable diagnostic;
* do not downgrade the whole Moment to a still unless video also fails.

---

# Local Live Photo Structure

Recommended local files:

```text
journeys/{journeyId}/
├── stills/
│   └── {momentId}.jpg
├── motion/
│   └── {momentId}.mp4
└── thumbnails/
    └── {momentId}.webp
```

The local still and motion filenames should share the same `MomentId`.

This makes ownership and cleanup clear.

---

# Import-Time Verification

Every processed Still Photo must pass:

* output file exists;
* output size greater than zero;
* output decodes successfully;
* final dimensions are valid;
* orientation is correct;
* thumbnail decodes successfully.

Every processed Live Photo must additionally pass:

* motion file exists;
* motion size greater than zero;
* video track exists;
* duration greater than zero;
* Media3 preparation succeeds;
* still and motion are associated with one planned Moment.

A successful write without successful decode or prepare is not a valid import.

---

# Playback Model

A Live Photo page follows this sequence:

```text
Still displayed
    ↓
Short hold
    ↓
Motion begins
    ↓
Motion plays once
    ↓
Final frame or still settles
    ↓
Page advances
```

---

# Playback Timing

Suggested initial timing:

| Stage                    |                   Default |
| ------------------------ | ------------------------: |
| Still hold before motion |                  1,000 ms |
| Motion playback          | Native processed duration |
| Post-motion hold         |              750–1,500 ms |
| Transition to next page  |        Page-turn duration |

These values should be configurable through playback policy rather than hardcoded inside the composable.

---

# Live Photo Playback Modes

Preferences should support:

```kotlin
enum class LivePhotoPlaybackMode {
    AUTOMATIC,
    TAP_TO_PLAY,
    STILL_ONLY
}
```

---

## Automatic

Default.

* show still;
* play motion once automatically;
* include audio according to preference;
* continue Journey.

---

## Tap to Play

* show still;
* show discreet motion indicator;
* motion plays when tapped;
* automatic page duration should allow time for interaction;
* Journey may continue if no interaction occurs.

---

## Still Only

* show still;
* do not prepare motion;
* retain the motion file locally;
* allow preference to be changed later.

---

# Live Photo State

```kotlin
sealed interface LivePhotoPlaybackState {

    data object ShowingStill : LivePhotoPlaybackState

    data object PreparingMotion : LivePhotoPlaybackState

    data class PlayingMotion(
        val positionMs: Long,
        val durationMs: Long
    ) : LivePhotoPlaybackState

    data object MotionCompleted : LivePhotoPlaybackState

    data object Paused : LivePhotoPlaybackState

    data class Failed(
        val category: LivePhotoFailureCategory
    ) : LivePhotoPlaybackState
}
```

The UI should map this to a calm visual experience.

It should not expose technical state names.

---

# Live Photo Player Ownership

Use one lifecycle-managed Media3 player instance for Journey playback unless profiling proves that a small controlled pool is necessary.

The player should:

* prepare the current motion;
* optionally pre-prepare the next Live Photo;
* stop when the page changes;
* release surfaces appropriately;
* pause when the app backgrounds;
* respect audio focus;
* release when Journey mode ends.

Do not attach one player permanently to every Live Photo page.

---

# Preloading

Preload conservatively.

Potential strategy:

* current still image loaded;
* next page image prefetched;
* current Live Photo motion prepared shortly before playback;
* next motion prepared only when it is the immediate next page and resources allow.

Avoid keeping multiple decoded videos or large bitmaps resident.

---

# Still-to-Motion Transition

Requirements:

* no visible resize;
* no aspect-ratio jump;
* no black flash;
* no incorrect rotation;
* no delayed layout shift.

The still image and video surface should use the same layout box and fit policy.

A short crossfade may hide minor differences between:

* still crop;
* video crop;
* colour rendering;
* first video frame.

---

# End Frame Behaviour

After motion completes, choose one consistent policy.

Recommended Version 1 policy:

1. crossfade back to the processed still image;
2. hold briefly;
3. turn the page.

This avoids device-specific final-frame retention issues.

Alternative:

* retain the final video frame.

The chosen behaviour should be tested for visual continuity.

---

# Replay

Tapping a Live Photo while its page is active should replay motion.

Rules:

* if motion is playing, restart only if the interaction specification explicitly requires it;
* otherwise ignore accidental repeated taps or show pause behaviour consistently;
* if motion completed, replay from the beginning;
* if the Journey is paused, replay should not silently unpause the automatic Journey timer;
* release and re-prepare only when required.

Gesture priority must be resolved in the Screen Specification because ordinary page tap also controls pause and resume.

A recommended approach is:

* tap centre area: pause or resume Journey;
* tap discreet Live Photo indicator: replay;
* long press: reveal controls including Replay.

This may be easier to use consistently than assigning two meanings to the same tap.

---

# Page Navigation During Motion

When the user turns the page:

1. stop playback;
2. detach or hide the video surface;
3. release or clear the media item;
4. return the renderer to a neutral state;
5. complete the page-turn animation;
6. prepare the newly active page.

No audio from the previous page may continue.

---

# Pause and Resume

When Journey playback is paused:

* automatic page progression stops;
* active Live Photo motion pauses;
* audio pauses;
* playback position is retained.

When resumed:

* motion resumes from its current position;
* or the still page remains still according to playback state;
* automatic progression restarts using the remaining timing policy.

---

# App Backgrounding

When the application enters the background:

* pause motion immediately;
* release audio focus;
* pause automatic Journey progression;
* store transient player state;
* do not continue audio.

When foregrounded within the resume window:

* restore the still page;
* allow motion to resume or restart according to policy;
* do not show a raw player surface before it is ready.

---

# Motion Failure During Playback

Failure hierarchy:

## Video cannot prepare

* show still;
* hide motion indicator after failure;
* continue as a Still Photo;
* record a recoverable error.

## Video starts but fails

* crossfade to still;
* continue Journey;
* do not retry repeatedly during the same playback.

## Audio fails

* continue video silently.

## Local motion file missing

* render still;
* flag the Moment for Workshop diagnostics.

## Both still and motion fail

* show a calm placeholder page;
* continue to the next Moment.

---

# Still Image Failure During Playback

If the playback copy fails:

1. attempt thumbnail as an emergency fallback;
2. if unavailable, show a restrained placeholder;
3. continue Journey;
4. record integrity issue;
5. expose repair guidance in Workshop.

Do not attempt to read the disconnected external archive.

---

# Media Error Categories

```kotlin
enum class MediaFailureCategory {
    SOURCE_UNREADABLE,
    UNSUPPORTED_FORMAT,
    INVALID_DIMENSIONS,
    IMAGE_DECODE_FAILED,
    IMAGE_ENCODE_FAILED,
    THUMBNAIL_FAILED,
    METADATA_FAILED,
    PAIRING_AMBIGUOUS,
    MOTION_UNMATCHED,
    MOTION_UNSUPPORTED,
    MOTION_PREPARE_FAILED,
    MOTION_TRANSCODE_FAILED,
    AUDIO_UNSUPPORTED,
    OUTPUT_VERIFICATION_FAILED,
    LOCAL_FILE_MISSING,
    LOCAL_FILE_CORRUPT,
    STORAGE_EXHAUSTED,
    CANCELLED
}
```

These codes are internal.

User-facing language should follow the Product Language document.

---

# Import Report

After discovery and pairing, the import preview should show:

* Still Photos detected;
* Live Photos detected;
* HEIC or HEIF files detected;
* unmatched motion files;
* unsupported files;
* unreadable files;
* uncertain pairings;
* estimated local output size.

Example:

```text
124 memories found

98 photographs
26 Live Photos

2 files need your attention
1 motion file could not be matched
1 image could not be read
```

Avoid making the user reason about raw file counts unless they open technical details.

---

# Storage Estimation

Estimate separately:

* processed still copies;
* thumbnails;
* retained or transcoded motion files;
* temporary workspace;
* safety margin.

The estimate should include temporary processing needs because import may briefly require both:

* source access;
* temporary output;
* final output.

Recommended safety reserve:

* do not allow import to consume all remaining internal storage;
* retain a configurable free-space margin for Android and app operation.

The exact threshold belongs in the Storage document.

---

# Temporary Files

Use a per-import temporary directory.

Example:

```text
cache/imports/{importId}/
├── stills/
├── motion/
├── thumbnails/
└── manifest/
```

Requirements:

* never mix imports;
* track every temporary output;
* remove after success;
* remove after cancellation;
* recover cleanup after process death;
* do not expose temporary files to Journey playback.

---

# Cancellation

The importer must support cancellation between processing units.

On cancellation:

* stop new work;
* finish or safely abort current encoder or transcoder;
* release decoders and players;
* delete temporary outputs;
* mark import cancelled;
* preserve source files;
* leave no READY Journey with partial media.

Some codec operations may not stop instantly.

The UI should remain truthful:

> Finishing the current page before stopping…

---

# Concurrency

Media processing is resource-intensive.

Recommended limits:

* decode and resize only a small number of stills concurrently;
* transcode one motion file at a time initially;
* avoid multiple hardware codec sessions until tested;
* bound memory use;
* make concurrency configurable.

Maximum throughput is less important than reliability and device temperature.

---

# Thermal and Power Behaviour

Large imports may be lengthy.

The importer should:

* avoid unbounded parallel work;
* allow the tablet screen to dim where safe;
* communicate progress;
* tolerate charger connection or removal;
* pause or reduce work if severe thermal conditions are reported;
* avoid running while Journey playback is active.

Media import and immersive Journey playback should not compete for codecs and memory.

---

# Media Cache

The image loader may maintain ordinary memory and disk caches.

However:

* app-managed playback files are authoritative;
* cache eviction must not remove Journey media;
* cache files do not need backup;
* cache must not contain the only readable representation;
* cache should be bounded.

Live Photo motion files should be read from app-managed storage, not copied into an opaque player cache as the only representation.

---

# Source Archive Relationship

The external archive contains:

* full-resolution originals;
* original HEIC files;
* original MOV components;
* optional `trip.json`;
* future portable manifests.

The tablet contains:

* optimised still copies;
* motion copies or compatible transcodes;
* thumbnails;
* Journey metadata;
* owner-authored story and order.

The tablet library is replaceable from the archive, except for authored data that must be included in future backup and export.

---

# Privacy

Media processing must avoid unintended disclosure.

Requirements:

* no upload;
* no network-based conversion;
* no third-party analytics;
* no logging of full filenames where avoidable;
* no logging of GPS;
* no persistent raw metadata dump;
* no external temporary conversion service;
* app-private local output.

If a third-party local decoder is introduced later, review:

* licence;
* maintenance;
* security history;
* supported formats;
* native-code risk.

---

# Security

Media files are untrusted input.

Defensive requirements:

* validate all dimensions;
* bound allocations;
* catch parser exceptions;
* reject path traversal;
* avoid trusting MIME type alone;
* process files with least privilege;
* keep libraries updated;
* test malformed input;
* avoid unsafe native decoders unless necessary.

The application should not import symbolic links or references that escape the selected source tree.

---

# Media Abstractions

Recommended interfaces:

```kotlin
interface MediaDiscoveryService

interface MediaMetadataExtractor

interface LivePhotoPairingEngine

interface StillImageProcessor

interface ThumbnailGenerator

interface MotionCompatibilityChecker

interface MotionTranscoder

interface MediaOutputVerifier

interface LivePhotoPlaybackController
```

Each interface should have:

* production implementation;
* fake test implementation;
* structured result types;
* no direct UI dependency.

---

# Still Processing Result

```kotlin
sealed interface StillProcessingResult {

    data class Success(
        val stillReference: LocalImageReference,
        val thumbnailReference: LocalImageReference,
        val width: Int,
        val height: Int,
        val stillBytes: Long,
        val thumbnailBytes: Long,
        val outputFormat: LocalStillFormat
    ) : StillProcessingResult

    data class Failure(
        val category: MediaFailureCategory,
        val recoverable: Boolean
    ) : StillProcessingResult
}
```

---

# Motion Processing Result

```kotlin
sealed interface MotionProcessingResult {

    data class Retained(
        val motionReference: LocalMotionReference,
        val durationMs: Long,
        val bytes: Long,
        val videoCodec: String,
        val audioCodec: String?
    ) : MotionProcessingResult

    data class Transcoded(
        val motionReference: LocalMotionReference,
        val durationMs: Long,
        val bytes: Long,
        val sourceVideoCodec: String?,
        val outputVideoCodec: String,
        val outputAudioCodec: String?
    ) : MotionProcessingResult

    data class Failure(
        val category: MediaFailureCategory,
        val stillCanBeImported: Boolean
    ) : MotionProcessingResult
}
```

---

# Live Photo Import Result

```kotlin
sealed interface LivePhotoImportResult {

    data class ImportedAsLivePhoto(
        val moment: ImportedLivePhoto
    ) : LivePhotoImportResult

    data class ImportedAsStill(
        val moment: ImportedStillPhoto,
        val reason: LivePhotoDowngradeReason
    ) : LivePhotoImportResult

    data class NeedsOwnerReview(
        val candidates: List<LivePhotoPairCandidate>
    ) : LivePhotoImportResult

    data class Failed(
        val category: MediaFailureCategory
    ) : LivePhotoImportResult
}
```

Downgrading to a Still Photo should be visible in the import report.

---

# Testing Strategy

Media support must be tested with a representative corpus.

---

# Required Still Test Corpus

Include:

* JPEG landscape;
* JPEG portrait;
* rotated JPEG;
* PNG with transparency;
* PNG without transparency;
* WebP;
* HEIC portrait from iPhone;
* HEIC landscape from iPhone;
* HEIF with wide colour;
* very large panorama;
* image with missing EXIF;
* image with malformed EXIF;
* zero-byte file;
* file with incorrect extension;
* corrupted image;
* unusually small image.

---

# Required Live Photo Test Corpus

Include:

* original HEIC + MOV pair;
* JPEG + MOV pair;
* pair with embedded matching identifier;
* pair with matching filename only;
* pair with altered filename;
* timestamp-only candidate;
* ambiguous pair;
* still without motion;
* motion without still;
* motion with unsupported codec;
* motion without audio;
* corrupted motion;
* Live Photo edited before export;
* Live Photo exported through a non-Apple service;
* duplicated pair;
* pair with different orientation metadata;
* pair with very large source resolution.

Test assets should not contain private family content in the repository.

Use purpose-created or properly licensed samples.

---

# Unit Tests

Test:

* extension normalisation;
* MIME sniffing;
* metadata priority;
* orientation normalisation;
* filename pairing;
* identifier pairing;
* timestamp pairing;
* ambiguous pairing prevention;
* unmatched motion handling;
* output target-size calculation;
* format selection;
* storage estimation;
* failure classification;
* Live Photo downgrade rules.

---

# Integration Tests

Test:

* HEIC decode to local JPEG;
* local output verification;
* thumbnail generation;
* motion direct-play compatibility check;
* motion transcoding;
* audio preservation;
* paired-file persistence;
* paired-file deletion;
* cancellation cleanup;
* process restart during import;
* low-storage failure;
* corrupted local file fallback.

---

# Physical HMD T21 Tests

The following must be verified on the actual tablet:

* HEIC decoding;
* HEIF decoding;
* colour accuracy;
* HDR-to-SDR appearance;
* portrait orientation;
* panorama memory use;
* direct MOV playback;
* HEVC video playback;
* AAC audio playback;
* Media3 surface startup;
* still-to-motion transition;
* no black flash;
* page navigation during playback;
* audio stopping on page turn;
* import speed;
* device temperature;
* storage usage;
* playback after reboot;
* playback with Wi-Fi disabled;
* playback with external drive disconnected.

Android documentation confirms platform-level HEIF support, but target-device testing remains mandatory because codec implementation and performance can vary.

---

# Performance Targets

Initial targets:

## Still page

* first frame displayed without visible blocking;
* next image prefetched;
* no full-resolution source decode during ordinary playback.

## Live Photo

* motion begins within approximately 250 ms of scheduled start after preparation;
* no visible surface flash;
* audio begins synchronously enough to feel natural;
* page turn stops playback immediately.

## Import

* UI remains responsive;
* memory remains bounded;
* progress remains accurate;
* cancellation remains available.

Final targets should be derived from physical profiling rather than desktop assumptions.

---

# Diagnostics

Workshop diagnostics may show:

* local format;
* dimensions;
* local file size;
* Live Photo pairing method;
* motion codec;
* motion duration;
* whether motion was transcoded;
* integrity status.

Diagnostics must not expose unnecessary private metadata.

A normal family user should never see this information.

---

# Repair Behaviour

A future repair action may allow the owner to reconnect the archive and replace a damaged local copy.

Version 1 may provide:

* identify damaged Moment;
* remove damaged local Moment;
* re-import the source Journey folder;
* avoid duplicating healthy Moments.

Full automatic repair is optional in Version 1.

The data and fingerprint strategy should not prevent it later.

---

# Decisions Requiring ADRs

Create ADRs for:

```text
ADR-009-Local-Still-Format.md
ADR-010-Live-Photo-Pairing-Policy.md
ADR-011-Motion-Compatibility-Strategy.md
ADR-012-Media-Metadata-Retention.md
ADR-013-HDR-and-Colour-Policy.md
```

These decisions should be finalised after target-device prototypes.

---

# Acceptance Criteria

The Version 1 media system is complete when:

* JPEG, PNG and WebP files import successfully;
* iPhone HEIC and HEIF files import without manual owner conversion;
* orientation is correct;
* app-managed display copies are produced;
* originals remain untouched;
* Live Photo still and motion components are paired as one Moment;
* embedded identifiers are used when available;
* fallback pairing is conservative and deterministic;
* ambiguous matches require owner review;
* unmatched motion files do not fail the import;
* Live Photo motion plays locally through Media3;
* incompatible motion can be converted or downgraded safely;
* Live Photo audio is preserved when supported;
* still playback remains available when motion fails;
* Journey playback works offline;
* media deletion removes all local components belonging to the Moment;
* target-device testing confirms acceptable colour, performance and stability;
* no media processing requires a cloud service.

---

# Final Principle

> **A Live Photo should feel like a printed memory gently returning to life—not like a video file being launched.**

Every technical decision should preserve that moment while keeping the family journal dependable for years to come.
