# Git CLI Commands

# Branch

## Displaying All Branches

!> This does **not** query the remote repository before its issued, nor does it prune deleted branches from the remote repository; its best to perform a [pull](learn_to_code/git/git_cli_commands?id=simple-pull) first and then [prune deleted remote branches](learn_to_code/git/git_cli_commands?id=clean-up-remote-branches) before issuing this command.

To display all [branches](learn_to_code/git/git_concepts?id=branch) - both <font color="purple">local</font> _and_ <font color="purple">remote</font> - type:
```
git branch -v --all
```

This will show all branches. Example output:
```
  add_XML                1b49acd Added XML file
* master                 aade838 Merged in iss53 (pull request #1)
  remotes/origin/add_XML 1b49acd Added XML file
  remotes/origin/master  aade838 Merged in iss53 (pull request #1)
```

What is useful about this is it show the commit IDs of each local and remote repository. From this we can see that the <font color="purple">local repository</font> `add_XML` and the <font color="purple">remote repository</font> `origin/add_XML` are the same, as their hash keys (`1b49acd`) are the same - so we know we are up-to-date.

## Displaying All Local Branches

To display all local [branches](learn_to_code/git/git_concepts?id=branch), type:
```
git branch
```
* The [active branch](learn_to_code/git/git_concepts?id=active-branch) will have an asterisk (`*`) next to it.

> If you perform a `git branch` and you see a message about a [detached head](learn_to_code/git/git_concepts?id=detached-head), try issuing a [git pull](learn_to_code/git/git_cli_commands?id=pull) to re-sync your repository.

## Displaying All Remote Branches

!> This does **not** query the remote repository before its issued, nor does it prune deleted branches from the remote repository; its best to perform a [pull](learn_to_code/git/git_cli_commands?id=simple-pull) first and then [prune deleted remote branches](learn_to_code/git/git_cli_commands?id=clean-up-remote-branches) before issuing this command.

To display all remote [branches](learn_to_code/git/git_concepts?id=branch), type:
```
git branch -r
```

This will show all remote branches.

> Sometimes the list will be different because someone else deleted a remote branch - if this is the case, you will have to [prune your remote branches](learn_to_code/git/git_cli_commands?id=clean-up-remote-branches).  

!> You cannot alter remote branches directly; to do so, you have to [checkout](learn_to_code/git/git_cli_commands?id=checkout) the branch locally (by [creating a new local branch of the remote branch](learn_to_code/git/git_cli_commands?id=create-new-branch-and-checkout)) and alter that.

## Creating New Branch

In order to create a new [branch](learn_to_code/git/git_concepts?id=branch) from the current [active branch](learn_to_code/git/git_concepts?id=active-branch), the command is:  
```
git branch new_branch_name
```
* `new_branch_name` is the new name of the branch.

Note that this does not switch you to the branch - in order for that to happen, you must use [checkout](learn_to_code/git/git_cli_commands?id=checkout) on `new_branch_name`.

## Switching to Another Branch

In order to switch branches, the `branch` command is _not_ used, at least directly. First, use `git branch` to list all branches, then use [checkout](learn_to_code/git/git_cli_commands?id=checkout) to switch to that branch (using the branch name).

## Deleting a Branch

To delete a _local branch_, use:
```
git branch -d branch_name
```
* `branch_name` is the local branch name.
* This only works if the branch has been merged in the upstream (remote) branch.
 * You can also use `-D`, which will _force_ the delete.
 * It may be best to delete the remote branch _first_.

To delete a _remote branch_, use:
```
git push --delete remote_name branch_name
```
* `remote_name` is the [remote name](learn_to_code/git/git_concepts?id=remote-name), which is usually `origin`.
* `branch_name` is the remote branch name.
* May also be: `git push remote_name --delete branch_name`

Note that for old versions of Git, the way to delete a remote branch is:
```
git push remote_name :branch_name
```

## Adding Branch to Remote Server

