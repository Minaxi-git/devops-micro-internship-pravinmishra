# Assignment 6 — Building an AI-Assisted Git Safety Net (PR Ready Check)

Part of the DevOps Micro Internship (DMI) Cohort 3 with Agentic AI

---

## Purpose

In Week 2 you built Claude Code hooks that block a dangerous action *before* it happens (`PreToolUse`), and a restricted skill that could look but not touch (`allowed-tools` without `Write`). In this assignment you will discover that Git has the exact same idea, decades older: a **pre-commit hook** that blocks a commit before it's created.

You will build both halves of a real "PR Ready" workflow:

1. A **Git hook that follows fixed rules** — scans staged changes for hardcoded secrets and oversized files and refuses the commit. No AI involved, no guessing, just a rule that gives the same answer every time.
2. A **restricted Claude Code skill** (`/pr-ready`) that reads your staged diff and drafts a Pull Request title, description, and a short list of things worth a second look — the kind of judgment a fixed rule can't make (mixed changes, missing context, unclear intent). The skill never commits, pushes, or opens the PR. You do that yourself, using its draft as a starting point.

This mirrors the Agentic Loop from Week 3's Linux triage assignment: **Gather → Analyze → Human Act → Verify**. The hook and the skill both gather and analyze; only you act.

---

# Task 0 — Confirm Your Fork and Create a Feature Branch

## Goal

Confirm you are working in your own fork, then create a dedicated branch for this assignment.

### Evidence

#### Screenshot 1 — Output of git remote -v and git branch showing the new branch

![git remote -v and git branch showing the new branch](screenshots/ConfirmForkInterviewsUpstream.png)

---

### Notes

**1. Why create a dedicated branch instead of doing this work on main?**

Creating a dedicated branch instead of working directly on main is one of the most critical best practices in modern DevOps and software engineering. It is a concept known as Feature Branch Workflow.Here is exactly why production environments never allow direct commits to main:
1. It Protects the Production EnvironmentThe main branch represents the stable, working version of your software (or in your case, your official assignment portfolio). Working on a dedicated branch ensures that if you write a buggy script or accidentally break a configuration file, your stable version remains unharmed.
2. It Enables the Code Review Process (Pull Requests)In a professional setting, you never merge your own code directly into production.You do your work on a feature branch.You push that branch to GitHub and open a Pull Request (PR).Senior engineers or automated tests review your code to catch bugs before it gets merged into main.
3. It Allows You to Multi-Task on Different IssuesImagine you are halfway through a complex script on main, and suddenly your instructor tells you to fix a critical error in an earlier assignment. If you are on main, your broken script is stuck in your way.With branches: You simply switch off your unfinished feature branch, jump back to main, fix the quick bug, push it, and switch right back to your feature branch without losing any work.
4. It Keeps Git History Clean and ReadableWhen teams work together, working on separate branches allows Git to organize commits cleanly. Instead of a messy, tangled timeline of half-finished thoughts on main, the history shows clean blocks of features being added one complete piece at a time.

---

# Task 1 — Stage a Change With Realistic Risk

## Goal

