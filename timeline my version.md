# Investigation Timeline
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

