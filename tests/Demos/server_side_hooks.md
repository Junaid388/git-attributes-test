# Server-side Git Hooks

In this clip, we will build off of what we created in the last clip, but we will be creating Git hooks that will run on the remote.  In this case, we will be creating a `pre-receive` hook to validate the commit message format.

## Preparation

First, we need to clone the remote repository.  In this case, we want one version of the repository to act like a remote.  To make this happen, we need to check this out as a `bare` repository:
```
git clone --bare https://github.com/davidtucker/example-js-project.git example-git-remote
```

Next, we will clone from the bare repository that we just pulled down.  In this manner, we can simulate the local and remote all on our local machine.
```
git clone example-git-remote example-js-project-local
```

## Creating our First Server-side Hook

We will next change into our bare repository:
```
cd example-git-remote
```

We can see that this repository looks different from a normal repository.  It is like we are dropped directly into our `.git` repository:
```
ls
```

In this directory, we will see that we have a hooks directory.  We will create a new file and make sure it is executable:
```
touch hooks/pre-receive
```
```
chmod +x hooks/pre-receive
```

Next, we will edit the file and insert the following:
```
#!/bin/bash

set -e

# Modified version from Github platform samples

zero_commit='0000000000000000000000000000000000000000'
msg_regex='\[GSP\-[0-9]+\]'

while read -r oldrev newrev refname; do

	# Branch or tag got deleted, ignore the push
    [ "$newrev" = "$zero_commit" ] && continue

    # Calculate range for new branch/updated branch
    [ "$oldrev" = "$zero_commit" ] && range="$newrev" || range="$oldrev..$newrev"

	for commit in $(git rev-list "$range" --not --all); do
		if ! git log --max-count=1 --format=%B $commit | grep -iqE "$msg_regex"; then
			echo "ERROR:"
			echo "ERROR: Your push was rejected because the commit"
			echo "ERROR: $commit in ${refname#refs/heads/}"
			echo "ERROR: is missing the Globomantics JIRA Issue key 'GSP-123'."
			echo "ERROR:"
			echo "ERROR: Please fix the commit message and push again."
			echo "ERROR: https://help.github.com/en/articles/changing-a-commit-message"
			echo "ERROR"
			exit 1
		fi
	done

done
```

## Testing out the Server-side Hook

Next, we will navigate back to our cloned version of this repository.
```
cd ../example-js-project-local
```

From here, we will make a change to our `.gitignore`.
```
Edit .gitignore
```

Next, we will commit this:
```
git commit -am "Updating .gitignore"
```

When we push, we should see a rejection:
```
git push
```

We will need to add in a sample ticket number to the `GSP` project for our commit to be accepted:
```
git commit --amend
```

Now, we should be able to successfully push:
```
git push
```

This is only the beginning of the kinds of things that you can validate using server-side hooks.

## Additional Notes

If you are interested in further exploring Git server-side hooks or even setting up your own Git server, you can check out another Pluralsight course that I authored, [Git Administration](https://app.pluralsight.com/library/courses/git-administration). This course dives deeper into what can be done with server-side hooks and includes additional examples.

