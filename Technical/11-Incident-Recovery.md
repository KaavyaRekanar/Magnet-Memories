# 11. Incident Recovery

## 11.1 Overview

No software system is immune to failure.

Storage devices wear out.

Applications crash.

Power is interrupted.

Files become corrupted.

People make mistakes.

Rather than assuming failures will never occur, Magnet Memories is designed with the expectation that they eventually will.

The objective of Incident Recovery is not merely to restore functionality.

It is to preserve trust.

When something goes wrong, the owner should understand:

* what happened;
* what information is affected;
* what actions are available;
* whether memories remain safe;
* how recovery can be performed.

Recovery is therefore considered a core architectural capability rather than an optional feature.

---

# 11.2 Recovery Philosophy

The incident recovery strategy is based on five principles.

## Preserve Before Repair

Whenever possible, preserve existing data before attempting corrective actions.

Recovery should never increase the scope of data loss.

---

## Recover Deterministically

Recovery procedures should follow predictable, documented workflows.

Two identical incidents should result in the same recovery behaviour.

---

## Fail Transparently

Unexpected failures should be communicated clearly.

The application should explain what is known, what is uncertain, and what the owner can do next.

---

## Protect the Archive

Recovery procedures must never modify the external archive.

The archive remains the authoritative source of original media.

---

## Recover Locally First

Where possible, recovery should use information already stored on the device.

The owner should not require internet connectivity to restore normal operation.

---

# 11.3 Incident Classification

Incidents are classified into four severity levels.

| Severity      | Description                                     | Example                |
| ------------- | ----------------------------------------------- | ---------------------- |
| Informational | Minor issue with no loss of functionality       | Thumbnail regeneration |
| Recoverable   | Limited disruption with defined recovery path   | Interrupted import     |
| Serious       | Loss of local data requiring owner intervention | Database corruption    |
| Critical      | Recovery depends on backup or archive           | Tablet loss            |

Severity determines the owner's experience and the recommended recovery workflow.

---

# 11.4 Recovery Lifecycle

Every incident follows the same lifecycle.

```text id="m70m1y"
Incident

↓

Detection

↓

Assessment

↓

Containment

↓

Recovery

↓

Verification

↓

Return to Normal Operation
```

No recovery workflow should bypass verification.

---

# 11.5 Incident A — Interrupted Import

## Description

The import process stops unexpectedly due to:

* application termination;
* power loss;
* device restart;
* owner cancellation.

---

## Detection

The application detects:

* incomplete import manifests;
* unfinished temporary workspaces;
* incomplete database transactions.

---

## User Experience

Display:

> We couldn't finish adding your journey.

Explain that existing Journeys remain unaffected.

---

## Recovery Strategy

* inspect temporary workspace;
* determine recoverable state;
* resume or restart import;
* clean temporary files;
* verify output.

---

## Expected Outcome

No partially imported Journey should ever appear in normal playback.

---

# 11.6 Incident B — External Drive Disconnected

## Description

The archive becomes unavailable during import.

---

## Detection

Read operations fail or Storage Access Framework access is revoked.

---

## User Experience

Display:

> The journey folder is no longer available.

---

## Recovery Strategy

* stop processing safely;
* preserve temporary work;
* allow reconnection;
* retry import.

---

## Expected Outcome

Existing Journeys remain fully functional because playback never depends on the archive.

---

# 11.7 Incident C — Journey Deleted

## Description

A Journey is intentionally or accidentally removed.

---

## Detection

Journey records no longer exist.

---

## User Experience

If an associated NFC tag is scanned:

> This journey is no longer in your journal.

Offer:

* reconnect to a new Journey;
* import again from the archive.

---

## Recovery Strategy

Recover by:

* importing the Journey again from the archive;
* restoring from backup if available.

---

## Expected Outcome

The NFC magnet remains reusable.

---

# 11.8 Incident D — Corrupted Media

## Description

One or more playback files become unreadable.

---

## Detection

Failures during:

* image decoding;
* video preparation;
* integrity verification.

---

## User Experience

Unavailable Moments should be clearly identified without preventing playback of the remainder of the Journey.

---

## Recovery Strategy

* regenerate thumbnails where possible;
* recreate playback copies from the archive;
* restore affected Journey from backup if required.

---

## Expected Outcome

Damage should remain isolated to the affected media.

---

# 11.9 Incident E — Database Corruption

## Description

The Room database becomes inconsistent or unreadable.

---

## Detection

* migration failure;
* integrity checks;
* database open failure.

---

## User Experience

Display:

> Your journal needs repair before it can be opened.

Avoid technical terminology.

---

## Recovery Strategy

* attempt automated repair where safe;
* restore database from backup;
* rebuild metadata using local Journey files where practical.

