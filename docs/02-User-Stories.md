# User Stories

**Project:** Magnet Memories

**Version:** 1.0

---

# Purpose

This document describes how people interact with Magnet Memories.

Each user story captures:

* the user's goal;
* the expected behaviour;
* the acceptance criteria.

These stories define the expected experience for Version 1.

---

# Primary Personas

## The Family

Uses Magnet Memories to relive journeys together.

Technical knowledge is not required.

---

## The Owner

Responsible for:

* creating journeys;
* importing photographs;
* writing NFC tags;
* maintaining the display.

---

## The Guest

Has never seen the application before.

Should be able to use it immediately without explanation.

---

# User Story 1 — Relive a Journey

**As a family member**

I want to tap a travel magnet

so that I can instantly relive that journey.

### Acceptance Criteria

* The NFC tag is detected.
* The correct journey is identified.
* The Memory Card appears.
* The slideshow begins automatically.
* No menus are displayed.
* Total time from tap to Memory Card should feel nearly instantaneous.

---

# User Story 2 — Read the Memory

**As a family member**

I want to read a short story before the slideshow begins

so that I remember why the journey was meaningful.

### Acceptance Criteria

* Every journey displays a Memory Card.
* The Memory Card includes:

  * title;
  * location;
  * travel dates;
  * personal story;
  * optional favourite quote.
* The slideshow begins automatically after a short delay.
* Tapping the screen starts immediately.

---

# User Story 3 — Watch the Journey

**As a family member**

I want the journey to play automatically

so that I can simply enjoy the memories.

### Acceptance Criteria

* Photographs fill the screen.
* Live Photos play automatically.
* Transitions are smooth.
* Image orientation is correct.
* Playback requires no interaction.

---

# User Story 4 — Pause

**As a family member**

I want to pause on a favourite memory

so that I can spend more time looking at it.

### Acceptance Criteria

* Tapping pauses playback.
* Tapping again resumes playback.
* Live Photos stop while paused.
* Playback resumes from the correct position.

---

# User Story 5 — Replay a Live Photo

**As a family member**

I want to replay a Live Photo

so that I can watch the movement again.

### Acceptance Criteria

* Tapping a Live Photo replays it.
* Replay begins immediately.
* Motion and audio remain synchronised.

---

# User Story 6 — Change Journeys

**As a family member**

I want to tap another magnet

so that I can immediately switch to another journey.

### Acceptance Criteria

* Current playback stops.
* Resources are released.
* New Memory Card appears.
* New journey begins.

No application restart should be required.

---

# User Story 7 — Return Home

**As a family member**

I want the display to return to the home screen

after a journey finishes

so that the next person can begin another journey.

### Acceptance Criteria

* Journey completes.
* End card is shown.
* Home screen appears automatically.

---

# User Story 8 — Import a Journey

**As the owner**

I want to import a complete journey folder

so that I can easily add new memories.

### Acceptance Criteria

* Folder picker opens.
* Journey folder is selected.
* Preview is displayed.
* Images are detected.
* Live Photos are detected.
* Metadata is read.
* Import progress is displayed.
* Local copies are created.
* Originals remain unchanged.

---

# User Story 9 — Import HEIC Images

**As the owner**

I want iPhone photographs to import without conversion

so that I do not need additional software.

### Acceptance Criteria

* HEIC files are recognised.
* HEIF files are recognised.
* Images display correctly.
* Orientation is preserved.
* Capture dates are preserved.

---

# User Story 10 — Import Live Photos

**As the owner**

I want Live Photos to remain animated

so that memories feel authentic.

### Acceptance Criteria

* HEIC/JPEG and MOV pairs are recognised.
* Live Photos become one Moment.
* Motion plays correctly.
* Motion survives offline.
* Pairing failures are reported.

---

# User Story 11 — Write a Magnet

**As the owner**

I want to program a travel magnet

so that tapping it always opens the correct journey.

### Acceptance Criteria

* Journey selected.
* NFC tag detected.
* URI written.
* URI verified.
* Success message displayed.

---

# User Story 12 — Unknown Magnet

**As the owner**

I want unknown magnets to be recognised

so that I can assign them later.

### Acceptance Criteria

* Unknown tag detected.
* Friendly message displayed.
* Option to associate with a journey.

Application must not crash.

---

# User Story 13 — Edit a Journey

**As the owner**

I want to update journey information

so that memories remain accurate.

### Acceptance Criteria

Editable fields include:

* title;
* location;
* dates;
* Memory Card;
* favourite quote;
* cover photograph.

---

# User Story 14 — Delete a Journey

**As the owner**

I want to remove a journey from the tablet

without deleting the original archive.

### Acceptance Criteria

* Confirmation required.
* Local media removed.
* Database updated.
* External archive untouched.

---

# User Story 15 — Low Storage

**As the owner**

I want to know when storage is running low

before importing

so that imports never fail unexpectedly.

### Acceptance Criteria

* Remaining storage displayed.
* Estimated import size shown.
* Warning before import.
* Import prevented when storage is insufficient.

---

# User Story 16 — Visitor Experience

**As a visitor**

I want to understand the display immediately

without instructions.

### Acceptance Criteria

Idle screen clearly communicates:

> Tap a travel magnet to relive a journey.

No additional explanation required.

---

# User Story 17 — Child Experience

**As a child**

I want to explore family journeys

without worrying about breaking anything.

### Acceptance Criteria

Normal interaction cannot:

* delete journeys;
* modify data;
* expose settings.

Administrative mode remains hidden.

---

# User Story 18 — Offline Playback

**As a family**

I want every journey to work without internet

so that memories are always available.

### Acceptance Criteria

Journey playback succeeds with:

* Wi-Fi disabled;
* no Google account;
* no external drive connected.

---

# User Story 19 — Reliability

**As every user**

I expect the application to behave predictably.

### Acceptance Criteria

The application must never:

* lose a journey;
* corrupt imported media;
* modify original files;
* expose technical errors during normal use.

---

# Definition of Done

Version 1 is complete when every user story in this document can be demonstrated successfully on the HMD T21 tablet using real NFC travel magnets and imported family journeys.

Every story should be independently testable.

Every story should contribute to one goal:

> **Tap a magnet. Relive the journey.**
