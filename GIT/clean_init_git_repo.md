# cleaning traces of Git commits history and pushing back the repo to GitHUB

```bash
rm -fR .git
git init
git add .
git commit -m "Initial commit"
git remote add origin git@github.com:hpcmtint/keycloak.git
git push -u --force origin master
git log -G"awe.co.uk" --patch
```

or 

```bash
git checkout --orphan newBranch
git add -A  # Add all files and commit them
git commit
git branch -D master  # Deletes the master branch
git branch -m master  # Rename the current branch to master
git push -f origin master  # Force push master branch to github
git gc --aggressive --prune=all     # remove the old files
```
or

```bash
for BR in $(git branch); do   
  git checkout $BR
  git checkout --orphan ${BR}_temp
  git commit -m "Initial commit"
  git branch -D $BR
  git branch -m $BR
done;
git gc --aggressive --prune=all
```

