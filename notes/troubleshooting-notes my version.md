# Troubleshooting Notes

## 1. PowerShell CSV Export Warning

### Observation

During CSV export, PowerShell 7.7.0 displayed:

`WARNING: Parameter 'NoTypeInformation' is obsolete. This parameter is obsolete and has no effect.`

### Cause

In modern PowerShell versions, `Export-Csv` does not include the type-information line by default.

The `-NoTypeInformation` parameter is therefore obsolete and has no effect.

### Impact

No evidence data was lost.

Both CSV files were successfully created and their contents were readable.

### Resolution

No corrective action was required.

For future PowerShell 7.7.0 commands, the parameter can simply be omitted:

`Export-Csv -Path $CustodyLog`

The warning does not indicate corruption or failure of the exported evidence records.

## 2. Original and Working-Copy Hashes Matched

### Observation

Immediately after copying the original artifact, both files produced the same SHA256 value.

### Interpretation

This was the expected result.

The matching hashes indicated that the working copy had identical content to the original at the time of verification.

No troubleshooting was necessary.

## 3. Hash Changed After Controlled Modification

### Observation

After appending a comment to the working copy, its SHA256 value changed.

Initial working-copy hash:

`4024625A3BF2E09F3A094472CE3B398FB42143C5DB3882346EC64EDD4B1D2F44`

Modified working-copy hash:

`B7C7EFA742F9E32F1AD353781571F7424EE17ED664444DCB9B6DA78B34A0FAA4`

### Interpretation

The content of the working copy changed, producing a different SHA256 hash.

This was the intended result of the controlled modification test.

The original file retained its initial hash.

## 4. Original File Integrity Check

### Observation

The final hash of the original file matched its initial reference hash.

### Interpretation

The original artifact remained unchanged during the controlled test.

The working-copy modification did not affect the original file.

## 5. Wazuh Sysmon Event Validation

### Observation

A Sysmon Event ID 11 record was found in Wazuh archives for:

`C:\DFIR-Lab143\working\suspicious-script.ps1`

### Relevant Fields

- Event ID: 11
- Event Record ID: 2300254
- Host: DESKTOP-9MMM37V
- Agent ID: 001
- Process: PowerShell 7.7.0
- Process ID: 14652
- Decoder: windows_eventchannel

### Interpretation

The endpoint generated a file creation event and Wazuh archives contained the corresponding record.

The event supports the observation that the working-copy file was created.

It does not independently verify the file's content or hash.

## 6. Important Forensic Limitations

### Hashes Do Not Prove Authenticity

A matching SHA256 hash demonstrates that two files have matching content at the time of comparison.

It does not prove:

- Who originally created the file.
- Where the file came from.
- Whether it was modified before collection.
- Whether the collection process was trustworthy.

### Sysmon Event ID 11 Does Not Record File Content

Event ID 11 provides file creation or overwrite telemetry.

It does not provide a SHA256 hash of the file contents in the observed event.

The hash was calculated separately using PowerShell.

### Chain-of-Custody Record Is Basic

The CSV currently records:

- Evidence registration.
- Working-copy creation and verification.

The later controlled modification was not added as a separate custody entry.

For a more complete record, every subsequent handling action should be documented with its timestamp, handler, action, location, and relevant integrity information.

### Controlled Lab Is Not a Forensic Acquisition

The test file was created specifically for this exercise.

The procedure demonstrates basic evidence-handling concepts, not a formal acquisition of evidence from a real incident.