When you create a _local_ branch, the [branch](learn_to_code/git/git_concepts?id=branch) must _also_ be added remotely. To do this, run the command:
```
git push --set-upstream <remote_name> <added_local_branch_name>
```
* `<remote_name>` is the [remote name](learn_to_code/git/git_concepts?id=remote-name) (do not use `<` or `>`); usually, this name is `origin`.
* `<added_local_branch_name>` is the added local branch name (do not use `<` or `>`); as implied, the branch must exist locally _first_.

## Displaying Merged Branches

Sometimes you will have local branches that were merged remotely; to view these, type:
```
git branch --merged
```

You can then [delete the local branch](learn_to_code/git/git_cli_commands?id=deleting-a-branch) if you wish.

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

# Log

> Sometimes you can get stuck in the log and not even `ctrl`+`c` will help; in this case, just press `q`.

`git log` is used to see [commit](learn_to_code/git/git_concepts?id=commit) information such as:
* [SHA1 hash](learn_to_code/git/git_concepts?id=sha1-id) of the commit.
* The commit's author.
* The commit's datetime.
* The commit's memo / description.

When you run `git log` you will see all information about al of the commits up to your [HEAD](learn_to_code/git/git_concepts?id=head). For example:
```
bwagenseller@HomeComputer:~/Documents/Code/Java/brentsgit$ git log
commit 60a0412ef173b2a813ebd9701f6ba49a96901590 (origin/master, master)
Author: bwagenseller <brent.b.wagenseller@yahoo.com>
Date:   Thu Jul 25 22:32:31 2019 -0400

    Add anotherTest.md

commit 2724711aa94a11d7a55730f19ed6937915545526
Author: bwagenseller <brent.b.wagenseller@yahoo.com>
Date:   Wed Jul 24 12:09:36 2019 -0400

    Changed test message

commit d91fee11e257332d6c69b06a822d2fb9794ba9b6
Author: bwagenseller <brent.b.wagenseller@yahoo.com>
Date:   Wed Jul 24 11:27:31 2019 -0400

    add test.md

commit 83a963fd728d9093679a1578ff1c70087f1a9e63
Author: bwagenseller <brent.b.wagenseller@yahoo.com>
Date:   Wed Jul 24 11:18:48 2019 -0400

    Updated java_basics.md

commit 9487fcaa7f271aac0d20eafef042807157ed4c29 (HEAD)
Author: bwagenseller <brent.b.wagenseller@yahoo.com>
Date:   Wed Jul 24 00:11:23 2019 -0400

    First commit in the GUI!

```

## Showing All Commits

If you [checkout](learn_to_code/git/git_concepts?id=checkout) a previous [commit](learn_to_code/git/git_concepts?id=commit), you will notice that `git log` only shows up to the [HEAD](learn_to_code/git/git_concepts?id=head). If you wish to see all commits (including ones that came after the current `HEAD`), use `git log --all`.

## Minimizing Output

You can also pare down this info to just show the first 6 characaters of the [SHA1 hash](learn_to_code/git/git_concepts?id=sha1-id) of the [commit](learn_to_code/git/git_concepts?id=commit) and the commit comment using the `git log --oneline` command:
```
bwagenseller@HomeComputer:~/Documents/Code/Java/brentsgit$ git log --oneline
60a0412 (origin/master, master) Add anotherTest.md
2724711 Changed test message
d91fee1 add test.md
83a963f Updated java_basics.md
9487fca (HEAD) First commit in the GUI!
```

## Grepping in the Log

To grep in the log, use `--grep="sometext"`:
```
git log --grep="some text"
```
* Note that this only searches the messages, it does not help for searching foranything like hash ID, author, etc.

## Search Between Dates

To search between dates in the log, use `--since=` and `--until=`:
```
git log --since=2019-06-01 --until=2019-07-04
```
* You can also use things like `-since=5.days` or `-since=10.months`

---

# Checkout

