# Git Config

In this clip, we will be working with Git config. We will be implementing common modifications to the config that you would perform when setting up a new installation of Git.

## Setting Global Identity

First, since we are starting with a new Git install, we need to set our global identity settings.  We will configure per-repository settings later this this clip.

```
git config --global user.name "David Tucker"
git config --global user.email david@globomantics.com
```

## Setting up An Initial Repository

Next, we will setup an initial repository:

```
mkdir repo
cd repo
git init
echo "# Sample Git Repo" >> README.md
git add .
git commit -m "Initial Commit"
```

## Reviewing the Current Config

From within our sample repository, we will run the following command to view all configuration:

```
git config --list --show-origin
```

This should output the entire config chain from the current repository, to user global settings, to settings for the Git installation. 

## Configuring Multiple Identities 

There are many cases where you will need to use a different identity for a separate repository (for example work versus personal development repo).  In these cases, you can just override your global config values with local ones for identity:

```
git config --local user.name "David Tucker"
git config --local user.email david@davidtucker.net
```

## Setting Standard Config Values

The next step will involve setting common config values to adjust the default functionality of Git.

### Setting the Default Editor

First we'll be updating the default editor for Git to be [VS Code](https://code.visualstudio.com/).  You could just as easily utilize other editors like vim, emacs, Sublime Text, etc...

To set VS Code as the editor, enter the following config command: 

```
git config --global core.editor "code --new-window --wait"
```

You can verify this command, by editing the config file using the following command:
```
git config --global -e
```

### Setting the Default Diff Viewer

From the open config file, we can configure VS Code to also serve as the diff tool by adding the following:

```
[diff]
    tool = vscode-diff
[difftool "vscode-diff"]
    cmd = code --wait --diff $LOCAL $REMOTE
```

Next, we will set one more value:

```
git config --global --add difftool.prompt false
```

Now, we can test out our diff tool:

```
echo "Sample content" >> README.md
git difftool
```

### Setting the Merge Viewer

Next, we can update our merge tool to be VS Code as well.  We will need to edit our global config to include the following:

```
[merge] 
	tool = vscode-merge
[mergetool]
	keepBackup = false
[mergetool "vscode-merge"]
	cmd = code --wait $MERGED
```

Once this is in place, we can create a merge conflict:

```
git checkout -b develop
echo "More content." >> README.md
git add .
git commit -m "Updating README.md"
git checkout master
echo "Other content." >> README.md
git add .
git commit -m "Adding other content"
git merge develop
git mergetool
```

We can accept one of the changes in VS Code, and then commit our merge:
```
git commit
```

### Setting Aliases

Git allows you to define aliases for common commands.  In this way, you can reduce the amount of characters you will need to type for things you use regularly.

First we will add an alias for `git status`:

```
git config --global alias.st status
git st
```

Next, we will define another alias for `git status --short`.  You will see that we are reusing the alias in our new definition:

```
git config --global alias.ss 'st --short'
```

Next, we can add in an alias for editing our global config:
```
git config --global alias.egc 'config --global -e'
git egc
```

From within our global config, we can add some additional aliases:

```
[alias]
	st = status
	ss = st --short
	egc = config --global -e
	elc = config --local -e
	esc = config --system -e
	co = checkout
	br = branch
	ci = commit
	amend = co -a --amend
```

We can now save this file.

*We will not be using these aliases in this course (just in case someone drop into the course without seeing this clip), however, when you use these on a regular basis they will become indispensable.*
