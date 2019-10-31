# <u>Git - Version control</u>

## Types of version control

---

### Central VCS (SVN)

This is located in one place, making changes to things then putting it back in the same place after making changes. *It's an issue if the person cannot get access to the repository*, so if offline etc you can only see the files you have checked out and not the most recent versions.

### Distributed VCS (GIT)

Everybody has a local repo. The local repo has all the info about the files within the repo from the last time you synced. This can be viewed as each developer has a backup of the whole system.

---

## Local (on your machine)

---

### Global configuration variables

#### Name and email

These values will be your credentials for all push/pull/commits on GIT. 

To define these:

```bash
git config --global user.name "<First Name> <Last Name>"
git config --global user.email "<Email address>"
```

#### Check the values

```bash
git config --list
```

### Basic useage

#### Initializing a repository

To track an existing code base by git (i.e if you have a directory you wish to start tracking with git):

```bash
git init
```

Which will initialize the directory, you will need to add all of the files you wish to be tracked manually (see below).

#### Stop tracking the directory

```bash
rm -rf .git
```

As the .git file contains all info about the files and their history.

#### Check status (your best friend)

```bash
git status
```

Will provide you with useful information regarding the current branch, tracked and untracked files, in a nutshell if you want to see any info about what's going on, do this command.

#### Staging file(s)
Running *git status* will show us files that are in the working directory, and whether they are staged or not. Files in the working directory can either be untracked (a new file which you did not _add_), or tracked/staged.  
The usual workflow is: create/modify a file > save the changes > add it to the staging area > when you wish to make a _checkpoint_ containing all changes to specific files do a git commit > give it a description so you can see it later. This will commit it locally, to push see the *remote* section.

The useful thing about staging files is if lots of files have been modified by you over the course of the day, the staging area can be used to push multiple commits instead of all the changed files at once.

```bash
git add -A 	# Add all files in dir to staging area
git add .project hello.py  # Add files singularly
```

**add -A** - *Will stage all files including new, deleted, modified and dot files in the entire working tree. This means any outside the working directory will also be staged if edited.*

**add -u** - *Adds all modified and deleted files for the whole tree, but not the untracked files (new files).*

**add .** - *Difference between this and -A we see that it will only stage files in the current->lowest branch of the working tree.*

**add * ** - *Not recommended due to the * being a shell command, so git may not interpret it correctly.*

#### Unstage file(s)
This is for files which have been added to the 'tracked' list, but you do not want them tracked anymore (they will not be committed).

```bash
git reset  # Remove all files from staging area
git reset .project  # Remove files singularly
```

#### Ignoring file(s)

Some files like preferences etc do not need to be pushed with the code, to ignore certain files they must be in the *.gitignore* file. First create such file in the directory:

```bash
touch .gitignore
```

Within the file, it is possible to ignore singular files, or even wild cards, meaning ignoring all files with a certain file type.

```bash
# Within the .gitignore file
.project  # Ignore singular file
*.py  # Ignore all .py files (wildcard)
```

Once this .gitignore is defined and saved, running the command *git status* again will show that they are now *invisible* to git and aren't picked up anymore.

#### Committing
If you have made changes to a file and wish to 'save' those changes, you must commit them. Use a meaningful message so if you need to refer back to this commit later, you know exactly what you did at a glance without the need to search through the code changes.

```bash
git commit -m "<Enter commit message>"  # -m is a commit message
```

#### Logs
Gives us a list of all commits from this repository, with their corresponding hash number, user and message.

```bash
git log
```

### Reverting changes
*These changes should only be made locally. This is due to these changes altering the .git history which can cause issues when editing a remote repository.*

#### Delete recent changes before commit
Will delete any changes made to this file since the last commit.

```bash
git checkout <filename>
```

#### Change most recent commit message 

```bash
git commit --amend -m "<new message>"
```

#### Show file changes in commit

```bash
git log --stat
```

#### Add file to previous commit

```bash
touch <filename>
git add <filename>
git commit --amend  # Puts created file to last commit
# Also brings up editor allowing for extra changes
```

#### Commit changes to alternate branch
_Branches are talked about later_.

```bash
git log
# Copy hash of the commit you want to copy (6 chars is enough)
git checkout <branch name>  # Branch to move commit to
git cherry-pick <copied hash number>
# Checkout to original branch
git log
# Copy hash of the commit before the branch you wish to delete
git reset --hard <most recent copied hash number>
```

*There are 3 types of reset: --soft, --mixed and --hard. Soft will not delete any changes before the reset point, and they will stay in the staging area. Mixed will also avoid deleting changes but they will be 'untracked' instead of 'staged*''. Finally hard will revert everything and lose any changes.*

#### Remove untracked files

```bash
git clean -df
```

*-d will remove any directories and -f any files.*

#### Retrieving 'lost' files

**This will only be allowed before 30 days or so from the deletion.**