To [checkout](learn_to_code/git/git_concepts?id=checkout) either a [commit](learn_to_code/git/git_concepts?id=commit) or [branch](learn_to_code/git/git_concepts?id=branch), use the `git checkout` command.

* To checkout by the [SHA1 hash](learn_to_code/git/git_concepts?id=sha1-id), use the command `git checkout <SHA1 ID>`
 * Replace `<SHA1 ID>` (even the brackets) with your actual ID; to find that, use [git log](learn_to_code/git/git_cli_commands?id=log).
* To checkout by [branch](learn_to_code/git/git_concepts?id=branch) name, just state the branch like so: `git checkout origin/master`
 * `origin/master` should be the starting branch in all Git projects, so it should exist (unless you changed it).
 * Another example would be: `git checkout --track origin/develop`
 
## Create New Branch and Checkout

To create a new [branch](learn_to_code/git/git_concepts?id=branch) (from your [active branch](learn_to_code/git/git_concepts?id=active-branch)) while also checking out that branch, you can use the `-b` switch:
```
git checkout -b new_branch_name
```
* `new_branch_name` is the new name of the branch that does not yet exist (and is to be created).

## Checkout Remote Branches

You cannot make changes on a remote branch - you can only do so on local branches. If you simply checkout a [remote branch](learn_to_code/git/git_concepts?id=remote-vs-local-branches) you will end up with a [detached head](learn_to_code/git/git_concepts?id=detached-head); to avoid this, we must checkout the remote branch _while also_ creating a local branch  from that remote branch. To do so:
```
git checkout origin/remote_branch -b local_branch
```
* The [remote name](learn_to_code/git/git_concepts?id=remote-name) should usually be `origin`, so only change that if its different.
* `remote_branch` is the name of the remote branch. 
* `local_branch` is the name of the branch you are creating that will mimic (and track) the given remote branch. 

At this point you can make changes in `local_branch` and eventually push them to `origin/remote_branch`.

---

# Reset

!> Please see [here](learn_to_code/git/git_concepts?id=reset) to understand how resets work. Its important to understand before use!  <br>In addition, make sure to notify all of your teammates so they don't overwrite your reset.

