# Adding a Submodule to a Project

In this clip, we will verify that we can add a submodule, update our submodule, configure Git for submodules, and delete a submodule.

## Verifying Your Version of Git

Submodules have improved greatly by some changes made within Git.  There were several critical updates to Git related to submodules released in versions 1.7.5, 1.7.8, and 2.7.  To be sure you are on a recent version of Git, make sure that you are using at least Git v2.7.

```
git --version
```

## Adding a Repository

First, we will create a sample repository to use for this clip.

```
mkdir repo
cd repo
git init
echo "# Git Test Repository" >> README.md
git add .
git commit -m "Initial commit"
mkdir external
```

Next, we will add in an example submodule within our repository:
```
git submodule add https://github.com/davidtucker/example-submodule.git external/example-submodule
```

If we navigate to our submodule, we will see the contents of the repository included in the directory that we specified.
```
cd external/example-submodule
ls
cd ../..
```

We can also see the contents of the new file `.gitmodules` file. 
```
cat .gitmodules
```

## Git Configuration for Submodules

Next, we can see the current state of the repository by running `git status`:
```
git status
```

We can make a change to make this information even more useful for us when working with submodules:
```
git config --global status.submoduleSummary true
```

Now, we can see the updated information using `git status`:
```
git status
```

Next, we can commit to our repository:
```
git add .
```
```
git commit -m "Adding example submodule"
```

In addition, we can improve diffs when working with submodules:
```
git config --global diff.submodule log
```

## Cloning a Repository with a Submodule

Next, we will clone the repository we were just working with using the file protocol:
```
cd ..
```

```
git clone repo clone
```

Now, we can view the contents of our submodule within the cloned repository:
```
cd clone
ls
cd external/example-submodule
cd ../../
```

We will notice that our submodule is not yet populated.  We need to first run the following to get the info from the `.gitmodules` file into our repository config:
```
git submodule init
```

Next, we need to tell the submodule to grab the contents of its repository:
```
git submodule update
```

## Updating the Reference to Our Submodule

One of the benefits of using a submodule, is that you can target a specific commit of the submodule and ensure that you will work with that specific commit until you (or a member of your team) specifically reference another commit.

First, we need to `fetch` content for our repository:
```
cd external/example-submodule
git fetch
```

Then we can checkout the correct branch:
```
git checkout beta
```

Next, we can navigate back into our repository:
```
cd ../../
```

From here, we can see the repository status:
```
git status
```

Next, we can commit this change:
```
git commit -am "Updating submodule to beta"
```

Then we need to run submodule update:
```
git submodule update
```

## Removing a Submodule

Next, we can permanently remove a submodule from our project. First, we want to `deinit` the module, which will remove it from our repository config:

```
git submodule deinit external/example-submodule
```

Next, we are ready to permanently delete the reference to the submodule:
```
git rm external/example-submodule
```

Next, we can see the status for our repository:
```
git status
```

Now, we can commit the removal:
```
git commit -am "Removing submodule"
```
