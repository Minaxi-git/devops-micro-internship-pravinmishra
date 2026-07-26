# Assignment 5 — Bash Script Automation Drill (OPS Checklist)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In this assignment, you will practice Bash scripting by building a series of small automation scripts covering environment setup, variables, arrays, loops, file conditionals, if-else logic, and functions. These scripts form the foundation of real-world Linux automation used in DevOps, cloud, and production support environments.

---

# Task 1 — Bash Environment & Workspace Setup

## Goal

Verify that Bash is available on your system and create a clean workspace for this assignment.

### Evidence

#### Screenshot 1 — Output of `echo $SHELL` and `bash --version`

![Output of `echo $SHELL` and `bash --version`](screenshots/echoandbashVersion.png)

---

#### Screenshot 2 — Output of `pwd` and `ls -lah` showing the scripts directory

![Output of `pwd` and `ls -lah` showing the scripts directory](screenshots/pwdLSLH.png)
---

### Notes

Answer the following in your own words:

**1. What is Bash?**

Bash is a command language interpreter and a Unix shell that reads and executes commands typed in a terminal or loaded from a script. It serves as the primary user interface for communicating with a computer operating system. Commands can automates tasks, manage files, and controls system software.

---

**2. What is the difference between shell and Bash?**

- Shell: This is an abstract, general term for any interface that lets a user interact with the operating system kernel by typing commands.

- Bash: This is a specific, concrete implementation of a shell (Bourne-Again SHell) created as a free software replacement for the original Bourne shell (sh).Think of shell as the generic vehicle category and Bash as a specific model of a car. 

---

**3. Why is it important to confirm the Bash version before writing scripts?**

- Feature Availability: Major versions introduce entirely new syntax and built-in capabilities, such as associative arrays which were added in Bash 4.0.

- Compatibility Protection: Writing code with features from Bash 5.0 will throw syntax errors and fail if executed on an older system like macOS, which defaults to Bash 3.2.

- Behavior Shifts: Minor changes in how commands or expansions operate between versions can introduce silent bugs if your script runs on an unverified version.

---

# Task 2 — Your First Bash Script

## Goal

Create your first Bash script, make it executable, and run it from the terminal.

### Evidence

#### Screenshot 1 — Content of `first-script.sh`

![Content of `first-script.sh](screenshots/firstScriptWithName.png)

---

#### Screenshot 2 — Output of `./first-script.sh`

![Output of `./first-script.sh`](screenshots/ExecutefirstScriptWithName.png)

---

#### Screenshot 3 — Output of `ls -l first-script.sh` showing executable permission

![Output of `ls -l first-script.sh` showing executable permission'](screenshots/PermissionsExecutefirstScriptWithName.png)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of `#!/bin/bash`?**

It informs the operating system to run the bash script commands and as a result of this commandthe o.s understands to use the Bash interpreter to tun the commands in the script file.

---

**2. Why do we use `chmod +x` before running a script?**

We want to execute the bash script so we make sure that the permissions on the file included executing it. We always perform check, validate changes (which we did with ls) then trust and deploy (in this case execute).
---

**3. What is the difference between running a script using `./script.sh` and `bash script.sh`?**

bash script.sh - the first command is bash this acts as a didatic command to the operating system to use the and run the bash script withitn the <FileName>.

./script.sh is more like a <path/FileName> and since a file could have on Read or Read+Write or Read+Execute etc permissions if we wish to use it as bash commands we need to explicitly have the #!/bin/bash command - if we want to have the bash interpretor execute the commands and also have to have the file posses the permission of being excutable.


---

# Task 3 — Variables: User Information Script

## Goal

Use variables to store and display user-related information.

### Evidence

#### Screenshot 1 — Content of `user-info.sh`

![Content of `user-info.sh`](screenshots/userinfoVIeditor.png)

---

#### Screenshot 2 — Output of `./user-info.sh`

![Output of `./user-info.sh`](screenshots/UserInfoutput.png)

---

### Notes

Answer the following in your own words:

**1. What is a variable in Bash?**

When we want to allow user to give values that might be changing (rather than a permanently fixed value, e.g. user name of the team emmber who is editing the file, since it migth not always be the same individual) - We can assign a label which can hold values. so a lable like "Fname" represented in the Bash script as "$Fname" would be a way that references the First name depending on whatevr value teh user has stored in that label. 

---

**2. Why should we avoid spaces around the `=` sign when creating variables?**

Bash's syntax does not allow for spaces when assigning value to a variable. If we add spaces Bash will read each of the words as separate commands instead of storing value.
Correct
    f_name="Minaxi"
Error:
    f_name = "Minaxi"
    Bash tries to run f_name as a command
    = } argument
    and "Minaxi"} as a fixed value
    No storage, no f_name variable  