---

## Expected Outcome

The application should preserve as much owner-authored information as possible.

---

# 11.10 Incident F — NFC Tag Damage

## Description

An NFC tag becomes unreadable, overwritten or physically damaged.

---

## Detection

* invalid NDEF;
* missing Journey URI;
* repeated read failures.

---

## User Experience

Display:

> This magnet can't be recognised.

Offer to program a replacement tag.

---

## Recovery Strategy

* replace the NFC tag;
* write the Journey URI again;
* verify the programmed tag.

---

## Expected Outcome

No Journey data is lost because NFC tags contain only identifiers.

---

# 11.11 Incident G — Tablet Loss or Replacement

## Description

The original tablet is lost, stolen or permanently damaged.

---

## Detection

Recovery begins on a replacement device.

---

## Recovery Strategy

* install Magnet Memories;
* restore the latest journal backup;
* reconnect NFC magnets if required;
* verify imported Journeys.

If no backup exists:

* import Journeys again from the external archive;
* recreate Memory Cards manually if necessary.

---

## Expected Outcome

The architecture ensures that the family's original media remains recoverable even if the tablet is lost.

---

# 11.12 Incident H — Backup Failure

## Description

A backup package cannot be created or restored.

---

## Detection

Manifest validation or archive verification fails.

---

## Recovery Strategy

* explain the failure;
* preserve existing journal;
* prevent partial restore;
* allow the owner to select another backup.

---

## Expected Outcome

The current journal should never be overwritten by an invalid backup.

---

# 11.13 Incident I — Low Storage

## Description

Insufficient storage prevents normal operation.

---

## Detection

Storage monitoring reports available capacity below required thresholds.

---

## Recovery Strategy

* prevent new imports;
* continue playback where possible;
* recommend cleanup;
* preserve existing Journeys.

---

## Expected Outcome

Low storage should never automatically delete memories.

---

# 11.14 Incident J — Application Update Failure

## Description

A software update introduces an unexpected failure.

---

## Detection

Version migration or startup validation fails.

---

## Recovery Strategy

* stop startup safely;
* preserve existing data;
* report migration issue;
* restore from backup if necessary.

---

## Expected Outcome

Software updates should never silently alter or remove Journeys.

---

# 11.15 Recovery Decision Matrix

| Incident            | Detection           | Recovery             | Owner Action             |
| ------------------- | ------------------- | -------------------- | ------------------------ |
| Interrupted Import  | Import manifest     | Resume or restart    | Retry if requested       |
| Drive Disconnected  | Read failure        | Reconnect archive    | Reconnect drive          |
| Journey Deleted     | Missing records     | Re-import or restore | Confirm import           |
| Corrupted Media     | Playback validation | Recreate media       | None in most cases       |
| Database Corruption | Integrity check     | Repair or restore    | Confirm recovery         |
| NFC Damage          | Read failure        | Reprogram tag        | Present replacement tag  |
| Tablet Loss         | New installation    | Restore backup       | Restore journal          |
| Backup Failure      | Manifest validation | Retry or replace     | Select another backup    |
| Low Storage         | Storage monitor     | Free space           | Remove unnecessary files |
| Update Failure      | Startup validation  | Repair or restore    | Follow recovery guidance |

---

# 11.16 Recovery Logging

Recovery logs should record:

* incident identifier;
* recovery stage;
* outcome;
* elapsed time;
* affected Journey identifiers.

Logs should not include:

* Memory Card contents;
* owner-written stories;
* GPS information;
* complete filenames.

Diagnostic information should support engineering without exposing personal memories.

---

# 11.17 Recovery Testing

Every recovery workflow should be exercised through automated and manual testing.

Testing should include:

* interrupted imports;
* storage exhaustion;
* corrupted media;
* damaged databases;
* failed migrations;
* missing NFC tags;
* backup restoration;
* archive disconnection.

Recovery paths should be tested as rigorously as normal workflows.

---

# 11.18 Architectural Guarantees

The recovery architecture guarantees that:

* incomplete imports never appear as valid Journeys;
* the external archive is never modified during recovery;
* NFC tag replacement never affects Journey content;
* failures remain isolated whenever possible;
* recovery procedures preserve existing data before repair;
* playback does not depend on the external archive;
* owner-authored stories receive the highest recovery priority.

These guarantees form part of the system's long-term engineering contract.

---

## Closing Statement

Failures are inevitable.

Permanent loss of family memories should not be.

Magnet Memories therefore treats recovery as an essential part of its architecture rather than an afterthought.

Every recovery workflow is designed around a simple objective:

> **When the unexpected happens, families should lose as little as possible, understand what happened, and recover with confidence rather than uncertainty.**
