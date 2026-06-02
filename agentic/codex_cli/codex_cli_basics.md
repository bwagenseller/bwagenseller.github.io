# Codex CLI Basics

# Links  

* [OpenAI Codex CLI docs](https://developers.openai.com/codex/cli?utm_source=chatgpt.com)  
* [Codex CLI Cheatsheet](https://shipyard.build/blog/codex-cli-cheat-sheet/?utm_source=chatgpt.com)  

# What is Codex CLI?  

<font color="purple">Codex CLI</font> is a terminal-based coding agent that runs locally on your machine. It can:  
* read your repo  
* modify files  
* execute shell commands  
* maintain sessions  

# Installation

!> Do _not_ install npm _or_ codex as root! The _only_ thing you should install as root is git.  

## Pre-Install  

_As root_, install git:  

1\. [Become root](/operating_systems/ubuntu/linux_notes?id=becoming-root).

2\. [Update all packages](/operating_systems/ubuntu/linux_notes?id=updating-upgrading-all-packages).  

3\. Install git: `apt install git`  

## Install Script  

> Do this as a regular user / non-root.  

1\. Install Node Version Manager (NVM): `curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash`  

2\. Source your bashrc: `source ~/.bashrc`  
* You should now be able to check your nvm version with: `npm -v`  

3\. Install Node.js itself: `nvm install --lts`  
* You should now be able to check your Node.js version with: `node -v`  

4\. Install Codex CLI: `npm install -g @openai/codex`  

5\. Export your API Key to `~/.bashrc`: `echo export OPENAI_API_KEY="your_key_here"' >> ~/.bashrc`  

6\. Source your bashrc (again): `source ~/.bashrc`  


## Updating  

Occasionally, you will have to update Codex CLI. To do so, run: `npm update -g @openai/codex`  

## API Key  

You installed the API key above, but if you ever have to edit it, its in `~/.bashrc` as `OPENAI_API_KEY`  

---  

# Running Basics  

## Core Workflow

The basic loop is:  
1\. You describe a task in natural language  
2\. Codex CLI proposes a plan and/or takes actions (file reads, edits, shell commands)  
3\. You approve, deny, or redirect  
4\. Repeat  

## Directory Is Critical  

The directory you launch Codex CLI from is critical: this directory is considered the <font color="purple">project root</font> - the directory you launch Codex CLI from is considered 'the codebase':  
* If you have too many unrelated things in the directory it can end up confusing / slowing Codex.   
* If you reference a file, it will look in the codebase and not elsewhere (unless specified).  
* Its a 'soft sandbox' - it can operate on things in that directory but (usually) will not disturb unrelated projects.  

## Launching

Start an interactive session in any project directory:

```bash
cd /your/project
codex
```  

Codex CLI now consider the base directory as the 'project'.  

## Resuming  

You can resume a session you were working on before using the `resume` parameter, like so:  
```bash
cd /your/project
codex resume  
```  

You will then be prompted with several previous sessions - pick the one you want.  Or, conversely, you could use `codex resume --last` to just pick the last one used.  

## New Session   

## AGENTS.md  

`AGENTS.md` is a pre-defined set of instructions where you tell Codex how to navigate the repo, what commands to run, and what standards to follow. This file is read in as you launch Codex, and gives Codex a persistent set of instructions. The base file is located in the <font color="green">project root</font>. An example of `AGENTS.md`:  
```markdown  
  ## Project Overview
  - This is a Docsify-based documentation project for learning Codex CLI.
  - Prefer small, readable markdown changes over big restructures.

  ## Working Rules
  - Do not rename files unless the task requires it.
  - Preserve existing writing tone and heading structure.
  - Ask before introducing new dependencies or build tooling.

  ## Repository Layout
  - `docs/` contains published docs pages.
  - `README.md` is for project setup notes.
  - `assets/` contains images and static files.

  ## Commands
  - Install deps: `npm install`
  - Run locally: `npm run docs:dev`
  - Build docs: `npm run docs:build`

  ## Editing Guidelines
  - Keep Markdown clean and Docsify-friendly.
  - Use relative links for internal docs.
  - Prefer short sections and concrete examples.
  - When adding command examples, verify they match the current repo.

  ## Validation
  - After doc changes, run: `npm run docs:build`
  - If a command fails, report the failure clearly in the final summary.

  ## Pull Request / Final Response Notes
  - Summarize changed files briefly.
  - Mention any commands run and whether they passed.
  - Call out assumptions if something could not be verified.
```  

The typical sections in `AGENTS.md` are usually:  
* project overview  
* repo layout  
* commands to run  
* coding/writing conventions  
* validation steps  
* any special do/don\u2019t rules  


**<font size="4">Multiple `AGENTS.md` files</font>**  

There are actually two potential `AGENTS.md` files that Codex can consider. The primary one will be in the [project root](agentic/codex_cli/codex_cli_basics?id=directory-is-critical). For example, lets say our project root / directory is `/projects/learning/playground`; the `AGENTS.md` files, from least impactful to most impactful, are:  

3\. `~/.codex/AGENTS.md` (this is the _global_ `AGENTS.md`)  
2\. `/projects/learning/playground/AGENTS.md`  
1\. Prompt instructions you give Codex CLI (this takes highest precedence)  

Any prompt will override `/projects/learning/playground/AGENTS.md`, which, in turn, overrides `~/.codex/AGENTS.md`.  

---  

## Common Commands

| Command | Description |
|---|---|
| `codex` | Start interactive session |
| `codex resume` | You can pick which instance to resume.  |
| `model` | Choose what model and reasoning effort to use. |
| `/new` | Start a new chat during a conversation (wipes context). |

# Permission Modes
