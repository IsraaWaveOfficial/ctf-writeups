Behind the Scenes CTF Reverse Engineering Challenge

Hello everyone\! This is my first Reverse Engineering CTF ever.

Before we start, I would like to say that you must have some basics of reverse engineering and you have to know the definitions of this field.

Ok, let's get started\! :)

### **Challenge Overview**

* **Challenge Name:** Behind the Scenes  
* **Challenge Description:** After struggling to secure our secret strings for a long time, we finally figured out the solution to our problem: Make decompilation harder. It should now be impossible to figure out how our programs work\!  
* **Challenge Difficulty:** Very Easy  
* **Challenge Link:** [Behind the Scenes on HTB](https://app.hackthebox.com/challenges/Behind%2520the%2520Scenes)

### **Step 1: Initial File Check** 

First of all, we download the challenge file from Hack The Box. Before jumping into big tools, it's a good habit to know what we are dealing with.The challenge file is a Linux ELF (64-bit) binary. Since I am performing Static Analysis using Ghidra, I can analyze the file directly on Windows without needing to execute it.

### **Step 2: Importing to Ghidra**

We import the file into **Ghidra** so we can decompile it and see what is hidden inside this challenge binary.

### **Step 3: Searching for the Secret String**

As usual in many Hack The Box challenges, we can search for specific keywords like "password" or "flag" to see where the program processes our inputs. To do this in Ghidra, go to the top menu, click on **Window**, and choose **Defined Strings**.

Now, type **"password"** in the search bar. As you can see, one item pops up, so we double-click on it to go to its location in the memory.

### **Step 4: Cracking the Developer's Trick**

After that, the flag shows up, but it looks messy and broken into small parts:

`"Itz\0_0n\0Ly_\0UD2\0> HTB{%s}\n"`

**Why does it look like this? Here is the secret:** The developer used a smart trick to confuse us and confuse the decompilation tools\!

1. **The `\0` (Null Bytes):** In computer memory and programming languages like C, `\0` tells the program that the text has ended. The developer put them between the words so the tools get confused and think these are just scattered letters, not one complete password.  
2. **The `UD2`:** This is an instruction in Assembly language that forces the program to crash or stop suddenly if someone tries to reverse engineer it.

But our analytical brain can fix this\! We need to collect all the words into one single line. To do that, right-click on the start of the flag (at address `001021b`), select the whole messy text, go to **Data** in the menu, and click on **String**.

### **Step 5: Extracting the Final Flag**

Ghidra will now merge the text for us. All we have to do now is clean it up by deleting the extra `\0` symbols and the `>` sign that the developer added to confuse us.

After cleaning the string, the final look of the flag is: **`HTB{Itz_0nLy_UD2}`**

Well done, you did it with me\! 🎉 See you in the next challenge\!

