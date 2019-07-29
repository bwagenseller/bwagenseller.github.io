# Git

# Basics of Git

>For more info on Git, [please go to Git-Scm](http://git-scm.com/), [a major online book on Git-Scm](https://git-scm.com/book/en/v2/), or [Github](https://github.com/).

## Why Git?

Git is a <font color="green">version control</font> which can save changes to code. What makes Git powerful is:
* You can revert to previous versions of code at any time.
* Multiple people can work on the code at once.

The above equates to Git being a <font color="green">multi-directional free flow context</font>.

## Distributed Version Control System

Git is a <font color="green">Distributed Version Control System</font>, which means that there is a copy of _all_ code on a remote server _as well as_ locally on your machine. 

## Installing Git (Command Line)

To install Git, [follow these instructions]

## Snapshots

You can revert to any version of any file in Git; this is possible because Git takes a <font color="green">snapshot</font> of the entire set of files instead of storing the changes from one version to the next (although note that if a file remains unchanged, a simple _reference_ is kept to the previous file). This means Git can restore previous versions quickly (instead of having to build following a change tree).

---

# Git GUI

You can use a GUI for Git in Ubuntu. I will try to mention both the <font color="green">CLI</font> and <font color="green">GUI</font> instructions for commands.

## Git Installation

**<font size="4">Git on Ubuntu</font>**

I covered how to install Git for Ubuntu (the command line interface (<font color="purple">CLI</font>)) for Git [here](ubuntu/server_build?id=installing-git).

**<font size="4">GUI on Ubuntu</font>**

To install the Git GUI in Ubuntu (must be done as root):
```
apt-get install git-gui
```

!> Do NOT use `git-gui` to launch the GUI - it will not work. Instead, use `gitk`. Also note it MUST be launched from the local [repository](learn_to_code/git/git_concepts?id=repository) directory of the project.

## Launching Git GUI

**<font size="4">Ubuntu</font>**

> You must first either clone or [initialize](learn_to_code/git/git?id=initialization-for-each-project) the project! If you do not do either one of these Running `gitk` will return nonsense.

To launch the Git GUI in Ubuntu, navigate to the [repository](learn_to_code/git/git_concepts?id=repository) and type `gitk`.

!> If you forget to navigate to the repository, the Git GUI will freak out. **Do not** forget to navigate to the repository _first_, then launch `gitk`!

Here is the initial screen:

![Git-GUI_SplashScreen.png](images/Git-GUI_SplashScreen.png)

I am not sure why, but the 'main' screen for the selected repository does not open. To do so, click `File`->`Start git gui`.



# Initializing

## One-Time Initialization

Its best to set up Git locally and globally, so Git can identify and group changes made to files in any repository.

```
git config --global user.name "user_name"
git config --local user.name "user_name"
git config --global user.email "email_id"
git config --local user.email "email_id"
git init
```
* replace `"user_name"` with your user name
* replace `"email_id"` with your email


## Setting Password

> I first found these instructions [here](https://stackoverflow.com/questions/11403407/git-asks-for-username-every-time-i-push).

From what I understand, you need to run `git config credential.helper store` and then do a push; you will be asked for a username/password, but from then on there will be no need to enter a username/password.

Your credentials will be stored in the file `.git-credentials` in your home directory, but beware - its unencrypted (although the permissions are set to `700`).

## Initialization for Each Project

Git needs to be initialized for every project - more specifically, a `.git` directory needs to be created in the working directory. This `.git` folder houses all the history about file changes made to your project.

**<font size="4">Initialization on the Command Line</font>**

To initialize on the command line, go into the directory you with to set as the [repository](learn_to_code/git/git_concepts?id=repository) and type:
```
git init
```

## Cloning a Project

Its possible to <font color="green">clone</font> a project, which simply means go to the remote server and download the _existing_ code - along with all of its versions and history - to your local machine (please note this also [initializes](learn_to_code/git/git?id=initialization-for-each-project) the project, so there is no need to further initialize it before or after you clone it). To do so, go to the directory you wish to house the [repository](learn_to_code/git/git_concepts?id=repository); from there, the general command is:
```
git clone https://some.website/your_git_project.git
```

!> Keep in mind that `git clone` creates the repository, so there is no need to do so yourself (unlike [initializing a new Git project](learn_to_code/git/git?id=initialization-for-each-project)). That said, make sure you run `git clone` in the directory you wish to house the repsoitory, as it will be created here.

You will have to get the link `https://some.website/your_git_project.git` from your server.  There are a few ways this is done, depending on the host you are using:
* If using <font color="purple">Bitbucket</font>, go to the project's main page - there will be a `Clone` button in the upper right corner of the page. Click this, and you will be able to copy the _entire_ git clone line (address and all). 

---

# Adding Files to a Project

!> Since adding files are project-specific, you _must_ be in the directory structure somewhere under your [repository](learn_to_code/git/git_concepts?id=repository).

You will have to routinely add files and folders to your git project. 

## Manually Adding Files

Simply place the files in your [repository](learn_to_code/git/git_concepts?id=repository) and then add them like so:
```
git add myFile.txt
```
Here, we have added `myFile.txt` to our repository and are now [tracking](learn_to_code/git/git_concepts?id=tracked-files) the file.

We can also just add _all_ new files under the repository - to do so, use the commmand:
```
git add --all
```

Alternatively, you can add all files with:
```
git add .
```

And you can even add using a wildcard like so:
```
git add *.txt
```

The files are now [staged](learn_to_code/git/git_concepts?id=staged-file).

## Manually Removing (Non-Committed) Files

If you add a file with `git add` _before_ you commit it (i.e. the file is [staged](learn_to_code/git/git_concepts?id=staged-file), you can remove the file like so:
```
git reset myFile.txt
```

The file will _not_ be a part of the next commit if it is removed in such a fashion.

## Adding Files with the Git GUI

To add files with the Git GUI, we must first click the `Rescan` button (see below) to find all new files; then, click on the page icon (next to the file name) to move the file from the [unstaged changes](learn_to_code/git/git_concepts?id=unstaged-changes) pane to the `Staged Changes` pane:

![Git-GUI_AddUnstaged.png](images/Git-GUI_AddUnstaged.png)

The files are now [staged](learn_to_code/git/git_concepts?id=staged-file).

> If you wish to add _all_ unstaged files, simply press `Ctrl`+`I`.

## Removing (Non-Committed) Files with the GUI

To remove a [staged file](learn_to_code/git/git_concepts?id=staged-file), simply click on the 'paper' icon next to the file in the `Staged Changes (Will Commit)` area.

The file will _not_ be a part of the next commit if it is removed in such a fashion.

---

# Updating Files

After you make changes to your file you will want to [commit](learn_to_code/git/git_concepts?id=committing-changes) them to the [local repository](learn_to_code/git/git_concepts?id=repository). Updating files are very similar to [adding them](learn_to_code/git/git?id=adding-files-to-a-project); the GUI instructions are exactly the same - even the `add` of the files. This may seem counterintuitive ("I already added the files, why am I adding again?") but it is what it is. 

Note that you only have to re-add the files that changed, not all files. The GUI will take care of this for you if you hit `rescan`, but if you are using the command line, you will want to run a [git status](learn_to_code/git/git?id=status) which will identify all changed files. Re-add these (or just re-add all), and then you will want to run a [commit](learn_to_code/git/git_concepts?id=committing-changes).

---

# Ignoring Files

Sometimes we do not want Git to track specific files (such as temp files). In order to force Git to ignore files we must use the file `.gitignore`. Create the file `.gitignore` and store it in your [repository directory](learn_to_code/git/git_concepts?id=repository).

Once this file is in place, type file names in this file to exclude them in Git. You can use wildcards too. For example, if your file contained these two lines:
```
blink.txt
~*.*
```
* _Any_ file named `blink.txt` would be ignored - no matter where it was in the directory structure (even if it is in a subdirectory under the [repository](learn_to_code/git/git_concepts?id=repository)).
* _Any_ file that matched the <font color="purple">pattern</font> `~*.*` would be ignored - no matter where it was in the directory structure (even if it is in a subdirectory under the [repository](learn_to_code/git/git_concepts?id=repository)). This specific pattern can be useful, as many temp files (such as Windows backup files) are created with a `~` in the beginning of the file.

As you can see, pattern matching is used, so **any** files that match that pattern are ignored, across all subdirectories. Its important to note, however, that _if the line in .gitignore contains a / pattern matching will **not** span subdirectories_. 

Say, for example, you had a subdirectory `{repository}/newDir` under your repository that contained a file called `blink.txt` that you do _not_ want to ignore, but you **do** want to ignore `blink.txt` in your main repository directory as well as in the subdirectory `{repository}/newDir/someOtherDir`. Your `.gitignore` could look something like this:
```
~*.*
/blink.txt
/newDir/someOtherDir/blink.txt
```
* Its also important to note that the repository directory is assumed to be the root - so `/newDir/someOtherDir/blink.txt` is _actually_ `{repository}/newDir/someOtherDir/blink.txt`

Since we use the `/`, the file `blink.txt` in the root repository directory will be ignored, the `blink.txt` file in `{repository}/newDir/someOtherDir/` will be ignored, but the file `{repository}/newDir/blink.txt` will _not_ be ignored.

> Note you _can_ have a `.gitignore` file in each subdirectory, if you wanted to - but usually only one `.gitignore` file is used.

---

# Committing Changes

Eventually, you will have to [commit your changes to the local repository](learn_to_code/git/git_concepts?id=commit), which basically means add your changes to the _local_ (as opposed to remote) version control system.

To do so manually, type something like the following:
```
git commit –m "some comment"
```
* The `-m` flag is to specify a comment, which you should _always_ do.

To do so in the GUI, simply put your message in the `Commit Message` section and then press the `Commit` button:

![Git-GUI_CommitExample.png](images/Git-GUI_CommitExample.png)

> After the commit you will be given a commit identifier (commit ID).

---

# Non-Project Related Commands

## List Config

To list all config variables, type the following:
```
git config -l
```

---

# Quick Commands to Update

```
git add --all
git commit -m "Initial Commit"
git push -u origin master
```

---

# Status

Sometimes you have to see the [unstaged changes](learn_to_code/git/git_concepts?id=unstaged-changes) that were made to the repository since the [previous state](learn_to_code/git/git_concepts?id=state). To see these changes, run:
```
git status
```

As an example, here are the results of this command for my current project:
```
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	java_basics.md

nothing added to commit but untracked files present (use "git add" to track)
```

We can gather that:
* We are on the master branch
* There are [untracked files](learn_to_code/git/git_concepts?id=tracked-files).

---

# Checking out

```
git checkout --track origin/develop
```

---
