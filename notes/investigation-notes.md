# Lab 143 – Investigation Notes

## 1. Investigation Objective

The objective was to demonstrate basic digital evidence handling practices using a controlled file artifact.

The investigation focused on:

- Creating and identifying an evidence artifact.
- Recording file metadata.
- Calculating its SHA256 hash.
- Creating a separate working copy.
- Comparing original and working-copy hashes.
- Introducing a controlled modification.
- Verifying that the original remained unchanged.
- Recording evidence-handling activities.
- Validating file creation telemetry in Wazuh archives.

## 2. Lab Environment

| Attribute | Value |
|---|---|
| Hostname | DESKTOP-9MMM37V |
| Operating System | Windows 11 Pro |
| PowerShell | 7.7.0 |
| Monitoring | Sysmon |
| SIEM | Wazuh 4.13.1 |
| Evidence ID | EVD-143-001 |

## 3. Workspace Preparation

Created the investigation directory:

`C:\DFIR-Lab143`

Subdirectories:

- `original`
- `working`
- `metadata`
- `logs`

All four directories were successfully created.

## 4. Controlled Evidence Artifact

Created the file:

`C:\DFIR-Lab143\original\suspicious-script.ps1`

The file contained harmless test statements:

- A lab identification comment.
- A message indicating an evidence integrity validation test.
- A message identifying the script as a harmless test artifact.

The script was not executed.

### File Metadata

| Attribute | Value |
|---|---|
| File name | suspicious-script.ps1 |
| Full path | C:\DFIR-Lab143\original\suspicious-script.ps1 |
| File size | 138 bytes |
| Creation time | 2026-11-23 10:15:44 |
| Last write time | 2026-11-23 10:15:44 |

## 5. Initial SHA256 Calculation

The original file was hashed using PowerShell:

`Get-FileHash -Path $SourceFile -Algorithm SHA256`

### Result

- Algorithm: SHA256
- SHA256:

`4024625A3BF2E09F3A094472CE3B398FB42143C5DB3882346EC64EDD4B1D2F44`

This value was recorded as the initial reference hash for the controlled artifact.

## 6. Evidence Registration

An evidence identifier was assigned:

`EVD-143-001`

The evidence metadata included:

| Field | Value |
|---|---|
| Evidence ID | EVD-143-001 |
| File name | suspicious-script.ps1 |
| Source path | C:\DFIR-Lab143\original\suspicious-script.ps1 |
| Collection time | 2026-11-23 10:16:40 +05:30 |
| Hash algorithm | SHA256 |
| SHA256 | 4024625A3BF2E09F3A094472CE3B398FB42143C5DB3882346EC64EDD4B1D2F44 |
| Collected by | DESKTOP-9MMM37V\Dell |
| Hostname | DESKTOP-9MMM37V |

The metadata was exported to:

`C:\DFIR-Lab143\metadata\evidence-register.csv`

The exported CSV was read back to confirm that the evidence information had been written.

## 7. Working Copy Creation

A separate working copy was created:

`C:\DFIR-Lab143\working\suspicious-script.ps1`

The original and working-copy files were independently hashed.

### Hash Comparison

| Attribute | SHA256 |
|---|---|
| Original | 4024625A3BF2E09F3A094472CE3B398FB42143C5DB3882346EC64EDD4B1D2F44 |
| Working copy | 4024625A3BF2E09F3A094472CE3B398FB42143C5DB3882346EC64EDD4B1D2F44 |

Result:

`Match = True`

The matching hashes demonstrated that both files had identical content at the time of comparison.

## 8. Chain-of-Custody Record

A CSV custody record was created:

`C:\DFIR-Lab143\metadata\chain-of-custody.csv`

Two activities were recorded:

| Timestamp | Action | Location |
|---|---|---|
| 2026-11-23 10:16:40 +05:30 | Evidence registered | Original evidence file |
| 2026-11-23 10:18:22 +05:30 | Working copy created and verified | Working-copy file |

