# Getting Started — Environment Setup (Do This Before Phase 1, Version-1)

You have zero coding/database background — this guide assumes that. Follow every step in order. Don't skip anything even if it looks obvious.

## What you're installing and why

| Tool | Why you need it |
|---|---|
| JDK 8 | WebSphere ND runs on Java 8; your app code compiles against it |
| Git | Version control — tracks every change you make |
| Maven | Builds your Java code into deployable EAR/WAR files |
| VS Code (+ Java Extension Pack) | Where you'll write and read code |
| DBeaver (or pgAdmin) | GUI tool to view/query PostgreSQL — no command-line SQL memorization needed |
| Postman | Lets you test APIs by clicking, not writing curl commands |

## Step 1 — Install JDK 8
1. Download IBM Semeru / AdoptOpenJDK 8 (search "Temurin JDK 8" — free, matches what WebSphere expects)
2. Install it, accepting defaults
3. Open a terminal (Command Prompt on Windows, Terminal on Mac/Linux) and run:
   ```
   java -version
   ```
4. You should see something like `openjdk version "1.8.0_XXX"`. If you see "command not found," the JDK isn't on your PATH — we'll fix that when we get here in Version-1.

## Step 2 — Install Git
1. Download from git-scm.com, install with defaults
2. Verify:
   ```
   git --version
   ```
3. Set your identity (only needs to be done once):
   ```
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   ```

## Step 3 — Install Maven
1. Download from maven.apache.org
2. Extract it, add the `bin` folder to your PATH (we'll walk through this in detail in Version-1 if you're unsure how)
3. Verify:
   ```
   mvn -version
   ```

## Step 4 — Install VS Code
1. Download from code.visualstudio.com
2. Open VS Code → Extensions (left sidebar) → search "Extension Pack for Java" → Install
3. This gives you code completion, error highlighting, and a "Run" button — you don't need to memorize Java syntax to get started

## Step 5 — Install DBeaver
1. Download from dbeaver.io (Community Edition, free)
2. This is a GUI — you'll browse tables, run queries by clicking, and see results in a spreadsheet-like view instead of a black terminal screen
3. We'll connect it to PostgreSQL once that's installed in Phase 1, Version-4

## Step 6 — Install Postman
1. Download from postman.com
2. Used to test the apps we build (e.g., "did SIM activation actually work?") by clicking Send instead of writing command-line requests

## Step 7 — VM Software (for your 5-VM lab)
You'll need a hypervisor to run the 5 VMs from ADR-003. Options:
- **VirtualBox** (free, works on Windows/Mac/Linux) — recommended for a fresher, simplest UI
- **VMware Workstation Player** (free for personal use) — alternative

Install whichever one; we'll create the actual VMs together in Phase 1, Version-2.

## Checklist before Version-1
- [ ] `java -version` works
- [ ] `git --version` works
- [ ] `mvn -version` works
- [ ] VS Code opens with Java extension installed
- [ ] DBeaver installed (don't need to connect yet)
- [ ] Postman installed
- [ ] VirtualBox (or VMware) installed

Once all boxes are checked, you're ready for Phase 1, Version-1, Sprint-1.
