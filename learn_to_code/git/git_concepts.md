# Git Concepts

# Branch

## Branch Example

The entire point of Git is version control. You can think of it as you are playing a game and you come to a point where you can choose one of two options (<font color="orange">Y</font> or <font color="orange">Z</font>), and you would like to try them both. You save the game at point <font color="purple">A</font> (before the choice is made), and then proceed to pick the path of <font color="orange">Y</font>. You play for a few hours and like where that took you, so you save your game (save <font color="purple">B</font>). You now re-load save point <font color="purple">A</font> and take option <font color="orange">Z</font>; you play for a few hours and decide you like the results of that option too, so you save the game and it becomes save point <font color="purple">C</font>.

You now have 3 save points: 
* <font color="purple">A</font>, which is just before the choice between paths <font color="orange">Y</font> and <font color="orange">Z</font>.
* <font color="purple">B</font>, which follows the events after you chose path <font color="orange">Y</font>.
* <font color="purple">C</font>, which follows the events after you chose path <font color="orange">Z</font>.

With these three save points, you have the option of re-starting the game at either of these three points at any given time.

The above encapsulates the idea of <font color="green">branching</font>: each point <font color="purple">A</font>, <font color="purple">B</font>, and <font color="purple">C</font> represent a different <font color="green">branch</font> of the overall save game structure; <font color="purple">A</font> can be considered the <font color="orange">master</font> <font color="green">branch</font> as both choices <font color="purple">B</font> and <font color="purple">C</font> diverge at <font color="purple">A</font>.

## Branch Definition

Officially, a <font color="green">branch</font> is a _copy_ of the code [state](learn_to_code/git/git_concepts?id=state) that exists in parallel alongside both its parent <font color="green">branch</font> and potentially its sibling <font color="green">branches</font>.

> It should also be noted that each branch has a [branch tip](learn_to_code/git/git_concepts?id=tip-or-branch-tip), which represents the most recent [commit](learn_to_code/git/git_concepts?id=commit) in that branch line.

**<font size="4">Branch Naming Conventions</font>**

Branches have the following naming restrictions:
* Absolutely forbidden:
 * Whitespace (including a space).
 * `: ? ~ ^ * [ ]`
 * Double dots `..` (single dots are fine, unless its in the combination of `/.`)
* Not forbidden, but be careful
 * `/` can be used, but you cannot use it at the end of the name
 * Dots (`.`) can be used, but _not_ `..` and _not_ `/.`

## Master Branch

The <font color="green">master branch</font> - commonly referred to as <font color="green">master</font> - is the 'trunk' of the tree: all other branches _should_ be linked back to the <font color="green">master</font> (although sometimes, through errors, there can exist branches that are no longer officially tied to anything). When the repository is initially started, there is only _one_ branch: the <font color="green">master</font>. 

**<font size="4">Master vs Origin/Master</font>**

The <font color="green">master</font> branch and the <font color="green">origin/master</font> branch are two different things:  
* <font color="green">master</font> represents the _local_ master.
* <font color="green">origin/master</font> represents the _remote_ master.

Sometimes, your local copy of <font color="green">master</font> can be behind by a few (or multiple) commits in comparison to the <font color="green">origin/master</font>; if this happens, your <font color="green">master</font> [branch tip](learn_to_code/git/git_concepts?id=tip-or-branch-tip) can be considered a [detached head](learn_to_code/git/git_concepts?id=detached-head). To rectify this, simply run a [pull](learn_to_code/git/git_concepts?id=pull), which will 'fast forward' your <font color="green">master</font> branch to that of <font color="green">origin/master</font>.

## Active Branch

The <font color="green">active branch</font> (sometimes referred to as the <font color="green">current branch</font>) is the only branch that is 'active' at any given time, There can only exist one <font color="green">active branch</font>; initially, the [master branch](learn_to_code/git/git_concepts?id=master-branch) is the <font color="green">active branch</font>, and it remains that way until you manually change it.

> You can change the <font color="green">active branch</font> with the [checkout](learn_to_code/git/git_concepts?id=checkout) command; in reality, issuing a [checkout](learn_to_code/git/git_concepts?id=checkout) on the development branch (in combination of starting (and naming) a new branch) is the preferred method for fixing bugs and making improvements.

## Remote vs Local Branches

