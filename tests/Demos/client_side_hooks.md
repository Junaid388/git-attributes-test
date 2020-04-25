# Using Client-side Git Hooks

## Prerequisites

Please complete the following steps to make sure that you can complete the work in this clip:

### Verifying Version of Git

We will be leveraging a config value for Git hooks that was introduced in Git 2.9.  Make sure that you are using at least that version for this clip.
```
git --version
```

### Downloading the Sample Project

There is a sample Github repository which includes a project we can use to test out our client-side Git hooks.  We can check it out:
```
git clone https://github.com/davidtucker/example-js-project.git
```

### Installing Needed Modules

Next, we will need to install the needed modules for this clip.  To do this, you will need to have both `node` and `npm` installed on your machine.  If you haven't done this, you can see [How to Install Node and NPM](https://nodejs.org/en/download/).  Once you have both, you can run the following command in the root of the sample project:
```
npm install
```

## Reviewing the Sample Project

Our sample project includes a utility that we can use to calculate the prime numbers up to a max number that we pass in.  It is important to note that this is just a sample, and this isn't the most efficient way to calculate large prime numbers.

First, make sure that the index file is executable (it should be already - but just to confirm):
```
chmod +x index.js
```

Now, we can run the utility.  This will find all of the primes up to 100.
```
./index.js 100
```

We have a few tests included.  We can run those using the following command:
```
npm test
```

We also have linting configured using `eslint`:
```
npm run lint
```

If you are using an editor, like VS Code, you may need to un-hide the `.git` directory.  For VS Code, you can search for `exclude`.  In the excluded files, you can remove `**/.git`.
```
Remove **/*.git
```

## Creating our First Git Hook

We want our code to be linted and our tests run prior to each local commit.  To make this work, we will add a `pre-commit` hook. If we open the `.git/hooks` directory, we will see `pre-commit.sample`. We need to rename this file to `pre-commit` (remove the `.sample`). 
```
Rename to pre-commit
```

Next, we can open this file. We will replace the content with the following:
```
#!/usr/bin/env bash

# Exit with non-zero status if any of the following scripts fail
set -e

# Run eslint on project
npm run lint

# Run mocha tests
npm test
```

Now, we will go and add some linting errors to our `index.js` file:
```
Adjust tabs on index.js
```

If we try to commit, we should see it block our commit with the feedback from the linting error (via `eslint`):
```
git commit -am "Trying to add linting errors"
```

Let's now fix our linting errors.
```
git reset --hard
```

Next, we will update our `lib/findPrimes.js` file to return an incorrect answer:
```
Return a fixed array in findPrimes.js
```

Now, if we try to commit, we should see our tests fail:
```
git commit -am "Trying to add test errors"
```

Let's reset the error we injected:
```
git reset --hard
```

## Created Shared Client-side Hooks

While we have implemented our hook, it is only available to us as it is in our local repository.  Luckily in Git 2.9, there was a config value added that allows us to have a specific directory within the repository (and not the repository configuration) for Git hooks:
```
git config --local core.hooksPath .githooks
```

Now we can create a directory in our repository for our hooks:
```
mkdir .githooks
```

Now, we can move our `pre-commit` hook into the repository:
```
mv .git/hooks/pre-commit .githooks/
```

It should executable, but just confirm:
```
chmod +x .githooks/pre-commit
```

Now we can add in a linting error into `index.js`.  When we try to commit, we will be prevented by our Git hook.  However, in this case, this file can be checked into the repository and made available to the remainder of the team:
```
git commit -am "Trying to add linting errors"
```