---

**3. How do you access the value stored inside a Bash variable?**

Preceed the charchter '$' to the variable no spaces to access teh stored value. E.g. $f_name 

Example from our scripts:  
    echo "$course_name"
Here, $course_name returns the value stored inside the course_name variable. 

---

# Task 4 — Arrays & Loops: Tools Checklist Script

## Goal

Use arrays and loops to print a checklist of tools used in Bash scripting.

### Evidence

#### Screenshot 1 — Content of `tools-checklist.sh`

![Content of `tools-checklist.sh`](screenshots/tools-checklist.png)

---

#### Screenshot 2 — Output of `./tools-checklist.sh`

![Output of `./tools-checklist.sh`](screenshots/tools-checklist-Output.png)

---

### Notes

Answer the following in your own words:

**1. What is an array in Bash?**

An array can be thought of like a list. It allows users to call any of the data that is part of that list by just one variable name. See more below.


---

**2. Why are arrays useful in scripts?**

Whereas a regular variable has one to one correspondence, one variable to can store one value. An array allows us to store several values like in a grocery shopping list, or to-dO list. If one were not to use arrays one would need to have a variable for each of the individual data that the user needed to store. 

So a guest list using regular variables would look like 
    guest1:"Ram"
    guest2:"John"
    guest3:"Athena"

Whereas storing guest list in an array would be 
    guest_list=("Ram" "John" "Athena")
    
---

**3. What does `"${tools[@]}"` mean?**

You should always use double quotes around array expansions in Bash unless you have a specific, intentional reason to split the text.

The standard rule is to write "${array[@]}" rather than ${array[@]}.

Why you must use quotes - When you omit quotes, Bash performs word splitting and globbing (filename expansion) on the values inside your array. This alters how data is processed, especially if your array elements contain spaces e.g. Guest_List = "Ram Manohar" "Jackie Chan" "Sharon Stone".
The differences in expansion"${my_array[@]}" (The Correct Way): Expands each element as a separate, individually quoted string. Spaces inside elements are perfectly preserved.${my_array[@]} (Unquoted): Expands all elements, but then breaks them apart at every single space, turning one element into multiple pieces

---

**4. What is the purpose of the `for` loop in this script?**

For loop helps one to repeat an action for a set condition is valid. 
In this case it goes through the entire value list in tools until the list is exhausted.
In this script, the following line creates an array called tools: 
		tools=("bash" "nano" "chmod" "echo" "ls" "pwd")

The array stores multiple tool names under one variable name.
The for loop goes through the array and displays each tool one by one:
		for tool in "${tools[@]}"
do
   		 echo "Tool available for practice: $tool"
done
During each loop iteration, the current item is stored in the tool variable. 


---

# Task 5 — Loops: Number Counter Script

## Goal

Use loops to repeat a task multiple times.

### Evidence

#### Screenshot 1 — Content of `counter.sh`

![counter.sh contents](screenshots/countingloop.png)

---

#### Screenshot 2 — Output of `./counter.sh`

![counter.sh output after changing permissions](screenshots/outputcountingloop.png)

---

### Notes

Answer the following in your own words:

**1. What is a loop?**

A loop is a programming control structure that repeatedly executes a specific block of code as long as a certain condition remains true or until all items in a target collection have been processed. 

---

**2. Why do we use loops in Bash scripting?**

Instead of writing out the same instructions over and over again, a loop automates the repetition. It also checks for teh conditions met or unmet to continue repeatign teh tasks in loop and when to appropriately take the path of 'done'.

---

**3. How many times did the loop run in your script?**

It ran 5 times displaying 
        Step 1 completed
        Step 2 completed
        Step 3 completed
        Step 4 completed
        Step 5 completed
        Loop completed successfully
---

**4. What would you change if you wanted the loop to run 10 times?**

One would update the list of values to 1 through 10 for the array name 'number' as shown below. As well as since 10 is now a two digit number for the list to be shown as space representeing teh separation of values added quotes around the array inteh echo command.

    for number in 1 2 3 4 5 6 7 8 9 10
    do
        echo "Step "$number" completed"
    done

    echo "Loop completed successfully"


---

# Task 6 — Files & Conditionals: File Validation Script

## Goal

Use file checks and conditionals to verify whether files and directories exist.

### Evidence

#### Screenshot 1 — Output of `ls -lah ../test-folder`

