# Lab 143 – Evidence Integrity and Chain of Custody

## Overview

This lab demonstrates fundamental digital forensics practices for preserving evidence integrity and maintaining a basic chain of custody.

Using PowerShell 7.7.0 on Windows 11, a harmless test script was created as a controlled evidence artifact. Its SHA256 hash was calculated, its metadata was recorded, and a separate working copy was created for examination.

The original and working-copy hashes were compared to establish that the copy initially matched the original. A controlled modification was then introduced into the working copy to demonstrate how a file hash changes when its contents are altered.

The investigation also included Wazuh archive validation of a Sysmon file creation event associated with the working copy.

## Objectives

- Understand digital evidence integrity and chain of custody.
- Create a controlled evidence artifact.
- Calculate and record a SHA256 hash.
- Register evidence with identifying metadata.
- Create a separate working copy for examination.
- Verify the integrity of the original and working copy.
- Demonstrate modification detection through hash comparison.
- Maintain a basic chain-of-custody record.
- Correlate file creation telemetry with Wazuh archives.

## Lab Environment

| Component | Details |
|---|---|
| Endpoint OS | Windows 11 Pro |
| Hostname | DESKTOP-9MMM37V |
| Shell | PowerShell 7.7.0 |
| Endpoint Monitoring | Sysmon |
| SIEM / HIDS | Wazuh 4.13.1 |
| Evidence Hash | SHA256 |
| Evidence ID | EVD-143-001 |

## Workspace Structure

The investigation workspace was created at:

`C:\DFIR-Lab143`

Directory structure:

- `original` – Original controlled evidence artifact.
- `working` – Separate copy used for examination.
- `metadata` – Evidence register and chain-of-custody CSV.
- `logs` – Reserved for supporting investigation logs.

## Investigation Summary

A harmless PowerShell script named `suspicious-script.ps1` was created under the original evidence directory.

The initial SHA256 hash was:

`4024625A3BF2E09F3A094472CE3B398FB42143C5DB3882346EC64EDD4B1D2F44`

A working copy was created and independently hashed. Both files initially produced the same SHA256 value.

A controlled comment was subsequently appended to the working copy. Its SHA256 hash changed to:

`B7C7EFA742F9E32F1AD353781571F7424EE17ED664444DCB9B6DA78B34A0FAA4`

The original file retained its initial hash.

This demonstrated that the modification affected the working copy without changing the original artifact.

## Evidence Records

| Attribute | Recorded Value |
|---|---|
| Evidence ID | EVD-143-001 |
| File | suspicious-script.ps1 |
| Original location | C:\DFIR-Lab143\original\suspicious-script.ps1 |
| Working copy | C:\DFIR-Lab143\working\suspicious-script.ps1 |
| Hash algorithm | SHA256 |
| Initial hash | 4024625A3BF2E09F3A094472CE3B398FB42143C5DB3882346EC64EDD4B1D2F44 |
| Modified working-copy hash | B7C7EFA742F9E32F1AD353781571F7424EE17ED664444DCB9B6DA78B34A0FAA4 |
| Original integrity check | Passed |

## Wazuh and Sysmon Validation

A Sysmon Event ID 11 record was identified in Wazuh archives for:

`C:\DFIR-Lab143\working\suspicious-script.ps1`

Recorded event details:

- Event ID: 11
- Event type: File created
- Host: DESKTOP-9MMM37V
- Agent ID: 001
- User: DESKTOP-9MMM37V\Dell
- Process: PowerShell 7.7.0
- Process ID: 14652
- Event Record ID: 2300254
- Archive timestamp: 2026-11-23 10:17:36.772 IST
- Sysmon UTC event time: 2026-11-23 04:47:35.768

This confirms that the file creation activity was recorded by Sysmon and ingested into Wazuh archives.

The event establishes file creation telemetry. It does not independently establish the file's content, SHA256 value, or complete chain of custody.

## Repository Contents

| File | Purpose |
|---|---|
| `README.md` | Lab overview and investigation summary |
| `concept.md` | Evidence integrity and chain-of-custody concepts |
| `lab-objectives.md` | Learning objectives |
| `lab-scenario.md` | Investigation scenario |
| `investigation-notes.md` | Commands, evidence, and findings |
| `troubleshooting-notes.md` | Warnings, observations, and limitations |
| `timeline.md` | Chronological investigation record |

## Key Findings

1. The original evidence artifact was successfully created and registered.
2. Its SHA256 hash was calculated and recorded.
3. The working copy initially matched the original hash.
4. A controlled modification changed the working-copy hash.
5. The original file retained its initial SHA256 hash.
6. Evidence metadata and a basic chain-of-custody CSV were generated.
7. Wazuh archives contained a Sysmon Event ID 11 record for the working-copy creation.

## Investigation Limitations

- The artifact was created specifically for this controlled lab; it was not acquired from a real incident.
- The hash comparison demonstrates content consistency at the time of calculation, not authenticity or provenance.
- The chain-of-custody CSV contains the initial registration and working-copy creation records. The subsequent controlled modification was not added as a separate custody entry.
- Sysmon Event ID 11 records file creation activity but does not provide a SHA256 hash of the file contents.
- This exercise demonstrates basic forensic handling practices, not a court-ready forensic acquisition procedure.

## Conclusion

The lab demonstrated how SHA256 hashing can help verify file integrity and detect subsequent content changes.

Maintaining a separate working copy helped preserve the original controlled artifact during examination. Evidence metadata and custody records provided a basic history of evidence handling.

The Wazuh archive record added endpoint telemetry supporting the working-copy creation activity.

**Key takeaway:** Evidence integrity and chain of custody are complementary. Hashes help detect content changes, while custody records document how evidence was handled.
