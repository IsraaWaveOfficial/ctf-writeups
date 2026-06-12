# Writeup: My Journey with crackme.exe

⚠️ **SPOILER ALERT:** This document contains the full solution, username, and password for the challenge. If you want to solve it by yourself, stop reading here!

---

## Technical Details
### Challenge Information:
* **Challenge Name:** whekkees's my first simple crack me by whekkees
* **Challenge Link:** [crackmes.one](https://crackmes.one/crackme/69b18083ddd6176826ae8947)
* **File Name:** `crackme.exe`
* **Tools Used:**
  * **Virtualization:** VirtualBox (Windows VM & Ubuntu VM) 
  * **Static Analysis:** Ghidra (Used on Windows and Ubuntu)
  * **Dynamic Analysis:** x64dbg (Used on Windows to trace crashes)
  * **Automation:** Python Fuzzer
  * **Final Execution:** Wine (Linux environment to get the Access message)

---

## Introduction
I spent **two days** trying to solve this challenge. Even though it was classified as "Easy," it took me some time because I am a beginner and still learning from CTF challenges. 

At first, I tried to solve it with the help of Gemini AI, but I failed. I felt hopeless and thought that Gemini was not helpful. To save my time, I decided to read the solution from a website. 

I thought there was a bug in the program because when I ran it on a real Windows machine, it crashed and closed immediately even when I entered the correct username and password! It never gave me the success message. 

But I did not stop there. I wanted to understand **why** it crashed. I analyzed the program completely and used a fuzzer to find the truth. 

Here are the important lessons I discovered from my failure:

---

## 3 Important Lessons I Learned

### Lesson 1: Hardcoded Hex and Data Encryption
When the username and password appeared in the decompiler, I did not recognize them quickly. This taught me to study how data is hidden and encrypted in challenges. I discovered that the credentials were encrypted in **Hex (Hexadecimal)** as numbers inside the decompiler. 
* **Future Goal:** I will always keep an eye on encryption methods when solving any CTF challenge or when I start doing Bug Bounty in the future.


### Lesson 2: The Windows vs. Linux Trick (Anti-Debugging)
The second lesson is about the environment. The challenge file was a `64-bit .exe` made for Windows. But it crashed on Windows even when I used the correct inputs!

I used **x64dbg** to understand the crash. I typed different passwords and followed the program in the debugger many times. Every time I entered the passwords, the program crashed. Even with the correct username and password, it still crashed!

This was a clever trick by the designer. But when I ran the program inside Linux using **Wine**, it worked perfectly and gave me the **`Access`** message! Some challenge programs run much better in Linux because it bypasses the system traps.



### Lesson 3: Challenges with "No Specific Flag"
I learned that some challenges do not have a standard flag (like random characters inside `flag{...}`). Sometimes, the challenge just ends with a success message like **`Access`**, and that message itself is the proof of completion.

---

### Step 1: Testing with Fuzzing
I designed a fuzzing strategy to test the binary with different payload lengths, and I used AI assistance to generate the helper Python script (`fuzzer.py`). 

When I entered long strings starting with `qwert` or `whekkees`, the program crashed immediately with **Exit Code 53**. This showed me that the program has a defense mechanism to stop people from breaking it.

### Step 2: Analyzing with Ghidra
I opened the file in **Ghidra** to see the code inside.

#### 1. Bypassing the Trap (Anti-Debugging)
The first function (`FUN_1400023f0`) checks the Windows system time and process ID to see if someone is analyzing it. If it detects a test environment, it crashes. 

To fix this, I ran the program inside Ubuntu vmware using **Wine**. Wine gave the program fake, stable numbers, which bypassed the trap and stopped the crashes.

#### 2. Finding the Username and Password
I search on string ‘password’ from top toolbar on window tab 
I looked at the main function (`FUN_1400012a0`) in Ghidra's Decompiler:

* **The Username:** I found Hex numbers (`0x6b656877` and `0x7365656b`). I converted them to regular text (ASCII) and got: **`whekkees`**.
* **The Password:** I found another Hex number (`0x72657771`) mixed with the letter `t`. I converted it to text and got: **`qwert`**.

---

## Step 3: Running the Program
I ran the program normally in the terminal using Wine:

```bash
wine crackme.exe

```

* **Login:** `whekkees`
* **Password:** `qwert`

**The Result:** The message after entering them is: **`Access`** (With no specific flag printed).

---

## Conclusion
The challenge is complete! I solved it by using Wine to bypass the system check, and Ghidra to read the secret credentials directly from the code.

