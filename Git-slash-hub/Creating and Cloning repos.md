## Creating a repo locally

here is what github instructs you to do upon creating a new repository:
```bash
echo "# obsidian-vault" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/kcjesse02/obsidian-vault.git
git push -u origin main
```
Here is what each part of this does:
0: make sure you are in the directory you want to make a github repo
1. Creates a readme in the desired folder
2. Initializes a repo. creates a `.git` sub-directory, which is where all the version control info will be stored. 
3. stages the readme. If you are creating a repo in an existing folder with stuff in it, you can do `git add *` to add everything in the directory.
4. commits what was staged
5. establishes the base branch as being named main! Make sure to do this. Else git won't know where to put the code when you tell it to push!
6. connects the local git repository with the remote github repository. Github will always supply you with a link for an empty repo.
7. puts code to main branch at the remote location of the repo. the `-u` sets the upsteam branch, which is basically like the name of the remote location, which it sets to origin because that's the default.