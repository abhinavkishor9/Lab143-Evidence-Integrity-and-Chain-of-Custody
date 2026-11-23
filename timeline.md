# Lab 143 – Investigation Timeline

**Date:** 23 November 2026  
**Host:** DESKTOP-9MMM37V  
**Evidence ID:** EVD-143-001  
**Timezone:** IST (UTC+05:30)

| Time (IST) | Activity | Result |
|---|---|---|
| 09:14 | Created the Lab 143 workspace and subdirectories | Success |
| 10:15:44 | Created `suspicious-script.ps1` in the original directory | Success |
| 10:16:40 | Calculated the initial SHA256 hash and registered the evidence | Success |
| 10:17:35.768 | Sysmon recorded creation of the working-copy file | Event ID 11 |
| 10:17:36.772 | Corresponding record appeared in Wazuh archives | Confirmed |
| 10:18:22 | Recorded working-copy creation and verification in the custody CSV | Success |
| 10:19:13 | Working-copy file showed a later modification time | Controlled modification |
| 10:20:46 | Exported the evidence register CSV | Success |
| Final verification | Compared initial and final original-file hashes | Match |
| Final verification | Compared original and modified working-copy hashes | Different |

## Key Investigation Events

### 1. Evidence Creation and Registration

A harmless PowerShell script was created as the controlled evidence artifact.

Its initial SHA256 hash was calculated and recorded in the evidence register.

### 2. Working-Copy Verification

A separate copy was created for examination.

The original and working-copy hashes initially matched.

### 3. Controlled Modification

A harmless comment was appended to the working copy.

Its SHA256 hash changed, while the original file retained its initial hash.

### 4. Endpoint Telemetry Validation

A Sysmon Event ID 11 record for the working-copy creation was located in Wazuh archives.

This confirmed that the file creation activity was recorded by endpoint monitoring.

## Final Outcome

The original artifact retained its initial SHA256 hash.

The modified working copy produced a different hash.

Evidence registration and basic custody records were generated successfully.

Wazuh archive validation provided supporting telemetry for the working-copy creation.

**Note:** The timeline combines PowerShell-observed timestamps and the corresponding Sysmon/Wazuh event timestamps. The custody CSV currently does not contain a separate entry for the later controlled modification.