Both records included the evidence ID, handler, location, SHA256 value, and notes.

The CSV was imported and displayed to validate its contents.

## 9. Controlled Modification Test

A harmless comment was appended to the working copy:

`# Controlled modification for Lab 143`

The original file was not modified.

Both files were hashed again.

### Post-Modification Hash Comparison

| File | SHA256 |
|---|---|
| Original | 4024625A3BF2E09F3A094472CE3B398FB42143C5DB3882346EC64EDD4B1D2F44 |
| Modified working copy | B7C7EFA742F9E32F1AD353781571F7424EE17ED664444DCB9B6DA78B34A0FAA4 |

Result:

`Match = False`

The working-copy hash changed after its contents were modified.

## 10. Original Integrity Verification

The original file was hashed again and compared with its initial reference hash.

| Attribute | Result |
|---|---|
| Initial SHA256 | 4024625A3BF2E09F3A094472CE3B398FB42143C5DB3882346EC64EDD4B1D2F44 |
| Final SHA256 | 4024625A3BF2E09F3A094472CE3B398FB42143C5DB3882346EC64EDD4B1D2F44 |
| Original intact | True |

The original artifact retained its initial hash throughout the controlled modification test.

## 11. Wazuh Archive Investigation

A Sysmon Event ID 11 record was located in Wazuh archives.

### Event Details

| Attribute | Value |
|---|---|
| Event ID | 11 |
| Event description | File created |
| Event Record ID | 2300254 |
| Hostname | DESKTOP-9MMM37V |
| Agent ID | 001 |
| Agent name | DESKTOP-9MMM37V |
| User | DESKTOP-9MMM37V\Dell |
| Process | PowerShell 7.7.0 |
| Process ID | 14652 |
| Target file | C:\DFIR-Lab143\working\suspicious-script.ps1 |
| Sysmon UTC time | 2026-11-23 04:47:35.768 |
| Wazuh archive timestamp | 2026-11-23 10:17:36.772 IST |
| Decoder | windows_eventchannel |

### Interpretation

The Sysmon event recorded file creation activity involving the working-copy path.

The event was present in the Wazuh archives index, demonstrating that the endpoint event was collected and ingested.

The event does not contain the file's SHA256 hash. Hash verification was performed separately using PowerShell.

## 12. Final Workspace Contents

The final directory inspection showed:

| File | Size |
|---|---:|
| chain-of-custody.csv | 571 bytes |
| evidence-register.csv | 338 bytes |
| original\suspicious-script.ps1 | 138 bytes |
| working\suspicious-script.ps1 | 177 bytes |

The `logs` directory was created but remained empty.

## 13. Findings

### Confirmed

- The controlled artifact was created successfully.
- The initial SHA256 hash was calculated.
- Evidence metadata was recorded in a CSV register.
- The working copy initially matched the original.
- The working-copy hash changed after controlled modification.
- The original file retained its initial SHA256 hash.
- A basic chain-of-custody CSV was created and validated.
- Wazuh archives contained a Sysmon Event ID 11 record for the working-copy creation.

### Not Established

- Authenticity or provenance of the artifact before its creation in this lab.
- Whether the artifact had been altered before the initial hash calculation.
- A complete custody history covering every subsequent handling action.
- A forensic acquisition from a real incident.
- A file-content hash directly recorded by Sysmon.

## 14. Conclusion

The investigation demonstrated the practical relationship between evidence integrity and chain of custody.

SHA256 comparisons detected the controlled modification of the working copy, while the unchanged original hash supported the conclusion that the original artifact remained unchanged during the test.

The evidence register and custody CSV provided a basic handling record. Wazuh archive validation added supporting endpoint telemetry for the working-copy creation.

The exercise reinforces an important DFIR principle:

**Preserve the original, examine a working copy, verify integrity, and document every evidence-handling action.**
