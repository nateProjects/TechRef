# Git Essentials

## Basics

* Cloning - ``git clone repoaddress``
* Adding - ``git add *``
* Committing - ``git commit -m 'message'``
* Pushing - ``git push`` # git push origin main / master etc.
* Pulling - ``git pull``
* Status - ``git status`` / ``git log`` / ``git diff``

## Branch basics

* Create & change to new branch - ``git checkout -b name``
* Select existing branch - ``git branch name``
* Return to master branch - ``git checkout main`` # or master
* Change to branch - ``git checkout -b name``
* Delete branch - ``git checkout -d name``
* Push branch - ``git push name``
* Merge - ``git merge name``

### Move code to new git repo
(The location of the repo has changed)

```bash
git remote rename origin old-origin 
git remote add origin [git@URL:group/project.git]
(or git remote add origin [https://devomatic.aardman.com/group/project.git] 

Then push any changes -

git push -u origin --all 
git lfs push origin --all 
git push -u origin --tags
```

### Local changes out of sync with remote repo -

```bash
git stash # save local changes
git pull # get remote changes
git stash pop # add back local changes
# then add, commit & push as usual
```

### Undo local changes -
(This can also change remote if pushed back up)

note: git revert is good for undoing changes to a remote repo

```bash
git revert # undo to last commit
git revert ID # undo to specific previous commit / can see id with git log --oneline
```

note: git reset is good for when changes are made to the wrong branch

```bash
git reset # remove previous commit (if not pushed)
git reset --hard HEAD # move current branch backwards
git reset ID
git reset FILENM # do this for one file
git fetch origin; git reset --hard origin/master # replace all
```

### Git Sync

Clone Primary repo to local computer
Add remote for secondary repo
Get the commits and refs from the primary repo to your local one
Update the secondary repo using local refs

```bash
git clone --mirror https://primary_repo_url/primary_repo.git
cd primary_repo.git
git remote add --mirror=fetch secondary https://secondary_repo_url/secondary_repo.git
git fetch origin
git push secondary --all
```

The secondary repo will now have all the files and their revision history same as in the primary repo.

```bash
git fetch origin
git push secondary --all
# consider making an alias so primary & secondary are pushed at the same time
```

After this, whenever a commit is made on the primary repo, run the git fetch followed by git push commands to keep the two repos in sync.

### HTTPS Repo Access

```bash
# if using Github install github credential manager
# & select https authentication

git config credential.helper cache

git remote add origin-https https://github.com/Projects/WorkingOn.git
git push origin-https main # etc.
```