![`ls -lah ../test-folder`](screenshots/ls-student-info-txt.png)

---

#### Screenshot 2 — Content of `file-check.sh`

![Content of `file-check.sh`](screenshots/contentFile-check.png)

---

#### Screenshot 3 — Output of `./file-check.sh`

![Output of `./file-check.sh`](screenshots/OutputtFile-check.png)

---

### Notes

Answer the following in your own words:

**1. What does `-d` check in Bash?**

In Bash, the -d operator tests whether a specific path exists and is a directory.When wrapped inside an if statement brackets ([ ... ]), it evaluates to true if the path points to a valid folder, and false if the path does not exist or points to a regular file (like a text document).
---

**2. What does `-f` check in Bash?**

-f: Checks if the path is a regular file (rather than a folder). In the If condition If a check is successful, the commands under then will run. Otherwise, the commands under else will run. Each conditional ends with fi.

---

**3. Why should file and directory paths be stored in variables?**

Storing paths in variables makes the script easier to read and update. If a path changes, we only need to update the variable instead of changing the same path in several places.
---

**4. What happens if the file does not exist?**

If the file does not exist, the -f check becomes false. Therefore, the commands under else will run, and the following message will be displayed:
File does not exist: ../test-folder/student-info.txt

---

# Task 7 — Conditionals: Pass or Retry Script

## Goal

Use if-else conditionals to make decisions based on a variable value.

### Evidence

#### Screenshot 1 — Content of `score-check.sh` with `score=85`

![Content of score-check.sh](screenshots/contentscorecheck.png)

---

#### Screenshot 2 — Output showing `Result: Pass`

![Output showing 85 Score as Pass ](screenshots/85ScorePass.png)

---

#### Screenshot 3 — Content of `score-check.sh` with `score=55`

Add your screenshot here.

---

#### Screenshot 4 — Output showing `Result: Retry`

![Output showing `Result: Retry`](screenshots/retryscore55.png)

---

### Notes

Answer the following in your own words:

**1. What is the purpose of if-else in Bash?**

The if-else statement provides decision-making logic in a script. It allows the script to evaluate a condition (such as checking if a file exists or if a command succeeded) and branch execution. If the condition evaluates to true, the script runs one block of code; if it evaluates to false, it runs an alternate block of code instead of just failing or stopping

---

**2. What does `-ge` mean?**

The -ge flag is a comparison operator that stands for greater than or equal to (>=). It is specifically used inside brackets [ ] or [[ ]] to compare integer values. For example, [ "$age" -ge 18 ] evaluates to true if the variable $age is 18 or any number higher.

---

**3. Why should conditions be tested with different values?**

esting conditions with different values—especially boundary and unexpected values—ensures your logic holds up under real-world scenarios. It helps you catch bugs where a script might work for a typical input but completely break or behave unpredictably when given zero, a negative number, a blank string, or an extremely large value

---

**4. How can conditionals help in automation scripts?**

Conditionals make automation scripts resilient and self-aware. Instead of blindly executing commands that might fail, conditionals allow a script to:
- Pre-verify environments: Check if necessary software tools or dependency packages are installed before starting an installation.
- Handle errors gracefully: Detect if a backup command failed, and immediately trigger a notification email instead of silently ignoring the issue.
- Enforce safety limits: Verify if a hard drive has enough free disk space (-ge) before attempting to download a massive dataset.

---

# Task 8 — Functions: Final Bash Automation Script

## Goal

Create a final Bash script using functions to organize reusable code.

### Evidence

#### Screenshot 1 — Content of `final-automation.sh`

![Content of `final-automation.sh`](screenshots/content-final-automation.png)

---

#### Screenshot 2 — Output of `./final-automation.sh`

![Output of `./final-automation.sh`](screenshots/output-final-automation.png)

---

#### Screenshot 3 — Output of `ls -lah` showing all created scripts

![Output of `ls -lah` showing all created scripts](screenshots/confirm-bash-scripts-in-folder-are-present.png)
---

### Notes

Answer the following in your own words:

**1. What is a function in Bash?**

A function is a reusable block of code designed to perform a specific task within a script. It acts like a mini-script inside your main script, grouped under a unique name. Once a function is defined, it can be executed (or "called") anywhere later in the script simply by typing its name.

---

**2. Why are functions useful in scripts?**

Code Reusability: Instead of copying and pasting the exact same 10 lines of code five different times throughout a script, you write it once inside a function and call it whenever needed.Modularity and Readability: They break complex scripts down into small, digestible, and isolated logical chunks, making the script significantly easier to read, organize, and debug.Local Variable Scoping: They allow you to use the local keyword to isolate variables inside the block, preventing functions from accidentally modifying or breaking variables in the main body of the script.

