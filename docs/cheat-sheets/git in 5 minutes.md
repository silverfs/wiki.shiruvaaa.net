
![[git-logo.png]]
<br>

# Git in 5 Minutes

Many people consider Git to be too confusing or complex to be a choice for version control. Yet Git considers to grow in adoption, and many interesting things have grown up around it. This document is geared for someone wanted to get started with Git, often coming from a Subversion background. For most basic needs this document will cover 70 to 90 percent of your use.
<br>
## Getting Started
To use Git you will have to setup a repository. You can take an existing directory to make a Git repository, or create an empty directory.

To make your current directory a Git repository we simply run init.

```bash
git init
```
To make a new directory that is a Git repository we just specify a directory.

```bash
git init newrepo
```
From here on out we'll assume that you are in the root of the Git repository unless otherwise noted.
<br>

## Adding New Files
So we have a repository, but nothing in it. You can add files with the add command.

```bash
git add filename
```
To add everything in your directory try git add ..

## Committing a Version
Now that we've added these files, we want them to actually be stored in the Git repository. We do this by committing them to the repository.

```bash
git commit -m "Adding files"
```
If you leave off the -m you will be thrown into an editor to write the message yourself, if supported.
<br>
In case you you want to `undo` the commit and change nothing more, you can use:
```bash
git reset --mixed HEAD~;
```
`mixed` will `reset` the `index` but not the working tree. The changed files are preserved but not marked for commit. It reports back what has not been updated. This is a default action.

<br>

## Editing Files
When you've made changes to some files, you can run git status to see what will happen on commit. You'll notice a list of modified files, and a message:

```bash
no changes added to commit (use "git add" and/or "git commit -a")
```
So running git commit will do nothing unless you explicitly add files to the commit with git add. If you're looking for the commit command to automatically commit local modifications we use the -a flag.

```bash
git commit -a -m "Changed some files"
```
Or if you'd like to have only certain files, but still not run git add we pass specific files.

```bash
git commit -m "change some files" file1 file2
```
Do note that -a will not cause new files to be committed, only modified.
<br>
## Publishing Your Repository
To put your repository on a server we'll start by making a "bare" repository, and upload it to a server.

```bash
cd /tmp
git clone --bare ~/your/repo/path project.git
scp -r project.git ssh://example.com/~/www/
```
Now if we have a couple of commits and want to push it up to that location:

```bash
git push ssh://example.com/~/www/project.git
```
If you dislike typing the URI each time we can take advantage that a cloned project remembers where it came from.

```bash
cd ..
git clone ssh://example.com/~/www/project.git project
```
Now git push will push to the URI it was cloned from. You can do this manually by editing .git/config in your repository.
<br>
## Get Upstream Changes
If you're already setup for push as above:

```bash
git pull
```
Will bring changes down and merge them in. To pull from a non-default location just specify the URI.

```bash
git pull http://git.example.com/project.git
```

> Git remote -v shows what repository you are in to at the moment.
{.is-info}

<br>
<br>

## More Than Five Minutes

Everything that didn't fit in 5 minutes 😅
<br>

## Commits
You'll have noticed that Git thinks in "commits." These are uniquely identified by a hash. You can see the history and the hashes with git log. Each commit involves modifications, new files, and files being removed. add will put a file in a commit. git reset HEAD will remove everything from the planned commit, but not change files.
<br>
## Remove
If you want to remove a file from the repository, removing it from future commits we use rm.

```bash
git rm file
```
<br>

## Branching and Merging
Branches are done locally and are fast. To create a new branch we use the branch command.

```bash
git branch test
```
the branch command does not move us into the branch, just create one. So we use the checkout command to change branches.

```bash
git checkout test
```
The first branch, or main branch, is called "master."

```bash
git checkout master
```
While in your branch you can commit changes that will not be reflected in the master branch. When you're done, or want to push changes to master, switch back to master and use merge.

```bash
git checkout master
git merge test
```
And if you're done with the branch you can delete with the branch command and pass the -d flag.

```bash
git branch -d test
```
<br>

## Traveling Through Time
You can quickly very previous states of the repository using the checkout command again.

```bash
git checkout HASH
```
Uncommited changes will travel with you. Return to the preset with git checkout master as with normal branches. If you commit while in the past a branch is automatically created and your changes will have to be merged forward.
<br>

## Sweeping Changes Under the Rug for Later
When moving between branches your local changes move with you. Sometimes you want to switch branches but not commit or take those changes with you. The Git command stash lets you put changes into a safe store.

```bash
git stash
```
You can retreive by passing an arguement of apply or pop.

```bash
git stash apply
```
The difference between apply and pop is simple. apply will take a stash state and apply it, but preserve that state in the stash. pop will take the stash state, apply it, and remove it from the stash. git stash clear empties the contents of the stash.



<br>
<br>
_______________________________

This document is mostly directly copied from [this website](https://classic.scottr.org/presentations/git-in-5-minutes/). I do not own the original texts, this is simply a good explanation handguide that I use myself and comes in handy at any time.