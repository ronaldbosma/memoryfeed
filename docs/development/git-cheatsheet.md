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

### Submodules

#### Clone repository with submodules
`git clone --recurse-submodules <repository>`

#### Update submodules
`git submodule update --remote --merge`

### Delete/prune local references to branches
`git remote prune origin`

### Cleanup unnecessary files and optimize the local repository
`git gc`

### Set email address
`git config --global user.email "email@example.com"`