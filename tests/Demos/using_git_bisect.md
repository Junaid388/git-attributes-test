# Using Git Bisect

Git biesct is a powerful tool that enables us to use either a manual or automated process to find the point at which a bug was injected into our code.

## Setup

First, we will be using the sample JavaScript repository that we leveraged in a previous module.  We first need to clone the repository:
```
git clone git@github.com:davidtucker/example-js-project.git
```

Next, we will change into the directory:
```
cd example-js-project
```

From here, we need to install project dependencies:
```
npm install
```

Next, we will checkout the `bisect` branch:
```
git fetch
```
```
git checkout bisect
```

## Discovering Bugs Manually

First, we want to run our `index.js` file:
```
./index.js 4
```

This seems to be correct, however, let's try it with a higher number:
```
./index.js 100
```

In this case, this seems to be off.  There should be 25 primes.  Let's verify with our tests:
```
npm test
```

Indeed the tests are failing.  At this point, we will use Git bisect to determine when a bad commit was injected into the process.  First, let's review the commits:
```
git log --pretty=oneline
```

Next, let's start Git bisect:
```
git bisect start
```

From here, we need to let Git bisect know the last time we confirmed that we had a good commit:
```
git bisect good cd93e60
```

Next, we need to let it know a commit that was certainly bad.  In this case, we'll use the latest (for the branch):
```
git bisect bad b7cd593
```

Now, Git bisect will guide us through testing the commits.  With each, we can run the script that failed previously. If we see the correct number of primes, we can say `git bisect good` and if not, `git bisect bad`.
```
./index.js 100
```

Once it determines the bad commit, we can use the following command to see what happened to our repository:
```
git show
```

Once we are ready to end Git bisect, we can run the following command:
```
git bisect reset
```

## Discovering Bugs Automatically

While this feature is useful, it would be even more useful if Git bisect could do the testing work itself.  Because we have a test suite for our project, it can do this work for us. We can start Git bisect just like before:
```
git bisect start
```

Then we can specify the good and bad commits:
```
git bisect good cd93e60
```
```
git bisect bad b7cd593
```

Instead of manually iterating through the commits, we can use the `git bisect run` command:
```
git bisect run npm test
```

Once we have gained the needed information, we can end our Git bisect session:
```
git bisect reset
```