To perform a branch [reset](learn_to_code/git/git_concepts?id=reset), the command is:
```
git reset --<option> <SHA1 ID>`
```
 * Replace `<SHA1 ID>` with your actual ID and do not use the brackets.
 * Replace `--<option>` with either `--hard`, `--soft`, or `--mixed`.

After the reset, you must force a push with:
```
git push -f
```
* If the push is not forced with `-f` your push will simply be rejected, as the remote repository is ahead of your own (as would be expected if you reverted).

---

# Remote

The <font color="green">remote</font> command is used to interact with the remote server / repository.

## Adding a Source

!> Warning - this will wipe out the remote repository. This will not matter if its brand new and has nothing in it, but if the remote repository has anything of value in it that you cannot lose, be _VERY_ careful with this as it will wipe it out!

It may be the case that you have a local Git repository that has valuable code and you wish to turn this into a remote repository. Its possible to take an _existing local_ repository and map it to a clean (i.e. you have nothing of note saved) remote source. To add a remote source to your local repository, [initialize a remote source](learn_to_code/git/git?id=setting-up-a-remote-repository) and get [the address](learn_to_code/git/git?id=setting-up-a-remote-repository) of the remote repository. Then - making sure you are in the directory of the local repository (the place where the `.git` directory is housed) - run the following:
```
git remote add origin https://YOUR_BITBUCKET_USERNAME@bitbucket.org/GROUP/repository.git
git push -u origin master
```
 * The above assumes its a Bitbucket remote repository.
   * `YOUR_BITBUCKET_USERNAME` is your username in Bitbucket.
   * `GROUP` is the group; if this is your personal repository this may be your username, but if it is your company's Group it will probably reflect your company's name.
   * `repository.git` is the repository; the base name will be different (it will probably match your local base directory for your local repository) but it will end in `.git`. 
 * This command expressly lists '[origin](learn_to_code/git/git_concepts?id=remote-name)' as the remote name; that said:
   * You can name it differently; unless you have a reason to, do not.
   * `origin` is the default, so you can run this without this keyword.
   * It is actually possible to have [multiple remote repositories](learn_to_code/git/git_concepts?id=remote-repository), which can be viewed with the [git remote](learn_to_code/git/git_cli_commands?id=viewing-sources) command.
     * If you have a reason to do this, simply swap out the word `origin` for whatever you want to call this remote source.
	 * Be careful with multiple remote sources.


## Changing a Source

It may be the case that you have a local repository that has an existing remote repository, but you wish to install this repository - and then link this repository - to a _different_ remote repository. This is very similar to [adding a source to a local repository](learn_to_code/git/git_cli_commands?id=adding-a-source), and shares some of the rules:
1. You must have an existing local repository.
2. The local repository must be linked to a (current) remote repository.
3. The (new) remote repository must exist.
4. You must have the web address of the (new) remote repository.
5. You must not care if any existing contents in the (new) remote repository are lost.

If the above is true, run these commands to swith remote repositories (and then push the contents of the local repository to the remote repository, wiping out the remote repository):
```
git remote set-url origin https://YOUR_BITBUCKET_USERNAME@bitbucket.org/GROUP/repository.git
git push -u origin master
```
 * The only difference in this and and add is that `add` becomes `set-url`.

!> Be careful with changing the remote source - once your local repository is linked to a different remote source, this technically becomes another project that is separate from the initial remote source. Your updates will no longer be valid for the initial remote source!

## Viewing Sources

To view sources, simply type:
```
git remote
```

This will show all source names (usually only [origin](learn_to_code/git/git_concepts?id=origin)). If you wish to see the actual web addresses as well, use `git remote -v`.

## Clean Up Remote Branches

Its possible that a branch was deleted remotely and [displaying remote branches](learn_to_code/git/git_cli_commands?id=displaying-all-remote-branches) still shows the branch as active; if this is the case, use the following command to re-scan the remote branch names:
```
git remote prune origin
```
* This assumes your [remote name](learn_to_code/git/git_concepts?id=remote-name) is `origin`.

---

# List Config

To list all config variables, type the following:
```
git config -l
```

---

# Pull

When utilizing a remote repository, it is required to occasionally check the remote repository for changes to remote branches (especially if you have local branches based off those remote branches). This is achieved by a [pull](learn_to_code/git/git_concepts?id=pull).

## Simple Pull

The following simply pulls updates for **all** local branches:
```
git pull
```

If you wish to pull updates for a specific branch, that command is:
```
git pull remote_name local_branch_name
```
* `remote_name` is the [remote name](learn_to_code/git/git_concepts?id=remote-name), which is usually `origin`.
* `local_branch_name` is the local branch name you are attempting to update.

> You can also check to see if your local branches are up to date by [displaying all branches wit hthe -v flag](git branch -v --all) (`git branch -v --all`). This will list hash IDs as well as all local and remote branches, and if the IDs match you are up-to-date. 

---

# Push

It will eventually become necessary to sync your local changes to the remote repository; this is where [push](learn_to_code/git/git_concepts?id=push) comes into play.

## Push Current Branch

To push the changes you have [committed](learn_to_code/git/git?id=committing-changes) to the local [branch](learn_to_code/git/git_concepts?id=branch), simply type:
```
git push
```
* This only pushes changes to the the current branch.
* Since your local branch is linked (tracked) to a remote branch, push update the remote branch.

If you wish to explicitly push a local branch, its:
```
git push local_branch_name
```

## Pushing All Altered Branches

!> I would advise pushing one branch at a time.

To push all branches you have changed locally, use the following command:
```
git push --all -u 
```
* `-u` (or `--set-upstream`) adds an upstream (remote) tracking reference

---



---