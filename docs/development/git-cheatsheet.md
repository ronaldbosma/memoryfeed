# Git Cheatsheet

### List local/unpushed commits
`git log origin/master..HEAD`

### Undo uncommited local changes

#### Stash
`git stash`

#### Discarding local changes (permanently) to a file
`git checkout -- <file>`

#### Undo changes permanently
`git reset --hard`

### Undo commits

#### Soft undo
Last commit is reverted and changes are kept as uncommited:  
`git reset --soft HEAD~1`

#### Hard undo
Last commit is reverted and changes are lost:  
`git reset --hard HEAD~1`

### Copy commits to another branch (cherry pick)
`git cherry-pick <commit id>`

### Git clone repository with Personal Access Token

When cloning `https://gitlab.com/username/myrepo.git` with a token, use:  
`git clone https://<token-name>:<token>@gitlab.com/username/myrepo.git`

### Submodules

#### Clone repository with submodules
`git clone --recurse-submodules <repository>`

#### Update submodules
`git submodule update --remote --merge`

### Delete/prune local references to branches
`git remote prune origin`

### Remove untracked files
Use command without `--dry-run` to actually remove the untracked files  
`git clean -d -x -f --dry-run`

### Cleanup unnecessary files and optimize the local repository
`git gc`

### Set email address
`git config --global user.email "email@example.com"`