In systems that utilize a [remote repository](learn_to_code/git/git_concepts?id=remote-repository), there are local branches and remote branches. Local branches are available to you to alter / modify. Remote branches are  branches on the remote repository; these usually in the format '[remote name](learn_to_code/git/git_concepts?id=remote-name)/branch_name' (and usually that is 'origin/branchname'), so if you see a branch name in this format it means its a remote branch.

It should be noted that _you cannot perform changes on a remote branch directly_; in order to do so, you must create / [checkout](learn_to_code/git/git_concepts?id=checkout) a _local_ branch to alter / modify and then [commit](learn_to_code/git/git_concepts?id=commit) and [push](learn_to_code/git/git_concepts?id=push) those changes. 

> This is why you may see a <font color="orange">master</font> and a <font color="orange">origin/master</font> branch: <font color="orange">master</font> is the _local_ version of <font color="orange">origin/master</font>, with the idea being you make changes to <font color="orange">master</font> and then push those changes to <font color="orange">origin/master</font>.

# Checkout

> For more information [see this article on Stack Overflow](https://stackoverflow.com/questions/15296473/what-do-git-checkouts-really-mean).

Using the [branch example](learn_to_code/git/git_concepts?id=branch-example) given earlier, you can think of a Git <font color="green">Checkout</font> as your save points (<font color="purple">A</font>, <font color="purple">B</font>, or <font color="purple">C</font>) that you can return to later (if you re-load the save point): a saved game allows you to re-load the game to a previous point, much like performing a <font color="green">checkout</font> on a [branch](learn_to_code/git/git_concepts?id=branch) or a specific [commit](learn_to_code/git/git_concepts?id=commit). Officially, a Git <font color="green">Checkout</font> is returning to a [branch](learn_to_code/git/git_concepts?id=branch) or a specific previous [commit](learn_to_code/git/git_concepts?id=commit) that you have made; much like jumping around a game save, a <font color="green">checkout</font> allows you to return to any [previous state](learn_to_code/git/git_concepts?id=state) that was saved.

A practical example would be: you are asked to make some changes to the code. You <font color="green">checkout</font> the current development [branch](learn_to_code/git/git_concepts?id=branch) of your repository, create a new [branch](learn_to_code/git/git_concepts?id=branch) off that (giving it a name), and start making your changes in code. If the code is suggessful and awesome, you [merge](learn_to_code/git/git_concepts?id=merge) your new branch in with the branch you branched it from; if the code stinks, you can just delete this entire new branch and forget it never happened (as in, you can <font color="green">checkout</font> the current development [branch](learn_to_code/git/git_concepts?id=branch) again, and it's like your changes never happened).

Practically speaking, you can issue a checkout based on the [branch name](learn_to_code/git/git_concepts?id=branch), the [hash](operating_systems/ubuntu/linux_notes?id=file-hashes) of the commit (as a hash is given for _every_ commit made), the tag name, or use relative syntax (HEAD^, HEAD~1). Using anything but the [branch name](learn_to_code/git/git_concepts?id=branch) can come at a price, though, as **using anything but the branch name will result in a [detached HEAD](learn_to_code/git/git_concepts?id=detached-head)**.

# Clone

> This concept is exclusively for distributed project in Git (i.e. projects hosted on GitHub, Bitbucket, etc).

To <font color="green">clone</font> a project means to download _all_ of a project's [repository](learn_to_code/git/git_concepts?id=repository): code, revision history, etc. A clone is an exact replica of a project.

Once the remote Git repository is cloned, the entire repository is located on your local machine.

!> Usually its best to create the repository on the foreign system (i.e. GitHub or Bitbucket) _first_ and then clone the project to your local machine (even though there is no code in it yet). This is an excellent way to initialize a Git project that is intended to be distributed and/or will be maintained by multiple people.

# Fetch

> This concept is exclusively for distributed project in Git (i.e. projects hosted on GitHub, Bitbucket, etc).

To <font color="green">fetch</font> changes of a remote Git [repository](learn_to_code/git/git_concepts?id=repository) means to identify the new changes between the repository on the server and your local repository.  

Note that <font color="green">fetch</font> does not _make_ the changes, it simply identifies them. To make the changes you must use [merge](learn_to_code/git/git_concepts?id=merge) after <font color="green">fetch</font>.

> There is a shorthand command for <font color="green">fetch</font>/[merge](learn_to_code/git/git_concepts?id=merge): [pull](learn_to_code/git/git_concepts?id=pull). Usually, <font color="purple">pull</font> is used instead of <font color="green">fetch</font> and then <font color="purple">merge</font>.

# Merge

To <font color="green">merge</font> changes of a Git [repository](learn_to_code/git/git_concepts?id=repository) means to combine two different [branches](learn_to_code/git/git_concepts?id=branch) that share a common parent branch (and, in many cases, one of the branches _is_ the parent branch).

<font color="green">Merge</font> is also used to rectify differences in your local branches with the remote branches (if there have been changes); in these cases, a [fetch](learn_to_code/git/git_concepts?id=fetch) command is issued and it is immediately followed by a <font color="green">merge</font>. Usually - when it comes to rectifying local and remote branches - [fetch](learn_to_code/git/git_concepts?id=fetch)/<font color="green">merge</font> is not used; instead, the [pull](learn_to_code/git/git_concepts?id=pull) command is used which combines the two.


# Origin

See [remote name](learn_to_code/git/git_concepts?id=remote-name).

# Pull

> This concept is exclusively for distributed project in Git (i.e. projects hosted on GitHub, Bitbucket, etc).

To <font color="green">pull</font> from a remote Git [repository](learn_to_code/git/git_concepts?id=repository) means to identify the differences in the local repository vs the remote repository (i.e. [fetch](learn_to_code/git/git_concepts?id=fetch)) and then combines the local and remote repositories and stores the result in your local repository (i.e. [merge](learn_to_code/git/git_concepts?id=merge)). Note that it is possible that you do not have a local [branch](learn_to_code/git/git_concepts?id=branch) that is tracked for any given repository (meaning you have never worked on it), and since <font color="green">pull</font> only reconciles local branche, pull will have no effect on these branches which do not have a local copy.

While you can run a <font color="purple">fetch</font> and then a <font color="purple">merge</font>, running a <font color="green">pull</font> is more efficient.

# Pull Request

A <font color="green">pull request</font> is a bit of a misnomer - it is _not_ a pull, but rather a polite request to [merge two different branches](learn_to_code/git/git_concepts?id=merge); this concept is only available on [remote repositories](learn_to_code/git/git_concepts?id=remote-repository).

Practically speaking, this is only used when multiple people are managing code (and the remote repository is on a website, like Bitbucket or GitHub). Usually, someone has a [branch](learn_to_code/git/git_concepts?id=branch) that needs to be [merged](learn_to_code/git/git_concepts?id=merge) to its parent but it is not done; someone else who needs this code in the master branch (or main branch) comes along and creates this request; if the request is granted, the two branches are merged. In essence, this is _only_ needed for multiple people working on code; if its just one person, a simple <font color="purple">merge</font> would suffice.

<font color="green">Pull request</font> are not technically a part of Git (there is no <font color="green">pull request</font> command) and are only relevant on the remote repository website.


# Push

> This concept is exclusively for distributed project in Git (i.e. projects hosted on GitHub, Bitbucket, etc).

<font color="green">Push</font> means "send our changes from the local [repository](learn_to_code/git/git_concepts?id=repository) to the remote repository". <font color="green">Push</font> sends only changes that were captured with a [commit](learn_to_code/git/git_concepts?id=commit).

# Remote

> This concept is exclusively for distributed project in Git (i.e. projects hosted on GitHub, Bitbucket, etc).

The <font color="green">remote</font> command is used to manage everything associated with the local repository and the remote repository. The remote connections are established with the <font color="green">remote</font> command

# Commit

A <font color="green">commit</font> officially stores all of your changes made to your local [repository](learn_to_code/git/git_concepts?id=repository), officially registering the changes for (at least local) version control. Note `commit` does _not_ push data to the remote repository, its simply used to commit any and all changes to your _local_ repository.

It should be noted that every commit will give a [SHA1 ID](learn_to_code/git/git_concepts?id=sha1-id) which uniquely identifies the commit.

> See [this article on Git-Scm](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History) for more on viewing the commit history.  

!> Commits can _only_ happen on a branch in the [local repository](learn_to_code/git/git_concepts?id=local-repository), _never_ on a branch in a [remote repository](learn_to_code/git/git_concepts?id=remote-repository)!  

# Rebase  

A <font color="green">rebase</font> is used when you wish to 're-merge' back to the original branch that you branched your current branch from (usually the `master` branch, but this can be different). You could just merge the base branch into your current branch, but it can lead to problems. Instead, use <font color="green">rebase</font>, which rewrites commits from the original branch to your current branch to make your current branch in sync.  

To do (using master as an example): 
```
git rebase master
```
* This is run while you are in the current branch you are working on (i.e. not master)  

---

# HEAD

The <font color="green">HEAD</font> refers to your current location in the [branch](learn_to_code/git/git_concepts?id=branch). `HEAD` can be relative, so when performing a [checkout](learn_to_code/git/git_concepts?id=checkout) you can specify:
* `HEAD`
* `HEAD-X` means X commits previously from the current `HEAD`.
* `HEAD+X` means X commits in the future from the current `HEAD` (assuming they actually exist).

`HEAD` can be a bit tricky. It officially means the current [commit](learn_to_code/git/git_concepts?id=commit) in the [branch](learn_to_code/git/git_concepts?id=branch) you are working on (which you can change with a [checkout](learn_to_code/git/git_concepts?id=checkout)), as stated above. That said, if you [checkout](learn_to_code/git/git_concepts?id=checkout) a branch name (and not a specific commit ID / [SHA1 hash ID](learn_to_code/git/git_concepts?id=sha1-id)) your new `HEAD` will point to the _branch_ `HEAD` of that branch (which is usually the latest commit, but there are no guarantees). 

!> If you choose to checkout with the commit ID / [SHA1 hash ID](learn_to_code/git/git_concepts?id=sha1-id), you will be working off a [detached HEAD](learn_to_code/git/git_concepts?id=detached-head); there are ways you can checkout a specific commit and not be working from a detached HEAD (which is valuable for bug fixing, and you can read about that [here](learn_to_code/git/git_concepts?id=detached-head)), but generally speaking, you will want to [checkout](learn_to_code/git/git_concepts?id=checkout) using the branch name and _not_ the commit ID.

## Detached HEAD

A <font color="green">Detached HEAD</font> is a [HEAD](learn_to_code/git/git_concepts?id=head) that was checked out using a [SHA1 hash ID](learn_to_code/git/git_concepts?id=sha1-id) and _not_ the [branch](learn_to_code/git/git_concepts?id=branch). When you [checkout](learn_to_code/git/git_concepts?id=checkout) a branch, Git moves the `HEAD` to the newest commit on the branch; when you use the SHA1 hash to perform the checkout, though, Git does not do this. The conesquence of this is if you make any changes to the project, the changes do not belong to any branch. You will also be annoyed by messages.

If you absolutely need to checkout by SHA1 ID (say, to investigate a bug), you should create a temporary branch (using the SHA1 ID), perform your tests, then delete the temp branch. This can be done by:
1\. `git checkout -b mytest <SHA1 ID>`
 * Replace `<SHA1 ID>` with your actual ID and do not use the brackets.
 * This checks out the commit ID _as well as_ stores this information in a new branch. This is critical.
2\. Perform whatever test you want to perform.
3\. `git checkout master`
 * This changes your branch to the master. You can change it to whatever, really - we just need to get off the test branch (in our case 'mytest').
4\. `git branch -d mytest`
 * This deletes the temp branch

---

# Remote Name

Each [remote repository](learn_to_code/git/git_concepts?id=remote-repository) is identified by a name (which can be viewed with the `gite remote` command); under most circumstances, there is only one remote repository, and its default name is `origin`.

---

# Repository

The <font color="green">repository</font> is simply the base directory that will house your Git project; it can also reference the _concept_ of your project as well.

As stated previously, there is a <font color="purple">local repository</font> (which resides on your machine) and a <font color="purple">remote repository</font> (which resides on some remote server). In theory, **ALL changes** are kept both locally and remotely, so you can revert to a previous version of any file and you can do so locally. That said, most people make a local `commit` (as `commit` is local) and do not always issue commands to send updates to the remote server (i.e. a `push`) until after a few `commit`s are made.

## Local Repository

The <font color="green">local repository</font> resides on your local machine. The <font color="green">local repository</font> is located in a directory that houses a `.git` folder, which is mostly used to track changes to the files in your repository. The folder that houses the `.git` folder for this project is sometimes referred to as the <font color="green">local repository</font>. The <font color="green">local repository</font> can change locations on your filesystem, so long as you keep the `.git` folder intact.

## Remote Repository

The <font color="green">remote repository</font> resides elsewhere; in many cases its a hosting site (like  [GitHub](https://github.com/) or [Bitbucket](https://bitbucket.org)) that can act as a server for your <font color="green">remote repository</font>; other times, you can set up a server on your local network to act as a <font color="green">remote repository</font>.

In most cases, there is only one <font color="green">remote repository</font> - and its given name is usually `origin`. That said, its possible to have _multiple_ <font color="green">remote repositories</font>.

---

# Reset 

> More on this [here](https://davidzych.com/difference-between-git-reset-soft-mixed-and-hard/).

A <font color="green">reset</font> means to restore the current working branch to a previous version (i.e. a previous [commit](learn_to_code/git/git_concepts?id=commit); this is done by `git reset  <SHA1 ID>`. There are three types (all of which can be specified i.e. `--hard`, `--soft`, `--mixed`):
* **Hard** - A <font color="purple">hard</font> reset indicates that all commits _after_ the commit you pick will be **_permanently_** deleted.
 * Use this to completely blow out changes forever.
* **Soft** - A <font color="purple">soft</font> reset simply moves the branch's [HEAD](learn_to_code/git/git_concepts?id=head) to the selected [commit ID](learn_to_code/git/git_concepts?id=sha1-id). All changes between the original `HEAD` and the new `HEAD` appear to be [staged](learn_to_code/git/git_concepts?id=staged-file).
* **Mixed** - A <font color="purple">mixed</font> reset  moves the branch's [HEAD](learn_to_code/git/git_concepts?id=head) to the selected [commit ID](learn_to_code/git/git_concepts?id=sha1-id). All changes between the original `HEAD` and the new `HEAD` appear to be modified, but _not_ [staged](learn_to_code/git/git_concepts?id=staged-file).
 * Mixed is the default.

---

# SHA1 ID

Every commit will give a <font color="green">SHA1 ID</font> (also known as a <font color="green">commit ID</font>), which is a SHA-1 [hash](operating_systems/ubuntu/linux_notes?id=file-hashes) that uniquely identifies the commit. These IDs are critical when [checking out](learn_to_code/git/git_concepts?id=checkout) various [commits](learn_to_code/git/git_concepts?id=commit), so be mindful of their existence.

> For Git, the SHA1 hash is _not_ of the files themselves but of the changes made in the commit.

# Staged File

A <font color="green">staged file</font> is one that is marked to be committed (but not actually committed yet). 

> It seems that <font color="green">staged</font> and <font color="green">unstaged</font> my not be fully in opposition, as it _seems_ a file can be unstaged (not committed) and also staged (marked to be committed), but this is unclear to me.

---

# State

<font color="green">State</font> represents the relation of items in the project. For example, a specific configuration of files with specific data is considered a state; changing the data in any of these files (and then saving the data) means the state of the project is now changed to a new state - one that reflects the additional (or in some cases, even removed) data.

A short (but not exhaustive) list of how state can change:
* Adding one (or more) files to the [repository](learn_to_code/git/git_concepts?id=repository).
* Deleting one (or more) files from the [repository](learn_to_code/git/git_concepts?id=repository).
* Adding data to one (or more) files.
* Deleting data from one (or more) files.

Typically, the state is not considered 'changed' until a commit or push is issued.

---

# Tags

A Git <font color="green">tag</font> simply points to a specific [commit ID](learn_to_code/git/git_concepts?id=sha1-id) but its advantage is its named (so you can remember it later). Most people use tags to denote specific points in the code (and they usually revolve around version numbers). 

---

# Tip (or Branch Tip)

A <font color="green">tip</font> or <font color="green">branch tip</font> references the most recent commit on a [branch](learn_to_code/git/git_concepts?id=branch).

It is also possible to have multiple tips per project; specifically if there are multiple [branches](learn_to_code/git/git_concepts?id=branch). For example, as taken from the [Pro Git Book](https://git-scm.com/book/en/v2):

![Git-GUI_TipExample.png](images/Git-GUI_TipExample.png)

There are actually 3 [branches](learn_to_code/git/git_concepts?id=branch) here, each with a tip: `master` (commit 2), `hotfix` (commit 4), and `iss53` (commit 3).

---

# Tracked Files

<font color="green">Tracked Files</font> are files that Git is monitoring for changes. Its possible to have _untracked_ files in the [repository](learn_to_code/git/git_concepts?id=repository) - usually these are files that were just added and we intend on tracking them later, but there are also cases where we wish for certain files to _never_ be tracked (for these we use the file [.gitignore](learn_to_code/git/git?id=ignoring-files)).

---

# Unstaged Changes

<font color="green">Unstaged Changes</font> are changes that have happened since the last known [state](learn_to_code/git/git_concepts?id=state) of the [repository](learn_to_code/git/git_concepts?id=repository).

---