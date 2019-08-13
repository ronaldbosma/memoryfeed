---
tags: [ "Git" ]
---

# Git Cheatsheet

### List local/unpushed commits
`git log origin/master..HEAD`

### Clone repository with submodules
`git clone --recurse-submodules <repository>`

### Delete/prune local references to branches
`git remote prune origin`

### Cleanup unnecessary files and optimize the local repository
`git gc`