On your own fork of this repository (the one you've been submitting your DMI work in since onboarding), create a new branch and stage a change that a real reviewer should catch: a hardcoded-looking secret and a leftover debug statement.

### Evidence

#### Screenshot 1 — Output of  `git status` showing the staged file on feature/ai-pr-ready

![git status` showing the staged file on feature/ai-pr-ready](screenshots/showing-the-staged-file-on-feature-ai-pr-ready.png)

---

### Notes

**1. Why does this assignment use an obviously fake key instead of a real one?**

This assignment explicitly uses an obviously fake AWS key (AKIAABCDEFGHIJKLMNOP) for one critical DevOps safety reason: it prevents your cloud infrastructure from being instantly compromised, exploited, and hit with massive unexpected bills.Here is exactly what happens behind the scenes if you push a real credential to a public Git repository like GitHub:
1. The GitHub "Leaked Secret" RadarGitHub runs automated scanners that continuously watch every line of code pushed to public repositories.If you push a real key: Within seconds, GitHub's security algorithms will spot the valid AWS pattern, alert Amazon Web Services, and temporarily lock down your AWS account or key to protect you.If you push a fake key: The automated scanner recognizes it is an invalid placeholder string, allows your file upload to proceed cleanly, and triggers no security alerts.
2. Malicious Script Crawlers (Bots)Hackers deploy automated scrapers that comb through public GitHub commits every second, explicitly searching for terms like AWS_ACCESS_KEY_ID.If a bot finds a real key, it will instantly log into your AWS console.Within minutes, they will spin up dozens of high-powered, expensive cloud servers to mine cryptocurrency or launch cyberattacks.This can stick the fre tier users with thousands of dollars in charges in less than an hour.
3. Debug Logs Are Permanently PublicYour script includes the line: echo "DEBUG: token is $AWS_ACCESS_KEY_ID".If this script ever runs inside an automated system (like GitHub Actions), that token will be printed out in plain text inside the execution logs for anyone to see. In a professional DevOps pipeline, you must never print or hardcode secret variables into your scripts.

---

# Task 2 — Write a Real Git Pre-Commit Hook

## Goal

Create a tracked, shareable pre-commit hook that blocks a commit containing secret-like patterns or files over 1MB.

### Evidence

#### Screenshot 2 — `hooks/pre-commit` open in VS Code showing the full script

![hooks/pre-commit](screenshots/hooks-pre-commit.png)

---

#### Screenshot 3 — Output of `git config core.hooksPath` confirming it points to `hooks`

!['git config core.hooksPath`](screenshots/HooksGitConfig.png)

---

### Notes

**1. Why is `hooks/pre-commit` tracked in the repo instead of living only in `.git/hooks/`?**

Tracking hooks/pre-commit in your visible project directory instead of leaving it hidden inside .git/hooks/ is a standard practice in professional DevOps for two critical reasons: team-wide sharing and version history control.
1. Hidden .git/ Folders Are Never UploadedThe default .git/ directory on your computer is your private local workspace database.
    - The Problem: When you run git push, Git deliberately ignores the contents of the hidden .git/ folder. It never uploads your private configurations, custom aliases, or local hooks to GitHub.
    - The Result: If you write a brilliant safety script and leave it inside .git/hooks/, none of your fellow engineers or grading mentors will ever see it. When they clone your repo, their local hooks folder will be completely empty, and they will lack your safety guardrails.
2. It Automates Team-Wide Security GuardrailsBy moving the hook script out into a regular tracked folder (hooks/pre-commit), it turns the script into standard project source code.
    - Collaboration: When a teammate clones your repository, the hooks/ directory downloads onto their machine automatically.
    - Easy Activation: All they have to do to activate the exact same security guardrails on their own computer is run a single setup script or type:git config core.hooksPath hooks.
This ensures that every developer on the project is automatically held to the exact same security standards (like blocking leaked secrets or blocking massive files over 1MB) before code leaves their laptop.
3. It Provides Version Tracking HistoryIf you edit or upgrade your safety hook script directly inside the hidden .git/hooks/ folder, Git cannot track your changes.

    By tracking it in your visible repository structure instead:
    - You can see exactly who modified the hook script using git log.
    - You can roll back to an older version of the hook script if a new update accidentally breaks someone's pipeline.
    - You can write clear commit messages explaining why a new regex expression was added to look for secret keys.
---

**2. Compare this to `PreToolUse` from Week 2 Assignment 6. What does each one intercept, and what do they have in common?**

Both have the same architectural security principle in action: intercepting an action to validate safety rules before allowing it to proceed.

The Week # 2 Hook:  It intercepts autonomous execution by scanning the AI’s generated command payload for dangerous patterns or destructive keywords (like terraform destroy or rm -rf) before the script hits your operating system shell.

The Week #6 Hook:  It intercepts human oversight by scanning your staged files for leaked access keys (AKIA...), unencrypted private keys, or bulky files larger than 1MB before they are written to your Git database.

---

# Task 3 — Prove the Hook Blocks the Risky Commit

## Goal

Attempt to commit the staged file from Task 1 and show the hook rejecting it.

### Evidence

#### Screenshot 4 — Terminal showing `git commit` rejected with the hook's "BLOCKED" message naming the exact file

Add your screenshot here.

---

### Notes

**1. Which line in `hooks/pre-commit` matched your fake key, and why did it match?**

Add your answer here.

---

**2. Could this hook have caught a poorly-named variable that stores a secret without the `AKIA` prefix? What does that tell you about the limits of a fixed rule like this?**

Add your answer here.

---

# Task 4 — Build the `/pr-ready` Skill

## Goal

Create a manually invoked Claude Code skill that reads your staged changes and produces a PR-readiness report and a draft PR description — without writing, committing, or pushing anything itself.

### Evidence

#### Screenshot 5 — `SKILL.md` frontmatter showing `allowed-tools: Bash, Read, Grep` (no `Write`) and `disable-model-invocation: true`

Add your screenshot here.

---

#### Screenshot 6 — `/pr-ready` output while the risky file is still staged, showing it flagged the secret and/or debug statement

Add your screenshot here.

---

### Notes

**1. Why does `/pr-ready` have `Bash` and `Read` but not `Write`?**

Add your answer here.

---

**2. The pre-commit hook and `/pr-ready` both looked at the same staged diff. Did they flag the same things? What did one catch that the other didn't?**

Add your answer here.

---

# Task 5 — Fix the Issues and Re-Verify

## Goal

Remove the secret and debug statement, then prove both gates now pass clean.

### Evidence

#### Screenshot 7 — `git commit` succeeding after the fix (no BLOCKED message)

Add your screenshot here.

---

#### Screenshot 8 — Second `/pr-ready` run showing a clean risk report and a drafted PR title + description

Add your screenshot here.

---

### Notes

**1. What exactly did you change to satisfy the pre-commit hook?**

Add your answer here.

---

# Task 6 — Push and Open a Pull Request Using the AI Draft

## Goal

Push your branch and open a real Pull Request, using `/pr-ready`'s drafted title and description as your starting point — read it critically and edit before you use it.

**Important:** Open this Pull Request with base repository set to **your own fork** — not the shared upstream `pravinmishraaws/devops-micro-internship-pravinmishra` repository. This assignment's hook and skill files are your own practice work, not a change meant for the shared class repo.

### Evidence

#### Screenshot 9 — Your Pull Request showing the base repository is your own fork, plus the title and description, with the `/pr-ready` draft visible for comparison (paste it in the PR conversation or your notes below)

Add your screenshot here.

---

#### PR Link

Add your PR URL here...

---

### Notes

**1. What, if anything, did you edit in the AI's drafted PR description before using it? Why?**

Add your answer here.

---

**2. If you had blindly copy-pasted the AI's draft without reading it, what could go wrong?**

Add your answer here.

---

**3. Why does this PR need to target your own fork instead of the shared upstream repository?**

Add your answer here.

---

# Task 7 — Map the Workflow to the Agentic Loop

## Goal

Explain this assignment's workflow using the same Gather → Analyze → Human Act → Verify structure from Week 3.

### Notes

**1. Which step(s) represent Gather?**

Add your answer here.

---

**2. Which step(s) represent Analyze?**

Add your answer here.

---

**3. Which step is Human Act, and why must a human — not Claude — run `git commit`, `git push`, and open the PR?**

Add your answer here.

---

**4. Which step is Verify?**

Add your answer here.

---

**5. In one or two sentences: why do you need *both* the fixed-rule pre-commit hook and the AI skill? Isn't one enough?**

Add your answer here.

---

# Task 8 — LinkedIn Post

## Goal

Publish a LinkedIn post summarizing what you built and what you learned about combining fixed-rule safety checks with AI-assisted review.

### Evidence

#### LinkedIn Post URL

Add your LinkedIn post URL here...

---

## Key Learnings

Add 3-5 bullet points on what you learned this week.

-
-
-

---

# Submission Instructions

- Ensure `hooks/pre-commit` and `.claude/skills/pr-ready/SKILL.md` are committed to your GitHub repository
- Add all required screenshots to your submission
- All written answers must be in your own words
- Do not use a real secret or credential anywhere in your submission — the fake key in Task 1 is intentional and must stay clearly fake
- Open your Pull Request against your own fork, not the shared upstream repository
- Push your final changes to your forked repository
- Include your PR link and LinkedIn post URL

---

## GitHub Repository URL

Paste your forked repository URL here:

`Add your URL here`

---

# Completion Checklist

- [ ] Branch `feature/ai-pr-ready` created with a staged file containing a fake secret and a debug statement
- [ ] `hooks/pre-commit` created and tracked in the repo (not only in `.git/hooks/`)
- [ ] `core.hooksPath` configured to point at `hooks/`
- [ ] Pre-commit hook shown blocking the risky commit
- [ ] `.claude/skills/pr-ready/SKILL.md` created with correct `allowed-tools` (no `Write`) and `disable-model-invocation: true`
- [ ] `/pr-ready` run against the risky diff and shown flagging issues
- [ ] Risky file fixed; `git commit` succeeds cleanly
- [ ] `/pr-ready` re-run showing a clean report and drafted PR title/description
- [ ] Pull Request opened using the AI draft as a starting point, with your own fork as the base repository (not upstream), PR link included
- [ ] Agentic Loop mapping (Task 7) completed in your own words
- [ ] LinkedIn post published and URL submitted
- [ ] All required screenshots added
- [ ] GitHub repository URL provided

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