---

**3. Which functions did you create in this script?**

- print_header(): Generates a consistent, formatted visual banner at the top of the terminal output displaying the assignment name.
- print_user_details(): Outputs the specific metadata of the script author, including your full name and the assignment title.
- check_files(): Evaluates the system environment by checking if the targeted directory (../test-folder) and file (../test-folder/student-info.txt) exist.
- print_tools(): Iterates through the predefined list of software utilities to print a checklist of tools used in the lesson.

---

**4. How does this final script combine variables, arrays, loops, conditionals, files, and functions?**



Six elements are combined into a structured workflow:
- Funtions: - Four individual blocks (print_header, print_user_details, check_files, and print_tools) isolate distinct tasks. The script defines them first, then runs them sequentially at the bottom, which keeps the main execution path highly organized and clean.
- Variables: Global string variables (full_name, assignment_name, directory_path, and file_path) are defined at the very top of the script. 
- Arrays: The tools array holds a list of six utility strings ("bash", "nano", etc.) in a list.
- Loops: Inside the print_tools() function, a for loop safely unpacks the array using "${tools[@]}", iterating through each tool one by one to print them uniformly.
- Conditionals: The check_files() function employs if statements paired with fi evaluates the paths using the -d (directory check) and -f (file check) flags to branch output based on whether the targets exist.
- Files: The check-file function isolates a clean error handling if certain files and directories are not present using the IF-FI block and passing the paths and filenames through variables (../test-folder/student-info.txt) preparing the script to interact with external storage paths.Functions: 

---

# LinkedIn Post (Required)

## Evidence

#### LinkedIn Post URL

Paste your LinkedIn post URL here:

https://www.linkedin.com/posts/minaxi-punjabi_dmibypravinmishra-agenticai-aileadership-share-7487258983163088896--LUI/?utm_source=share&utm_medium=member_desktop&rcm=ACoAABqPfm0BltUSV7lY0aKhl1BWZCRBhv3K4iQ

---

#### Screenshot — Published LinkedIn post

![Published LinkedIn post Git Bash Script](screenshots/LinkedinGitBash.png)

---

# Submission Instructions

- Add all required screenshots in your submission
- Full name must be visible in required screenshots
- All script files must be created and run successfully
- Required notes must be answered clearly for every task
- Do not expose sensitive information (keys, passwords, credentials)

---

# Completion Checklist

- [ ] Task 1: Environment setup verified, workspace created (Screenshots 1–2, Notes answered)
- [ ] Task 2: First script created, executed, permissions verified (Screenshots 1–3, Notes answered)
- [ ] Task 3: Variables script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 4: Arrays and loops script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 5: Counter loop script created and run (Screenshots 1–2, Notes answered)
- [ ] Task 6: File validation script created and run (Screenshots 1–3, Notes answered)
- [ ] Task 7: Pass/Retry conditional script tested with both values (Screenshots 1–4, Notes answered)
- [ ] Task 8: Final automation script created and run (Screenshots 1–3, Notes answered)
- [ ] All scripts run without errors
- [ ] Full Name visible in all required screenshots
- [ ] LinkedIn post published and URL submitted
- [ ] No sensitive data exposed

---

## 📌 About DMI & CloudAdvisory

DevOps Micro Internship (DMI) is a project-based DevOps program run by Pravin Mishra (The CloudAdvisory) focused on real-world execution, systems thinking, and career readiness.

It helps learners build strong DevOps foundations with hands-on experience.

---

## 📌 Resources

- 🌐 DMI Official Website: https://dmi.pravinmishra.com?utm_source=github&utm_medium=readme  
- 🎓 University: https://university.pravinmishra.com?utm_source=github&utm_medium=readme  
- 💬 Discord Community: https://discord.pravinmishra.com?utm_source=github&utm_medium=readme  
- 📝 Blog: https://dmi.pravinmishra.com/blog?utm_source=github&utm_medium=readme  
- ▶️ YouTube Playlist: https://www.youtube.com/playlist?list=PLFeSNDtI4Cho  
- 🔗 Pravin Mishra (LinkedIn): https://www.linkedin.com/in/pravin-mishra-aws-trainer/  
- 🏢 CloudAdvisory (LinkedIn): https://www.linkedin.com/company/thecloudadvisory/

---

*This submission is part of DevOps Micro Internship (DMI) Cohort 3 — Agentic AI Track.*