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


### Username and email

#### List and set username and email

With `git config --list` the full config can be listed.

With the following commands you can list and set the user.name and user.email for a repository.

```
git config user.name
git config user.email

git config user.name "Your Name"
git config user.email "email@example.com"
```

#### Set global email address
`git config --global user.email "email@example.com"`

#### Set email for all repositories in a folder

In some cases, you might want to overwrite the local git config for all repositories in a folder. 
The following PowerShell script will set the email address for all git repositories in the current directory.

```pwsh
$email = "<your-email>"
$folders = Get-ChildItem -Path . -Directory

foreach ($folder in $folders)
{
    Push-Location $folder
    try
    {
        if (Test-Path -Path ".git") 
        {
            Write-Host "Set git email to $email in $folder"
             git config --local user.email $email
        }
    }
    finally
    {
        Pop-Location
    }
}
```