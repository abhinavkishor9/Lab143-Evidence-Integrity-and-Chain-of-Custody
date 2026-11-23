# Lab143-Evidence-Integrity-and-Chain-of-Custody
## Overview
### 1. What Is Digital Evidence?

Digital evidence is information stored or transmitted in digital form that may help establish what happened during a security incident.

Examples include:

- Files and documents.
- Windows event logs.
- Linux system logs.
- PowerShell execution records.
- Network packet captures.
- Memory dumps.
- Disk images.
- Browser artifacts.
- Endpoint detection and response telemetry.

In a DFIR investigation, evidence must be handled carefully so that its integrity and handling history can be assessed.

### 2. Evidence Integrity

Evidence integrity refers to maintaining the accuracy and unchanged state of digital evidence.

A common method for checking file integrity is cryptographic hashing.

A hash function processes file content and produces a fixed-length value.

For example, SHA256 produces a 256-bit hash, typically represented as a 64-character hexadecimal string.

#### Why SHA256?

SHA256 is widely used in digital forensics and incident response to:

- Verify that a copied file matches its source.
- Detect changes in file content.
- Validate evidence before and after transfer.
- Compare evidence against previously recorded hash values.
- Support evidence documentation.

#### Example

Original file:

`evidence.txt`

Initial SHA256:

`ABC123...`

Copied file:

`evidence-copy.txt`

Copied-file SHA256:

`ABC123...`

If both hashes match, the files have matching content at the time of calculation.

If the file is modified, its SHA256 hash will normally change.

**Important:** A matching hash does not prove that a file is authentic, that it came from a particular source, or that it was never altered before the initial hash was calculated.

### 3. Chain of Custody

Chain of custody is the chronological documentation of how digital evidence is identified, collected, transferred, accessed, stored, and preserved.

It helps establish who handled evidence and what happened to it throughout an investigation.

A custody record typically contains:

- Evidence ID.
- Evidence description.
- Source and collection location.
- Date and time of collection.
- Timezone.
- Name or identity of the handler.
- Action performed.
- Transfer or storage details.
- Relevant hash values.
- Notes and observations.

#### Example

| Time | Handler | Action |
|---|---|---|
| 10:00 | Analyst A | Evidence collected |
| 10:05 | Analyst A | SHA256 calculated |
| 10:15 | Analyst A | Working copy created |
| 10:30 | Analyst B | Evidence transferred |

Each entry helps document the history of evidence handling.

### 4. Evidence Integrity vs. Chain of Custody

| Evidence Integrity | Chain of Custody |
|---|---|
| Focuses on whether evidence content has changed | Focuses on how evidence was handled |
| Uses hashes such as SHA256 | Uses chronological handling records |
| Detects content differences | Documents access, transfer, and handling |
| Supports confidence in content consistency | Supports accountability and traceability |

Both are necessary for sound digital evidence handling.

A hash alone does not provide a custody history, and a custody record alone does not prove that file contents remained unchanged.

### 5. Original Evidence and Working Copies

A key forensic principle is to preserve the original evidence and perform examination on a separate working copy whenever possible.

A basic workflow is:

1. Identify the evidence.
2. Acquire it using an appropriate method.
3. Calculate and record its hash.
4. Preserve the original in secure storage.
5. Create a working copy.
6. Verify the working copy against the recorded hash.
7. Perform examination on the working copy.
8. Record examination activities and any changes.

For formal investigations, acquisition methods, write blockers, access controls, and storage procedures should follow the organization's forensic process.

### 6. Role of Sysmon and Wazuh

Sysmon provides detailed Windows endpoint telemetry.

For this lab, Sysmon Event ID 11 was relevant because it records file creation and overwrite activity.

Wazuh collects and stores endpoint events, allowing analysts to investigate file activity centrally.

However, file creation telemetry does not establish file-content integrity.

A separate hash calculation is needed to verify file contents.

### 7. Practical DFIR Applications

Evidence integrity and chain of custody are important in:

- Malware investigations.
- Insider threat investigations.
- Data exfiltration investigations.
- Endpoint compromise analysis.
- Ransomware investigations.
- Legal and regulatory investigations.
- Incident response evidence preservation.

An analyst should be able to explain what evidence was collected, how it was handled, whether its content changed, and which records support those conclusions.

### 8. Key Takeaways

- SHA256 helps identify changes in file content.
- Matching hashes indicate matching content at the time of comparison.
- A hash does not independently prove authenticity or provenance.
- Original evidence should be preserved.
- Examination should use a separate working copy where appropriate.
- Every evidence-handling action should be documented.
- Timestamps should include the timezone.
- Sysmon and Wazuh provide useful telemetry but do not replace forensic custody records.

**Core principle:** Preserve the original, verify integrity, work on a copy, and document every evidence-handling action.


This lab demonstrates fundamental digital forensics practices for preserving evidence integrity and maintaining a basic chain of custody.

Using PowerShell 7.7.0 on Windows 11, a harmless test script was created as a controlled evidence artifact. Its SHA256 hash was calculated, its metadata was recorded, and a separate working copy was created for examination.

The original and working-copy hashes were compared to establish that the copy initially matched the original. A controlled modification was then introduced into the working copy to demonstrate how a file hash changes when its contents are altered.

The investigation also included Wazuh archive validation of a Sysmon file creation event associated with the working copy.

## Lab Objectives

### Primary Objective

Demonstrate the fundamental practices involved in preserving digital evidence integrity and maintaining a traceable record of evidence handling during a controlled DFIR exercise.

### Technical Objectives

1. **Evidence Identification**
   - Assign a unique evidence identifier.
   - Record the file name, source path, and endpoint details.
   - Capture basic file metadata.

2. **Cryptographic Hashing**
   - Calculate a SHA256 hash for the original artifact.
   - Understand the purpose of cryptographic hashing.
   - Record the initial hash as an integrity reference.

3. **Working-Copy Validation**
   - Create a separate copy of the original artifact.
   - Calculate the hash of the copied file.
   - Compare both hash values to verify content consistency.

4. **Modification Detection**
   - Introduce a controlled modification to the working copy.
   - Recalculate its SHA256 hash.
   - Identify the difference between the original and modified hashes.
   - Confirm that the original artifact remains unchanged.

5. **Evidence Documentation**
   - Generate an evidence register.
   - Record the evidence ID, handler, collection time, source path, and hash.
   - Create a basic chain-of-custody record.

6. **Endpoint Telemetry Correlation**
   - Identify a relevant Sysmon file creation event.
   - Locate the corresponding event in Wazuh archives.
   - Examine the event's host, user, process, timestamp, and target file fields.
   - Understand the limitations of file creation telemetry.

### Learning Outcomes

After completing the lab, the analyst should be able to:

- Explain evidence integrity and chain of custody.
- Use SHA256 to identify changes in file content.
- Distinguish original evidence from a working copy.
- Maintain basic evidence documentation.
- Interpret a Sysmon Event ID 11 record.
- Explain why hashes and custody records serve different purposes.
- Distinguish confirmed observations from conclusions that the available evidence cannot establish.

### Success Criteria

The lab is considered successful when:

- The evidence artifact is created and identified.
- Its initial SHA256 hash is recorded.
- The working copy initially matches the original.
- A controlled modification changes the working-copy hash.
- The original retains its initial hash.
- Evidence register and custody CSV files are generated.
- Relevant Wazuh archive telemetry is identified and interpreted.

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

