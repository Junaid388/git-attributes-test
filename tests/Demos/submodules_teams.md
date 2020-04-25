# Adding a Submodule to a Project

In this clip, we will deal with some real world scenarios that you will face when dealing with submodules on your projects.

## Nested Submodules

If you are using submodules on a project, it will be rare to not run across nested submodules (submodules within submodules).  If we follow the code we used in the previous clip, we would have to navigate to each submodule directory and both `init` and `update` it.  Fortunately, Git provides another way.

If you are leveraging a repository that leverages nested repositories, you can use the `--recursive` option:
```
git clone --recursive https://github.com/davidtucker/example-nested-submodule.git repo
cd repo
```

This is the equivalent of cloning, init'ing, and updating each submodule recursively.

If we navigate to this repository, we can see that each of the three submodules have been populated.  We can also see them in the `gitmodules` file:
```
cat .gitmodules
```

We can now use another command `git submodule foreach` to see the `.gitmodules` file for each of the submodules:
```
git submodule foreach 'cat .gitmodules'
```

## Working with Teams

Submodules can introduce challenges to a team workflow if specific guidelines are not followed.

### Pulling from a Repo with Submodules

First, we will need to pull code from the main module just as we normally would:
```
git pull
```

In some cases, our Git submodule configuration, found in `.gitmodules` can be changed by one of our team members.  For example, there could be the potential that a developer chose to fork a repository and use the URL for the forked repository instead of the previous one.  In these cases, you need to sync your configuration with what is contained in the `.gitmodules` file. 
```
git submodule sync --recursive
```

Next, you will need to update your submodules.  However, we should also remember that some repositories may be new since we just pulled down code from our remote.  In addition, this could happen in any of our submodules or any of their submodules.  We can handle all of these scenarios with the following command:
```
git submodule update --init --recursive
```

### Working within the Submodules

There are multiple problems that can occur when working with our submodules.  The primary challenge occurs when we push a reference to a commit for a submodule that we haven't yet pushed to the remote.  In this situation, we have pretty much broken our teammate's reference to the submodule.  
```
cd external/repo1/
git checkout master
git pull --rebase
echo "Editing a submodule" >> README.md
git commit -am "Updating the README"
cd ../../
git commit -am "Updating our submodule code"
```

If we pushed our code to the remote from here, it would cause the problem we referred to earlier.  Git now provides a config option (since Git 2.7) to prevent this from happening in many cases.  If you set `pull.recurseSubmodules` it will check to be sure that the first level of submodules is checked for any commits that need to be pushed when we push the primary repository. We'll go ahead and set that value here:
```
git config --global push.recurseSubmodules on-demand
```

Now, when we push our primary repository, we should see our submodule push too.
```
git push
```