```bash
git reflog  # Shows order of commits from when last referenced
# Copy commit hash value you wish to get back
git checkout <copied hash value>
git log  # You will see changes made in this commit
git branch <backup branch name>  # Create branch to save to

# To confirm the changes were saved
git checkout master
git branch  # Does the <backup branch name> exist?
git checkout <backup branch name>
git log  # Compare hash value of most recent to one copied earlier
```

---

## Remote (GitHub)

---

### Basic usage
Now this is the part everyone uses to collaborate. The directory on your computer that you are working from needs to be sent to a remote repository for others to see. You can either make the directory on your computer a new repository, or you can download an existing one from GitHub and store it locally.

#### Cloning
To make a local copy of a repository from GitHub, you must first 'clone' (download) it to your machine. This can be done easily on GitKraken. Once done you can pull/push and see all of the changes that have been made.

```bash
git clone <url>	 # The url must end with .git, so github or local
git clone <url> .  # Clones <url> files into current dir, no new dir
```

#### Fetching info

```bash
git remote -v  # Shows remote repo info
git branch -a  # Shows all branches of repo
```

#### See changes made to files

```bash
git diff
```

#### Pull changes

This command is important when working with other developers as it pulls the most recent commit from the remote to the local repository. **Make sure to commit your changes locally to your machine before doing a pull!!!**. Tip: If you have issues pulling (maybe a merge conflict or similar), use the *stash* command and save your most recent changes.. this way you can just pull normally and then copy across the changes which you would like to this new pull.

```bash
git pull origin master  # Origin is remote name, master is branch
```

#### Push changes

This pushes all changes which were committed to the remote repo. This will only push committed files. So if you have added the file, committed the changes and then push.. these files will be sent to the online repository. Files/changes you didn't commit will **not** be sent to GitHub.

```bash
git push origin master
```

### Reverting changes

These commands will not change the git history or cause an error when pushing to a remote repo. *New commits will be made on top of the existing ones which undo the unwanted changes*.

#### Undo particular commit

*If you wanted to undo a commit that may have been checked out by other people:*

```bash
git log
# Copy hash number
git revert <copied hash number>

git log
# Will tell you the commit has been reverted, yet the old one is still visible on the list below this

# To see changes between the two
git diff <first hash number> <second hash number>
```

---

## Common work-flow

---

The usual way to work with git on a day-to-day basis is to create branches for each feature you wish to implement.

### Branches
Branches are used to protect the _main_ part from being messed up. The master repository should contain the working code, and any modifications or features that wish to be added should be done on a new branch. This branch contains (at the start) a copy of the code base upon creation, then the code can be changed seperately to the master branch, and when finished it can be _merged_ back to the master. GitHub controls the merge back and will warn of any errors that may occur and how to fix them before merging.

#### Create

```bash
git branch <branch name>
```

#### List all branches

```bash
git branch -a  # The current working branch is highlighted in green
```

#### Change working branch

```bash
git checkout <branch name>
```

#### Committing

This has no effect on the main local branch or the remote repo.

```bash
git commit -m "<commit message>"
```

#### Pushing

```bash
git push -u origin <branch name>
```

*Here the -u represents an initial command which links the branch to the origin (in this case), meaning the next time you wish to push/pull to/from this branch you can simply type:*

```bash
git pull
git push
```

#### Merging

```bash
git checkout master  # Change working branch to master
git pull origin master
git branch --merged  # Will say the branches merged so far
git merge <branch name>  # Merge into master
git push origin master  # Push to master on remote repo
```

#### Deleting

```bash
git branch --merged  # Check if <branch name> merged successfully
git branch -d <branch name>  # Delete the branch locally
git branch -a  # Check if it still exists on remote repo
git push origin --delete <branch name>  # Delete from remote too
```

 ### Stashing

This is where you may be done with a particular part of work, but not yet ready to commit. You may need to switch branches but *save* your current work before you do, which is exactly what the stash command does, saving your progress in a temporary place so you can come back to it later. This temp place is a stack data structure. LIFO.

```bash
git stash save "Some save message"
# At this point the changes will dissapear from the status
git stash list  # List all stashes (stash@{1}, stash@{...}, etc)
```

#### Unstashing

```bash
git stash apply stash@{<stash no.>}
# Restores stash but keeps stash in stash list
```

```bash
git stash pop 
# Pops last stash from stash stack while restoring stash
```

#### Delete a stash

```bash
git stash drop stash@{<stash no.>}  # Removes from stash stack
```

```bash
git stash clear  # Deletes all stashes in stack
```

#### Transfer changes to different branch

A stash can be used across all branches, so if you stash a change, that stash can be applied/popped onto another branch without effecting the current.

```bash
git stash save  # On files you wish to transfer
git checkout <other branch>
git stash pop
git diff  # To check the changes
git add .
git commit -m "Some commit message"
```

---
