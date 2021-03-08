# Git - Configure WinMerge as diff and merge tool

Add the following to the .gitconfig in your user folder: `%userprofile%\.gitconfig`.

```
[diff]
	tool = winmerge
[difftool "winmerge"]
	cmd = \"C:\\Program Files\\WinMerge\\WinMergeU.exe\" -e \"$LOCAL\" \"$REMOTE\"
[merge]
	tool = winmerge
[mergetool "winmerge"]
	cmd = \"C:\\Program Files\\WinMerge\\WinMergeU.exe\" \"$MERGED\"
```