# Utilizing Attributes in Git

In this clip, we will leverage Git attributes to configure how specific files are handled within a repository. 

## Prerequisites

We will be using the `exiftool` command line utility.  If you are using a Mac with Homebrew, this can be installed with `brew install exiftool`. For other platforms or setups, you can visit the [Exiftool site](https://exiftool.org/).

In addition, we will be using a new remote repository.  For this demo, I would recommend using [Github](https://github.com) since the specific functionality we will be using the remote repository for is not supported equally across all Git hosts.

## Handling Image Diffs

First, we will begin by creating a new repository to work in:
```
mkdir repo
cd repo
git init
```

Next, we will add in a `README.md` and `index.js` file:
```
echo "# Git Attributes Test Repository" >> README.md
echo "// Sample JS File" >> index.js
```

Now, we will create our `.gitattributes` file:
```
echo "# Git Attributes" >> .gitattributes
```

Next we will configure our JPG files to leverage `exiftool` for diff's.  To accomplish this, we will add the following line to our `.gitattributes` file:
```
*.jpg diff=exif
```

Next, we will need to define a diff driver for the `exiftool`.  To do this, we will need to update our `gitconfig`.  In this case, we will define it globally, so our user can use it for any repository:
```
git config --global diff.exif.textconv exiftool
``` 

We'll add this to the repository:
```
git add .
git commit -m "Initial commit"
```

Now we can add in an image to our repository. Copy the file `1.jpg` from the `original_image` directory in the exercise files.  Next commit this file:
```
git add 1.jpg
git commit -m "Adding original image file"
```

Next, copy the file from the `modified_image` directory to overwrite the original file.  Once this has been done, you should be able to see the `exiftool` in action by entering the following:
```
git diff
```

## Excluding Files from Generated Output

Next, we will be leveraging the ability that some Git hosts provide to leverage Git attributes to determine what is included in the generated output of a repository.

First, copy the `tests` folder from the exercise files into the repository.  Add those files and commit.  Also, add in your origin and push the master branch to origin:
```
git add tests
git commit -m "Adding tests directory"
git remote add origin git@github.com:<YOUR REPO URL>
git push -u origin master
```

Now, we'll create a tag for this version:
```
git tag 0.0.1
git push origin 0.0.1
```

Next, we will navigate to Github to see our release.  If we navigate to the releaes section, we will see that the zip for the release includes all files in the repository. Next we will update the `.gitattributes` file with the following:
```
.*				export-ignore
tests			export-ignore
```

Now we will commit and create a new tag:
```
git add .gitattributes
git commit -m "Adding export-ignore attribute"
git tag 0.0.2
git push origin 0.0.2
```

When we navigate to releases now, we should see the zip file contain only the files we want it to contain.

## Using Filters

Next, we will define a clean and smudge filter for our repository that will enable us to keep a secret key locally and not in our remote repository. 

First, we'll paste the following code into our `index.js` file:
```
const API_KEY = "a1693afecca123";

const makeAPICall = function(key) {
    console.log("Making API Call.....");
}

makeAPICall(API_KEY);
```

Then we'll add in the following two config values for our local repository.  This leverages [sed](https://www.geeksforgeeks.org/sed-command-in-linux-unix-with-examples/), a command line tool that transform text.  In this case, we will simply be leveraging it to find and replace a specific string.  
```
git config --local filter.updateAPIKey.smudge 'sed "s/{SECURE_API_KEY}/a1693afecca123/"'
git config --local filter.updateAPIKey.clean 'sed "s/a1693afecca123/{SECURE_API_KEY}/"'
```

Next, we'll paste the following code into our `.gitattributes` file:
```
*.js			filter=updateAPIKey
```

Now, we'll commit our `.gitattributes` file:
```
git add .gitattributes
git commit -m "Adding filter to attributes"
```

Then we'll add in our `index.js` file:
```
git add index.js
git commit -m "Adding updated index.js file"
git push origin master
````

We can navigate now to Github and see that our `index.js` file was updated to not include our key but only a placeholder.